# Store

先ほどのセクションで、[Action](Actions.md)を定義しました。Actionは、“何かが起きた”という事実を表します。また、[reducers](Reducers.md)も定義しました。Reducerは、Actionによって状態を更新します。

**Store** は、上記2つをまとめるオブジェクトです。Storeは次の責務を担います：

* アプリケーションの状態を保持する
* [`getState()`](../api/Store.md#getState)による状態へのアクセスを許可する
* [`dispatch(action)`](../api/Store.md#dispatch)による状態更新を許可する
* [`subscribe(listener)`](../api/Store.md#subscribe)によりリスナーを登録する
* [`subscribe(listener)`](../api/Store.md#subscribe)で返される関数により、リスナーの登録解除を処理する

Reduxアプリケーションで、Storeはただ1つです。これは重要なことなので、気をつけてください。 データ処理のロジックを分けたいときは、[Reducer合成](Reducers.md#reducerを分割する)を使いましょう。複数のStoreを作ってはいけません。

Reducerがあれば、Storeを作るのは簡単です。[前のセクション](Reducers.md)で、[`combineReducers()`](../api/combineReducers.md)を使いました。複数のReducerを1つにまとめるためです。このReducerをインポートして、[`createStore()`](../api/createStore.md)に渡しましょう。

```js
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)
```

初期状態を明示することもできます。初期状態は、[`createStore()`](../api/createStore.md)に第2引数として渡します。これはサーバーで実行しているReduxアプリケーションの状態を、クライアントの状態へ溶け込ませるのに有効です。

```js
let store = createStore(todoApp, window.STATE_FROM_SERVER)
```

## ActionのDispatch（送信）

いまStoreを作りました。プログラムが動くか確かめましょう！UIが全くなくても、もう更新ロジックをテストできます。

```js
import {
  addTodo,
  toggleTodo,
  setVisibilityFilter,
  VisibilityFilters
} from './actions'

// 初期状態のログをとる
console.log(store.getState())

// 状態が変化するたびに、ログをとる
// subscribe()はリスナーを登録解除するための関数を返すことに、注意してください
const unsubscribe = store.subscribe(() =>
  console.log(store.getState())
)

// ActionをいくつかDispatchする
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(toggleTodo(0))
store.dispatch(toggleTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))

// 状態更新の購読をやめる
unsubscribe()
```

Storeの保持している状態が、どう変わったか確認できます：

<img src='http://i.imgur.com/zMMtoMz.png' width='70%'>

UIを書き始める前にもう、アプリの動きを明示しました。このチュートリアルではしませんが、この時点でReducerとActionクリエイターのテストを書くこともできます。何もモックする必要はありません。なぜなら、ただ[純粋な](../introduction/ThreePrinciples.md#変化は純粋（副作用のない）関数で作られる)関数だからです。関数を呼び出し、返される値をアサートします。

## ソースコード

#### `index.js`

```js
import { createStore } from 'redux'
import todoApp from './reducers'

let store = createStore(todoApp)
```

## 次のステップ

TodoアプリのUIを作る前に、寄り道しましょう。[Reduxアプリケーションで、どのようにデータが流れるか](DataFlow.md)を確かめます。
