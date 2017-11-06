# Action

まずはじめに、いくつかActionを定義しましょう。

**Action** は情報の本体であり、アプリケーションからStoreへ送るデータです。そしてStoreのための、*唯一の* 情報源です。[`store.dispatch()`](../api/Store.md#dispatch)を使うことで、ActionはStoreに送られます。

下記はActionの例です。新しいTodo項目の追加を表しています。

```js
const ADD_TODO = 'ADD_TODO'
```

```js
{
  type: ADD_TODO,
  text: '初めてのReduxアプリをつくる'
}
```

Actionは単なるJavaScriptのオブジェクトです。Actionは必ず`type`プロパティを持ちます。`type`プロパティが、実行されるActionのタイプを示しているのです。一般的には、文字列の定数として定義すべきです。そしてアプリが大きくなったら、Actionタイプを別のモジュールへ移したくなるかもしれません。

```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

>##### 常用文についての注意

>別ファイルで、Actionタイプの定数を定義する必要はありません。というより定義すること自体、しなくても構いません。小さなプロジェクトなら、Actionタイプとして文字列リテラルを使うだけにした方が簡単でしょう。しかし大きなコードベースでは明示的に定数を宣言することに、いくつか利点があります。詳しくは、[常用文の削減](../recipes/ReducingBoilerplate.md)を読んでください。コードベースを綺麗に保つ、実践的なコツが書かれています。

`type`以外の、Actionオブジェクトの構造はすべてあなた次第です。 Actionの構成方法に関するおすすめは、[Fluxの標準Action（Flux Standard Action）](https://github.com/acdlite/flux-standard-action)で確認できます。

ユーザーが完了したTodo項目にチェックできるよう、もう1つActionタイプを加えましょう。特定のTodo項目は、`index`で参照します。`index`を配列で保持しているからです。実際のアプリでは、何か新しいものが作られるたびにユニークIDを生成する方が賢明です。

```js
{
  type: TOGGLE_TODO,
  index: 5
}
```

それぞれのActionで渡すデータは、できるだけ小さくしましょう。例えば、Todoオブジェクト全体よりも、`index`を渡す方が良いです。

最後に、もう1つActionタイプを加えましょう。いま表示されるTodo項目を変更するための、Actionタイプです。

```js
{
  type: SET_VISIBILITY_FILTER,
  filter: SHOW_COMPLETED
}
```

## Actionクリエイター

**Actionクリエイター** はその名の通り、Actionを作る関数です。 “Action”と“Actionクリエイター”は混同しやすいので、正しい用語を使うように注意してください。

Reduxでは、Actionクリエイターは単純にActionを返します：

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

これによって移動が可能となり、テストも簡単になります。

[従来のFlux（traditional Flux）](http://facebook.github.io/flux)ではよく、Actionクリエイターが呼び出されたときにDispatch（ディスパッチ）を引き起こします：

```js
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

Reduxでは、そんなことは *ありません*。  
その代わりDispachを実際に始動するため、`dispatch()`関数にActionクリエイターの呼び出し結果を渡します：

```js
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

または、自動でDispachする **バウンドActionクリエイター（bound action creator）** を作ることもできます：

```js
const boundAddTodo = text => dispatch(addTodo(text))
const boundCompleteTodo = index => dispatch(completeTodo(index))
```

こうすると、直接的に呼び出せます：

```
boundAddTodo(text)
boundCompleteTodo(index)
```

`dispatch()`関数は、[`store.dispatch()`](../api/Store.md#dispatch)としてStoreから直接利用できます。しかし、ヘルパーを使って利用することが多いでしょう。[react-redux](http://github.com/gaearon/react-redux)の`connect()`などです。[`bindActionCreators()`](../api/bindActionCreators.md)を使うと、自動的に多くのActionクリエイターを`dispatch()`関数にバインド（結び付ける、束縛）できます。

Actionクリエイターは非同期でも良いし、副作用も持てます。 詳しくは[上級チュートリアル](../advanced/README.md)にある、[非同期Action](../advanced/AsyncActions.md)を読んでください。AJAXのレスポンスを処理し、Actionクリエイターを非同期な制御フローに組み立てる方法を学べます。初級チュートリアルを終えるまで、非同期Actionに飛ばないでください。 初級チュートリアルには上級チュートリアルと非同期Actionの前に必要となる、重要なコンセプトが含まれています。

## ソースコード

### `actions.js`

```js
/*
 * Actionタイプ
 */

export const ADD_TODO = 'ADD_TODO'
export const TOGGLE_TODO = 'TOGGLE_TODO'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'

/*
 * 他の定数
 */

export const VisibilityFilters = {
  SHOW_ALL: 'SHOW_ALL',
  SHOW_COMPLETED: 'SHOW_COMPLETED',
  SHOW_ACTIVE: 'SHOW_ACTIVE'
}

/*
 * Actionクリエイター
 */

export function addTodo(text) {
  return { type: ADD_TODO, text }
}

export function toggleTodo(index) {
  return { type: TOGGLE_TODO, index }
}

export function setVisibilityFilter(filter) {
  return { type: SET_VISIBILITY_FILTER, filter }
}
```

## 次のステップ

これから[Reducerを定義](Reducers.md)しましょう！Actionを送信したとき、どうやって状態更新するか指定するためです。
