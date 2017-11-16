# 非同期なAction

[初級チュートリアル](../basics/README.md)では、シンプルなTodoアプリケーションを作りました。これは完全に同期的なアプリです。ActionがDispatch（送信）されると毎回、すぐに状態が更新されます。

しかしこのチュートリアルでは、非同期なアプリケーションを作ります。具体的には、RedditのAPIを使います。選択したサブレディット（訳注：特定の話題を扱う掲示板。いわゆる「板」のこと）にある、現在の見出しを一覧表示するためです。どうやって非同期に、Reduxフローへ適合させれば良いでしょう？

## Action

非同期APIを呼び出すとき、2つのとても重要な瞬間があります：呼び出しを始める瞬間と、応答を受け取る（またはタイムアウトした）瞬間です。

この2つの瞬間はそれぞれ、アプリケーションの状態変更を必要とするのが一般的です。そのためには、標準的なActionをいくつかDispatchする必要があります。これらのActionは、Reducerによって同期的に処理されます。通常どんなAPIリクエストでも、最低3回は違う種類のActionをDispatchしたいでしょう：

* **Reducerに、リクエストの開始を知らせるAction**

  Reducerは状態にある`isFetching`フラグを切り替えることで、このActionを処理できます。これで、UIはスピナーを表示するタイミングだと分かります。

* **Reducerに、リクエストの成功を知らせるAction**

  Reducerは管理している状態に新しいデータをマージして、`isFetching`をリセットします。こうすることで、ReducerはこのActionを処理できます。UIは、スピナーを非表示にして取得したデータを表示します。

* **Reducerに、リクエストの失敗を知らせるAction**

  Reducerは`isFetching`フラグをリセットすることで、このActionを処理できます。加えて、Reducerはエラーメッセージを保持しても良いでしょう。UIがこのエラーメッセージを表示するためです。

Action専用の`status`フィールドを使えます：

```js
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }
```

または、別々のタイプを定義しても構いません：

```js
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
```

