# ミドルウェア

[非同期なAction](../advanced/AsyncActions.md)の例で、Action内にあるミドルウェアを見てきました。もし[Express](http://expressjs.com/)や[Koa](http://koajs.com/)のようなサーバーサイドのライブラリを使ったことがあれば、すでに *ミドルウェア* のコンセプトにも慣れているかもしれません。これらのフレームワークでミドルウェアは、フレームワークがリクエストを受け取り、そしてレスポンスを生成する間に何らかのコードを加えます。例えばExpressやKoaのミドルウェアは、CORSヘッダーやログ取得、圧縮などを加えます。ミドルウェアの最大の特徴は、チェーンでつなげることです。 つまり1つのプロジェクトで、独立したサードパーティによる複数のミドルウェアが使えます。

Reduxのミドルウェアは、ExpressやKoaのミドルウェアとは異なる問題を解決します。しかし、コンセプトは同様です。**ActionがDispatch（送信）され、Reducerへたどり着くまでの間に、サードパーティによる拡張ポイントを用意します。** Reduxのミドルウェアはログ取得やクラッシュレポート、非同期なAPIとの交信、ルーティングなどに使われます。

この記事は、大きく2つのセクションで構成されています。1つは、コンセプトの理解に役立つ導入部です。もう1つは、ミドルウェアの力を示すための[実践的な使用例](#7つの使用例)です。使用例は最後に記載しています。この2つは、対応する部分を交互に読むと良いかもしれません。退屈とひらめきを、行き来するように。

## ミドルウェアを理解する

ミドルウェアは、API呼び出しを含めいろんな用途に使えます。そんなミドルウェアは、どうやって作られているのでしょうか？　これを理解するのが大切です。そこで、ミドルウェアへとつながる思考プロセスを案内します。例として、ログ取得とクラッシュレポートを使います。

### 問題： ログ取得

Reduxを使う利点の1つ。それは状態変化を予測できるようにし、その透明性を高めることです。ActionがDispatchされるたびに、新しい状態が計算・保持されます。状態が自分自身を変更することはできません。特定のActionによる結果としてのみ、変更できます。

アプリで起きるすべてのActionと、そのActionによって計算された次の状態。この2つのログを取れたら良いですね。何か問題が起きたら、ログを確認できます。そしてどのActionが状態を壊したのか、把握できるのです。

<img src='http://i.imgur.com/BjGBlES.png' width='70%'>

Reduxでどう対処するか？

### 試行 #1: 手作業でログを取る

最も単純な解決策は、[`store.dispatch(action)`](../api/Store.md#dispatch)を呼び出すたびに自分でActionと次の状態のログを取ることです。実際のところ、これは解決策ではありません。しかし、問題を理解するための第一歩です。

>##### 注意

>もし[react-redux](https://github.com/reactjs/react-redux)や同様のバインディング（連携プログラム）を使っているなら、コンポーネント内でStoreインスタンスに直接アクセスすることはできないでしょう。次の段落は、Storeを明示的に渡したという想定です。

Todoを作成するとき、これを呼び出します：

```js
store.dispatch(addTodo('Use Redux'))
```

Actionと状態のログを取るには、このように変えます：

```js
let action = addTodo('Use Redux')

console.log('Dispatching', action)
store.dispatch(action)
console.log('次の状態', store.getState())
```

これで望んだ結果が得られます。しかし、毎回これを書きたくはないでしょう。

### 試行 #2: Dispatchをラップ（内包）する

関数内でログを取れます：

```js
function dispatchAndLog(store, action) {
  console.log('Dispatching', action)
  store.dispatch(action)
  console.log('次の状態', store.getState())
}
```

`store.dispatch()`の代わりに、この関数をどこでも使えます：

```js
dispatchAndLog(store, addTodo('Use Redux'))
```

ここで終わっても良いのですが、いつも特別な関数をインポートするのは、あまり便利じゃありません。

### 試行 #3: Dispatchを、置き換える（モンキーパッチング）

ただ単に、Storeインスタンスの`dispatch`関数を置き換えるのはどうでしょう？　ReduxのStoreは、[いくつかのメソッド](../api/Store.md)を持つ普通のObjectです。それに、書いているのはJavaScriptです。ということは、`dispatch`の実装を置き換えることができます：

```js
let next = store.dispatch
store.dispatch = function dispatchAndLog(action) {
  console.log('Dispatching', action)
  let result = next(action)
  console.log('次の状態', store.getState())
  return result
}
```

やりたいことに、かなり近づきました！　ActionをどこでDispatchしても、ログの取得が保証されます。置き換えるのが正しい事だとは、まったく思えません。しかし今は、これでやっていけます。

### 問題： クラッシュレポート

もし、`dispatch`に上記のような変形を **複数** 加えたいときは？

先ほどとは別の、有効な変形を思いつきました。本番環境で、JavaScriptのエラーをレポートするのです。グローバルの `window.onerror`イベントは頼れません。なぜなら、古いブラウザのいくつかではスタック情報を提供していないからです。これは、なぜエラーが起きるのかを理解する上で致命的です。

次のようにできたら、役に立つと思いませんか？　まず、ActionをDispatchした結果としてエラーがいつも投げられます。そしてこのエラーを[Sentry](https://getsentry.com/welcome/)のようなクラッシュレポートのサービスに送ります。エラーだけでなくスタックトレース、エラーを起こしたAction、現在の状態も一緒に送ります。こうすると、開発環境でエラーの再現がより簡単になります。

しかし、ログ取得とクラッシュレポートを分けておくことが大切です。理想的には、この2つを別のモジュールにしたいです。別のパッケージとする可能性もあります。そうしないと、求めているようなユーティリティのエコシステムは得られません。（ヒント：ミドルウェアって何なのか、ゆっくりと分かり始めています！）

もしログ取得とクラッシュレポートが別々のユーティリティなら、下記のようになるでしょう：

```js
function patchStoreToAddLogging(store) {
  let next = store.dispatch
  store.dispatch = function dispatchAndLog(action) {
    console.log('Dispatching', action)
    let result = next(action)
    console.log('次の状態', store.getState())
    return result
  }
}

function patchStoreToAddCrashReporting(store) {
  let next = store.dispatch
  store.dispatch = function dispatchAndReportErrors(action) {
    try {
      return next(action)
    } catch (err) {
      console.error('例外をキャッチした！', err)
      Raven.captureException(err, {
        extra: {
          action,
          state: store.getState()
        }
      })
      throw err
    }
  }
}
```

これらの関数を別々のモジュールとして公開すれば、あとで使えます。具体的には、Storeにパッチとしてあてることができます：

```js
patchStoreToAddLogging(store)
patchStoreToAddCrashReporting(store)
```

まだ、改善の余地があります。

### 試行 #4: 置き換えを隠す

置き換えはハック（改変）です。“どんなメソッドでも、好きなように置き換える”ということです。それってつまり、どんなAPIでしょう？　その本質を理解しましょう。先ほど`store.dispatch`を、自作の関数で置き換えました。この関数の代わりに、新しい`dispatch`関数を *返したら* どうでしょう？

```js
function logger(store) {
  let next = store.dispatch

  // さっきやったのは:
  // store.dispatch = function dispatchAndLog(action) {

  return function dispatchAndLog(action) {
    console.log('Dispatching', action)
    let result = next(action)
    console.log('次の状態', store.getState())
    return result
  }
}
```

Reduxの内部に、ヘルパーを提供できます。このヘルパーが、実際の置き換えとして使われます。下記が実装の詳細です：

```js
function applyMiddlewareByMonkeypatching(store, middlewares) {
  middlewares = middlewares.slice()
  middlewares.reverse()

  // それぞれのミドルウェアで、Dispatch関数を変形する
  middlewares.forEach(middleware =>
    store.dispatch = middleware(store)
  )
}
```

ヘルパーで複数のミドルウェアを追加できます。下記のように：

```js
applyMiddlewareByMonkeypatching(store, [logger, crashReporter])
```

しかしまだ、置き換えています。
置き換えという事実をライブラリの中に隠しても、この事実は変わりません。

### 試行 #5: 置き換えを取り除く

そもそも、なぜ`dispatch`を上書きするんでしたっけ？　もちろん、`dispatch`をあとで呼び出すためです。しかし、もう1つ理由があります：それはすべてのミドルウェアが、前にラップされた`store.dispatch`へアクセス（呼び出しも）できるようにするためです：

```js
function logger(store) {
  // 前のミドルウェアが返したdispatch関数を、参照しなければいけない：
  let next = store.dispatch

  return function dispatchAndLog(action) {
    console.log('Dispatching', action)
    let result = next(action)
    console.log('次の状態', store.getState())
    return result
  }
}
```

ミドルウェアを、チェーンでつなげるために必要なのです！

もし`applyMiddlewareByMonkeypatching`が、最初のミドルウェアを処理した直後に`store.dispatch`を割り当てなければ、`store.dispatch`は元の`dispatch`関数をずっと参照し続けるでしょう。そして2番目のミドルウェアもまた、元の`dispatch`関数にバインド（束縛）されます。

しかし、チェーンでつなげるようにする方法は他にもあります。この方法だと、ミドルウェアは`next()`というDispatch関数を受け取れます。この`next()`が、`store`インスタンスの`dispatch`関数を読み込む代わりになります。

```js
function logger(store) {
  return function wrapDispatchToAddLogging(next) {
    return function dispatchAndLog(action) {
      console.log('Dispatching', action)
      let result = next(action)
      console.log('次の状態', store.getState())
      return result
    }
  }
}
```

[“もっと深く進まなければならない”](http://knowyourmeme.com/memes/we-need-to-go-deeper)ときが来ました。理解するのに、しばらく時間がかかるかもしれません。上記の連続する関数には、威圧感があります。ES6のアロー関数で、この[カリー化（currying）](https://en.wikipedia.org/wiki/Currying)をもっと見やすくしましょう：

```js
const logger = store => next => action => {
  console.log('Dispatching', action)
  let result = next(action)
  console.log('次の状態', store.getState())
  return result
}

const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('例外をキャッチした！', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}
```

**これはまさに、Reduxのミドルウェアと同様です。**

ミドルウェアが、`next()`としてDispatch関数を引数に取り、またDispatch関数を返すようになりました。ミドルウェアが返したDispatch関数は、`next()`として左のミドルウェアへ順番に渡されます。この繰り返しです。 この方法でも、`getState()`のようなStoreメソッドへのアクセスを保っておくと役立ちます。そのため`store`は、これまで通り最上位の引数として利用できます。

### 試行 #6: ミドルウェアを単純に加える

`applyMiddlewareByMonkeypatching()`の代わりとして、下記のように`applyMiddleware()`が書けます。この関数はまず、完全にラップされた`dispatch()`関数を得ます。そして、この`dispatch()`関数を使ったStoreのコピーを返します：

```js
// 警告: 単純すぎる実装です！
// これはRedux APIでは *ありません*。
function applyMiddleware(store, middlewares) {
  middlewares = middlewares.slice()
  middlewares.reverse()
  let dispatch = store.dispatch
  middlewares.forEach(middleware =>
    dispatch = middleware(store)(dispatch)
  )
  return Object.assign({}, store, { dispatch })
}
```

これはReduxに含まれている[`applyMiddleware()`](../api/applyMiddleware.md)の実装と同様です。しかし、**3つの重要な面で異なります**：

* ミドルウェアに対して、[Store API](../api/Store.md)の一部だけを公開しています：具体的には、 [`dispatch(action)`](../api/Store.md#dispatch)と[`getState()`](../api/Store.md#getState)です。

* ミドルウェアから`next(action)`の代わりに`store.dispatch(action)`を呼び出すと、少しトリッキーな動きをします。このときActionは、ミドルウェアのチェーン全体を再び伝わります。呼び出したミドルウェアにも、Actionが再び伝わってきます。[以前](AsyncActions.md)確認したように、これは非同期なミドルウェアにとって有効です。

* ミドルウェアを一度しか追加できないようにするため、`store`自体ではなく`createStore()`で操作します。`(store, middlewares) => store`の代わりに、引数を`(...middlewares) => (createStore) => createStore`という形で渡します。

`createStore()`を使う前に、`createStore()`へ関数を渡すというのは扱いづらいです。そのため`createStore()`は、最後の引数として関数を受け取ります。この引数は、任意（省略可能）です。

### 最後の対処法

上記で書いたミドルウェアを考慮すると：

```js
const logger = store => next => action => {
  console.log('Dispatching', action)
  let result = next(action)
  console.log('次の状態', store.getState())
  return result
}

const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('例外をキャッチした！', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}
```

下記は、Redux Storeにミドルウェアを加える方法です：

```js
import { createStore, combineReducers, applyMiddleware } from 'redux'

let todoApp = combineReducers(reducers)
let store = createStore(
  todoApp,
  // applyMiddleware()は、createStore()にミドルウェアの処理方法を伝える
  applyMiddleware(logger, crashReporter)
)
```

これで完成！StoreインスタンスへとDispatchされたActionはすべて、`logger`と`crashReporter`を通るようになりました：

```js
// loggerとcrashRepor、両方のミドルウェアを通ることになる！
store.dispatch(addTodo('Use Redux'))
```

## 7つの使用例

もし上記のセクションを読んで頭が痛くなったら、ミドルウェアを書くって結局どういう事なのか考えてみてください。このセクションは、リラックスするためにあります。そして、頭の回転を助けます。

下記の関数はそれぞれ、有効なReduxミドルウェアです。ただ、すべて有用というわけではありません。しかし少なくとも、すべて楽しい関数です。

```js
/**
 * Dispatchされた、すべてのActionと状態のログを取る
 */
const logger = store => next => action => {
  console.group(action.type)
  console.info('Dispatching', action)
  let result = next(action)
  console.log('次の状態', store.getState())
  console.groupEnd(action.type)
  return result
}

/**
 * 状態が更新されリスナーに通知されたら、クラッシュレポートを送る
 */
const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('例外をキャッチした！', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}

/**
 * { meta: { delay: N } }を持つActionに対し、Nミリ秒の遅延をスケジューリングする
 * この場合`dispatch`が、タイムアウトを取り消す関数を返すようにする
 */
const timeoutScheduler = store => next => action => {
  if (!action.meta || !action.meta.delay) {
    return next(action)
  }

  let timeoutId = setTimeout(
    () => next(action),
    action.meta.delay
  )

  return function cancel() {
    clearTimeout(timeoutId)
  }
}

/**
 * { meta: { raf: true } }を持つActionに対し、
 * rAFループ内でDispatchされることをスケジューリングする
 * この場合`dispatch`が、キュー（待ち行列）からActionを削除する関数を返すようにする
 */
const rafScheduler = store => next => {
  let queuedActions = []
  let frame = null

  function loop() {
    frame = null
    try {
      if (queuedActions.length) {
        next(queuedActions.shift())
      }
    } finally {
      maybeRaf()
    }
  }

  function maybeRaf() {
    if (queuedActions.length && !frame) {
      frame = requestAnimationFrame(loop)
    }
  }

  return action => {
    if (!action.meta || !action.meta.raf) {
      return next(action)
    }

    queuedActions.push(action)
    maybeRaf()

    return function cancel() {
      queuedActions = queuedActions.filter(a => a !== action)
    }
  }
}

/**
 * Actionに加え、PromiseもDispatchできるようにする
 * Promiseが解決したら、その結果がActionとしてDispatchされる
 * 呼び出し元でReject処理ができるように、このPromiseは`dispatch`から返される
 */
const vanillaPromise = store => next => action => {
  if (typeof action.then !== 'function') {
    return next(action)
  }

  return Promise.resolve(action).then(store.dispatch)
}

/**
 * { promise }フィールドを持つ特別なActionをDispatchできるようにする
 *
 * このミドルウェアはまず、{ promise }フィールドを持つActionを普通のActionに変えてDispatchする
 * そして`promise`が解決したら、成功（または失敗）を加えたActionに変えて再びDispatchする
 *
 * 呼び出し元で待機できるように、このPromiseは`dispatch`から返される
 * こうすると都合が良い
 */
const readyStatePromise = store => next => action => {
  if (!action.promise) {
    return next(action)
  }

  function makeAction(ready, data) {
    let newAction = Object.assign({}, action, { ready }, data)
    delete newAction.promise
    return newAction
  }

  next(makeAction(false))
  return action.promise.then(
    result => next(makeAction(true, { result })),
    error => next(makeAction(true, { error }))
  )
}

/**
 * Actionの代わりに関数をDispatchできるようにする
 * この関数は、引数として`dispatch`と`getState`を受け取る
 *
 * 非同期な制御フローと同じく（Action以外も`dispatch()`できる）、
 * 早く抜けるのに役立つ（`getState()`によって条件付け）
 *
 * `dispatch`はDispatchされた関数の戻り値を返す
 */
const thunk = store => next => action =>
  typeof action === 'function'
    ? action(store.dispatch, store.getState)
    : next(action)

// これら全部を使えます！（使うべきだという意味ではありません）
let todoApp = combineReducers(reducers)
let store = createStore(
  todoApp,
  applyMiddleware(
    rafScheduler,
    timeoutScheduler,
    thunk,
    vanillaPromise,
    readyStatePromise,
    logger,
    crashReporter
  )
)
```