フラグと一緒に1つのActionタイプを使うか、または複数のActionタイプを使うかは、あなた次第です。つまりチーム全体で決めるべき約束事です。複数タイプの方が、失敗する余地はありません。しかし[redux-actions](https://github.com/acdlite/redux-actions)のようなヘルパーライブラリでActionクリエイターとReducerを生成すれば、問題にはなりません。

どんな約束事を選んだとしても、アプリケーション全体でそれを守ってください。
このチュートリアルでは、複数のタイプを使います。

## 同期的なActionクリエイター

このアプリで必要な同期的なActionタイプとActionクリエイターを、いくつか定義することから始めましょう。まず、ユーザーは表示するサブレディットを選択できます：

#### `actions.js` (Synchronous)

```js
export const SELECT_SUBREDDIT = 'SELECT_SUBREDDIT'

export function selectSubreddit(subreddit) {
  return {
    type: SELECT_SUBREDDIT,
    subreddit
  }
}
```

またユーザーは、更新するための“再読み込み”ボタンを押せます：

```js
export const INVALIDATE_SUBREDDIT = 'INVALIDATE_SUBREDDIT'

export function invalidateSubreddit(subreddit) {
  return {
    type: INVALIDATE_SUBREDDIT,
    subreddit
  }
}
```

これらはユーザーの操作に左右されるActionです。また違う種類のActionもあります。それは、ネットワークリクエストに左右されるActionです。これらをDispatchする方法は後で確認しましょう。今は定義だけします。

`SELECT_SUBREDDIT`と`INVALIDATE_SUBREDDIT`を分けることが大切です。もちろん、この2つは続けて起きることもあります。しかし、アプリが発展するにつれてより複雑になります。そうすると、ユーザーのActionに関係なくデータを取得したくなるかもしれません。（例えば1番人気のサブレディットを前もって取得したり、ときどき古いデータを再読み込みするなど）または、ルート遷移に応じて取得したくなるかもしれません。そのため初期の段階で、取得と特定のUIイベントを一緒にするのは、賢いやり方ではありません。

サブレディットの投稿を取得するには、`REQUEST_POSTS`ActionをDispatchします：

```js
export const REQUEST_POSTS = 'REQUEST_POSTS'

function requestPosts(subreddit) {
  return {
    type: REQUEST_POSTS,
    subreddit
  }
}
```

最後に、ネットワークリクエストが無事にDispatchされてきたら、`RECEIVE_POSTS`をDispatchします：

```js
export const RECEIVE_POSTS = 'RECEIVE_POSTS'

function receivePosts(subreddit, json) {
  return {
    type: RECEIVE_POSTS,
    subreddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  }
}
```

今のところ知っておくべきことは、これですべてです。Actionをネットワークリクエストと共にDispatchするための特別な仕組みは、後で検討しましょう。

>##### エラーハンドリングについての注意

>実際のアプリでは、リクエストの失敗でもActionをDispatchしたいはずです。このチュートリアルでは、エラーハンドリングを実装しません。しかし[リアルワールドの例](../introduction/Examples.md#リアルワールド)では、適切な対処法の1つを示しています。

## 状態の形をデザインする

初級チュートリアルと同じく、実装へ突き進む前に[アプリケーションにおける状態の形をデザインする](../basics/Reducers.md#状態の形をデザインする)必要があります。また非同期的なコードが加わると、気をつけなければならない状態が多くなります。そのためじっくり考える必要があります。

ここは、よく初心者が混乱する部分です。なぜならどの情報が非同期アプリケーションの状態を記述していて、どうやってそれを1つの状態ツリーにまとめるか、すぐ明確にはならないからです。

最も一般的な事例から始めましょう。Webアプリケーションではよく、物事のリストを表示することがあります。例えば投稿のリストや、友人のリスト。アプリを開発するには、どんなソートを表示可能か理解する必要があります。これらのリストは、状態の中で別々に保持したいはずです。別々に保持することで、キャッシュして必要なときだけ再取得できるからです。

“Redditの見出し一覧”の状態の形は、下記のようになるでしょう：

```js
{
  selectedSubreddit: 'frontend',
  postsBySubreddit: {
    frontend: {
      isFetching: true,
      didInvalidate: false,
      items: []
    },
    reactjs: {
      isFetching: false,
      didInvalidate: false,
      lastUpdated: 1439478405547,
      items: [
        {
          id: 42,
          title: 'FluxとRelayについての混乱'
        },
        {
          id: 500,
          title: 'React JSとFluxアーキテクチャを使って、シンプルなアプリケーションを作る'
        }
      ]
    }
  }
}
```

重要な点があります：

* それぞれのサブレディット情報を別々に保持しています。すべてのサブレディットをキャッシュするためです。ユーザーが2度目に切り替えた際は、すぐに更新されます。意図しない限り、再取得しなくて良いのです。すべての投稿データをメモリに保持することについて、心配する必要はありません：数万の投稿を扱い、ユーザーがめったにタブを閉じないような場合でなければ、メモリの解放などは全く必要ないでしょう。

* サブレディットのリストごとに、下記を保持します。
  * `isFetching`：スピナーを表示するため
  * `didInvalidate`：データが古くなったら更新するため
  * `lastUpdated`：最後にいつ取得されたか把握するため
  * `items`：投稿本文
  * `fetchedPageCount`や`nextPageUrl`：実際のアプリでは、ページネーション（ページ繰り）の状態を保持するため

>##### ネストしたエンティティについての注意Note on Nested Entities

>この使用例では、受け取った投稿データをページネーション情報と一緒に保持しています。しかしこの方法は上手くいかない場合があります。それは、ネストしたエンティティがお互いを参照したり、ユーザーが個々のデータを編集できるような場合です。ある投稿を、ユーザーが編集したいとしましょう。しかしこのとき、投稿は状態ツリー内のいくつかの場所に分散しています。これでは、実装がとても辛いでしょう。

>ネストしたエンティティがあったり、ユーザーが受け取ったエンティティを編集可能なら、状態内で別々に保持すべきです。まるでデータベースのように。そしてページネーション情報では、エンティティをIDだけで参照しましょう。こうすることで、いつも最新の状態を保てます。 [リアルワールドの例](../introduction/Examples.md#リアルワールド)では、この方法を取っています。また[normalizr](https://github.com/paularmstrong/normalizr)で、ネストされたAPIレスポンスを正規化しています。状態はこのようになるでしょう：

>```js
> {
>   selectedSubreddit: 'frontend',
>   entities: {
>     users: {
>       2: {
>         id: 2,
>         name: 'Andrew'
>       }
>     },
>     posts: {
>       42: {
>         id: 42,
>         title: 'FluxとRelayについての混乱',
>         author: 2
>       },
>       100: {
>         id: 100,
>         title: 'React JSとFluxアーキテクチャを使って、シンプルなアプリケーションを作る',
>         author: 2
>       }
>     }
>   },
>   postsBySubreddit: {
>     frontend: {
>       isFetching: true,
>       didInvalidate: false,
>       items: []
>     },
>     reactjs: {
>       isFetching: false,
>       didInvalidate: false,
>       lastUpdated: 1439478405547,
>       items: [ 42, 100 ]
>     }
>   }
> }
>```

>このチュートリアルでは、エンティティを正規化しません。しかし、もっと動的なアプリケーションでは考慮すべきです。

## Actionを処理する

ネットワークリクエストと共にActionをDispatchする詳細へ進む前に、上記で定義したActionのためにReducerを書きましょう。

>##### Reducer合成についての注意

> ここでは、[`combineReducers()`](../api/combineReducers.md)によるReducer合成を理解していることが前提です。`combineReducers()`は、[初級チュートリアル](../basics/README.md)にある[Reducerを分割する](../basics/Reducers.md#reducerを分割する)という部分で説明しています。もしまだ理解できていないようなら、[ここを最初に読んで](../basics/Reducers.md#reducerを分割する)ください。

#### `reducers.js`

```js
import { combineReducers } from 'redux'
import {
  SELECT_SUBREDDIT,
  INVALIDATE_SUBREDDIT,
  REQUEST_POSTS,
  RECEIVE_POSTS
} from '../actions'

function selectedSubreddit(state = 'reactjs', action) {
  switch (action.type) {
    case SELECT_SUBREDDIT:
      return action.subreddit
    default:
      return state
  }
}

function posts(
  state = {
    isFetching: false,
    didInvalidate: false,
    items: []
  },
  action
) {
  switch (action.type) {
    case INVALIDATE_SUBREDDIT:
      return Object.assign({}, state, {
        didInvalidate: true
      })
    case REQUEST_POSTS:
      return Object.assign({}, state, {
        isFetching: true,
        didInvalidate: false
      })
    case RECEIVE_POSTS:
      return Object.assign({}, state, {
        isFetching: false,
        didInvalidate: false,
        items: action.posts,
        lastUpdated: action.receivedAt
      })
    default:
      return state
  }
}

function postsBySubreddit(state = {}, action) {
  switch (action.type) {
    case INVALIDATE_SUBREDDIT:
    case RECEIVE_POSTS:
    case REQUEST_POSTS:
      return Object.assign({}, state, {
        [action.subreddit]: posts(state[action.subreddit], action)
      })
    default:
      return state
  }
}

const rootReducer = combineReducers({
  postsBySubreddit,
  selectedSubreddit
})

export default rootReducer
```

このコードでは、2つの興味深い点があります：

<!-- textlint-disable preset-jtf-style/1.1.3.箇条書き -->
* ES6の計算されたプロパティ構文（computed property syntax）を使っています。`Object.assign()`で、`state[action.subreddit]`を簡単な方法で更新できます。この部分です：
<!-- textlint-enable preset-jtf-style/1.1.3.箇条書き -->

  ```js
  return Object.assign({}, state, {
    [action.subreddit]: posts(state[action.subreddit], action)
  })
  ```
  これは下記と同等です：

  ```js
  let nextState = {}
  nextState[action.subreddit] = posts(state[action.subreddit], action)
  return Object.assign({}, state, nextState)
  ```
* 特定の投稿リストについて状態管理する、`posts(state, action)`を抜き出しました。これこそ[Reducer合成](../basics/Reducers.md#reducerを分割する)です！Reducerを、どうやってより小さなReducerに分割するかは自由です。ここではオブジェクト内にある、投稿データの更新を`posts`に任せています。[リアルワールドの例](../introduction/Examples.md#リアルワールド)ではさらに、ページネーションのために抜き出したReducerをパラメータ化して、Reducerファクトリで作り出す方法を示しています。

Reducerはただの関数だということを思い出してください。そのため思う存分、関数合成や高階関数が使えます。

## 非同期的なActionクリエイター

最後に、[先ほど定義した](#同期的なActionクリエイター)同期的なActionクリエイターを、どうやってネットワークリクエストと共に使えば良いでしょうか？　Reduxでの標準的な方法は、[Redux Thunkのミドルウェア](https://github.com/gaearon/redux-thunk)を使うことです。`redux-thunk`という別のパッケージを導入します。一般的にミドルウェアがどう機能するかは、[あとで](Middleware.md)説明します。今のところ、知っておくべき大切なことは1つだけです：この特別なミドルウェアを使うことで、Actionクリエイターはオブジェクトの代わりに関数を返せるようになります。これにより、Actionクリエイターは[Thunk](https://en.wikipedia.org/wiki/Thunk)になります。

Actionクリエイターが関数を返すと、その関数はRedux Thunkのミドルウェアによって実行されます。この関数は、純粋でなくても構いません。つまり非同期のAPI呼び出しを含めて、副作用を持つことが許されます。またこの関数は、ActionをDispatchすることもできます。先ほど定義した、同期的なActionと同じように。

これら特別なThunk Actionも、これまで通り`actions.js`ファイル内で定義できます：

#### `actions.js` (Asynchronous)

```js
import fetch from 'cross-fetch'

export const REQUEST_POSTS = 'REQUEST_POSTS'
function requestPosts(subreddit) {
  return {
    type: REQUEST_POSTS,
    subreddit
  }
}

export const RECEIVE_POSTS = 'RECEIVE_POSTS'
function receivePosts(subreddit, json) {
  return {
    type: RECEIVE_POSTS,
    subreddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  }
}

export const INVALIDATE_SUBREDDIT = 'INVALIDATE_SUBREDDIT'
export function invalidateSubreddit(subreddit) {
  return {
    type: INVALIDATE_SUBREDDIT,
    subreddit
  }
}

// 初めてThunk Actionとご対面！
// 内部は異なりますが、使い方はその他のActionクリエイターと同様です：
// store.dispatch(fetchPosts('reactjs'))

export function fetchPosts(subreddit) {
  // Thunkミドルウェアは関数の処理方法を分かっています。
  // 関数に引数としてDispatchメソッドを渡されたら、
  // DispatchメソッドがActionをDispatchできるようにします。

  return function (dispatch) {
    // 最初のDispatch：API呼び出しが始まり、
    // アプリの状態が更新されたことを伝えます。

    dispatch(requestPosts(subreddit))

    // Thunkミドルウェアによって呼び出された関数は、値を返せます。
    // この値はDispatchメソッドへ渡されます。
    // 渡された値は、Dispatchメソッドの戻り値になります。

    // この事例では、待機するためにPromiseを返します。
    // Thunkミドルウェアにとって必要なわけではなく、その方が書きやすいからです。

    return fetch(`https://www.reddit.com/r/${subreddit}.json`)
      .then(
        response => response.json(),
        // Catchを使わないでください。
        // Dispatchとそれに続く描画に関するすべてのエラーが、
        // Catchされてしまうからです。
        // これは'Unexpected batch number'エラーのループを引き起こします。
        // https://github.com/facebook/react/issues/6895
        error => console.log('An error occurred.', error)
      )
      .then(json =>
        // 何度でもDispatchできます！
        // ここでは、API呼び出しの結果でアプリ状態を更新します。

        dispatch(receivePosts(subreddit, json))
      )
  }
}
```

>##### `fetch`についての注意

>この使用例では、[`fetch` API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API)を使っています。`fetch`はネットワークリクエストをするための、新しいAPIです。`XMLHttpRequest`に代わり、最も共通したニーズに応えます。ほとんどのブラウザが、まだネイティヴでは対応していません。そのため[`cross-fetch`](https://github.com/lquixada/cross-fetch)ライブラリの使用を提案します：

>```js
>// `fetch`を使う場所すべてにこれを書いてください
>import fetch from 'cross-fetch'
>```

>内部的に、クライアントでは[`whatwg-fetch` Polyfill](https://github.com/github/fetch)を使っています。またサーバーでは[`node-fetch`](https://github.com/bitinn/node-fetch)を使っています。そのためアプリを[ユニバーサル（universal）](https://medium.com/@mjackson/universal-javascript-4761051b7ae9)に変更しても、API呼び出しを変更する必要はありません。

>すべての`fetch`ポリフィルは、[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)ポリフィルがあることを前提としています。Promiseポリフィルを確実に持たせる、最も簡単な方法があります。それは、どんなコードを実行するよりも前に、エントリーポイントでBabelのES6ポリフィルを有効にすることです：

>```js
>// アプリのどんなコードよりも前に、一度だけ行う
>import 'babel-polyfill'
>```

Redux Thunkミドルウェアを、どうやってDispatchの仕組みに取り込めば良いでしょう？　Reduxから、[`applyMiddleware()`](../api/applyMiddleware.md)というStoreエンハンサー（強化プログラム）を使います。下記のように：

#### `index.js`

```js
import thunkMiddleware from 'redux-thunk'
import { createLogger } from 'redux-logger'
import { createStore, applyMiddleware } from 'redux'
import { selectSubreddit, fetchPosts } from './actions'
import rootReducer from './reducers'

const loggerMiddleware = createLogger()

const store = createStore(
  rootReducer,
  applyMiddleware(
    thunkMiddleware, // 関数をdispatch()できるようにする。
    loggerMiddleware // Actionのログを取るための、簡潔なミドルウェア。
  )
)

store.dispatch(selectSubreddit('reactjs'))
store
  .dispatch(fetchPosts('reactjs'))
  .then(() => console.log(store.getState()))
```

Thunkの良いところは、Thunkの間でお互いの結果をDispatchできることです：

#### `actions.js` (with `fetch`)

```js
import fetch from 'cross-fetch'

export const REQUEST_POSTS = 'REQUEST_POSTS'
function requestPosts(subreddit) {
  return {
    type: REQUEST_POSTS,
    subreddit
  }
}

export const RECEIVE_POSTS = 'RECEIVE_POSTS'
function receivePosts(subreddit, json) {
  return {
    type: RECEIVE_POSTS,
    subreddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  }
}

export const INVALIDATE_SUBREDDIT = 'INVALIDATE_SUBREDDIT'
export function invalidateSubreddit(subreddit) {
  return {
    type: INVALIDATE_SUBREDDIT,
    subreddit
  }
}

function fetchPosts(subreddit) {
  return dispatch => {
    dispatch(requestPosts(subreddit))
    return fetch(`https://www.reddit.com/r/${subreddit}.json`)
      .then(response => response.json())
      .then(json => dispatch(receivePosts(subreddit, json)))
  }
}

function shouldFetchPosts(state, subreddit) {
  const posts = state.postsBySubreddit[subreddit]
  if (!posts) {
    return true
  } else if (posts.isFetching) {
    return false
  } else {
    return posts.didInvalidate
  }
}

export function fetchPostsIfNeeded(subreddit) {
  // 関数はgetState()も受け取ることに注意してください。
  // 次に何をDispatchするか選択できます。

  // これはキャッシュされた値が利用できるとき、
  // ネットワークリクエストを避けるために有効です。

  return (dispatch, getState) => {
    if (shouldFetchPosts(getState(), subreddit)) {
      // ThunkからThunkをDispatchする！
      return dispatch(fetchPosts(subreddit))
    } else {
      // 呼び出しコードに、待つべきものはないと知らせる。
      return Promise.resolve()
    }
  }
}
```

これでより高性能な非同期制御フローを、段階的に書けるようになりました。一方で、これを実行するコードはほとんど同じです：

#### `index.js`

```js
store
  .dispatch(fetchPostsIfNeeded('reactjs'))
  .then(() => console.log(store.getState()))
```

>##### サーバーレンダリングについての注意

>非同期なActionクリエイターは、サーバーレンダリングにとって非常に便利です。まずStoreを作り、1つの非同期なActionクリエイターをDispatchします。このActionクリエイターが、さらに別の非同期なActionクリエイターをDispatchします。このActionクリエイターは、アプリ全体のデータを取得します。データを取得してPromiseを返した後のみ、描画します。これで完了です。描画前に必要な状態が、Storeにはもう供給されているのです。

[Thunkミドルウェア](https://github.com/gaearon/redux-thunk)以外にも、Reduxで非同期なActionをまとめる方法はあります：
- 関数の代わりにPromiseをDispatchするには、[redux-promise](https://github.com/acdlite/redux-promise)か[redux-promise-middleware](https://github.com/pburtchaell/redux-promise-middleware)が使えます。
- ObservableをDispatchするには、[redux-observable](https://github.com/redux-observable/redux-observable) が使えます。
- より複雑で非同期なActionを組み立てるには、[redux-saga](https://github.com/yelouafi/redux-saga/)というミドルウェアが使えます。
- Promiseベースの非同期なActionをDispatchするには、[redux-pack](https://github.com/lelandrichardson/redux-pack) が使えます。
- APIの呼び出しを記述する（訳注：例えばAPIの呼び出しや、その戻り値の正規化などをする）ために、あなたが自分でミドルウェアを書くことさえできます。[リアルワールドの例](../introduction/Examples.md#リアルワールド)でやっているように。

<!-- textlint-disable preset-japanese/no-doubled-joshi -->
これらの選択肢を試すかどうかは、あなた次第です。ミドルウェアを使っても使わなくても、好きなものを選び、その約束事に従ってください。
<!-- textlint-enable preset-japanese/no-doubled-joshi -->

## UIにつなげる

非同期なActionをDispatchすることは、同期的なActionをDispatchするのと変わりません。そのため詳しい説明はしません。ReactコンポーネントからReduxを使うには、まず[Reactと使う](../basics/UsageWithReact.md)を確認してください。この使用例で検討したソースコードの完全版は、 [使用例： Reddit API](ExampleRedditAPI.md)を確認してください。

## 次のステップ

[非同期なフロー](AsyncFlow.md)を読んでください。Reduxフローに、非同期なActionがどう適合するか、その要点をつかむためです。
