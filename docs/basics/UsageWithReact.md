# Reactと使う

まず最初に、ReduxはReactと何の関係もないことを強調する必要があります。ReduxアプリはReact、Angular、Ember、jQuery、あるいはJavaScript単体とでも書けます。

とはいえ、Reduxは[React](http://facebook.github.io/react/)や[Deku](https://github.com/dekujs/deku)のようなライブラリと特に相性が良いです。これらのライブラリではUIを状態の関数として表すことができ、ReduxはActionに反応して状態更新を送り出すからです。

ここではReactを、シンプルなTodoアプリを作るのに使いましょう。

## React Reduxをインストール

デフォルトでは、[React bindings](https://github.com/reactjs/react-redux)はReduxに含まれていません。明示的にインスールする必要があります：

```
npm install --save react-redux
```

npmを使っていない方は、unpkgから最新のUMDビルドを簡単に利用しても良いです（[開発（development）](https://unpkg.com/react-redux@latest/dist/react-redux.js)か [本番（production）](https://unpkg.com/react-redux@latest/dist/react-redux.min.js)ビルドのどちらか）。`<script>`でページに加えると、`window.ReactRedux`というグローバル変数がエクスポートされます。

## プレゼンテーショナルとコンテナ、2種類のコンポーネント

Redux用のReactバインディング（連携プログラム）は、**プレゼンテーショナルとコンテナという、2種類のコンポーネント（構成要素）を分離する** というアイデアを取り入れています。[まずこれを読んで（read about them first）](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)から、戻ってきてください。重要なことが書かれているので、待っています！

記事を読み終わりましたか？　2つの違いをおさらいしましょう：

<table>
    <thead>
        <tr>
            <th></th>
            <th scope="col" style="text-align:left">プレゼンテーショナルコンポーネント</th>
            <th scope="col" style="text-align:left">コンテナコンポーネント</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th scope="row" style="text-align:right">目的</th>
          <td>どう見えるか（マークアップ、スタイル）</td>
          <td>どう働くか（データ取得、状態更新）</td>
        </tr>
        <tr>
          <th scope="row" style="text-align:right">Reduxの存在</th>
          <td>知らない</th>
          <td>知っている</th>
        </tr>
        <tr>
          <th scope="row" style="text-align:right">データ読み込み</th>
          <td>Propsから読み込む</td>
          <td>Reduxの状態を購読する</td>
        </tr>
        <tr>
          <th scope="row" style="text-align:right">データ変更</th>
          <td>Propsからコールバックを呼び出す</td>
          <td>ReduxのアクションをDispatch（送信）する</td>
        </tr>
        <tr>
          <th scope="row" style="text-align:right">作り方</th>
          <td>手作業で書く</td>
          <td>通常はReact Reduxで生成する</td>
        </tr>
    </tbody>
</table>

書くことになるコンポーネントのほとんどは、プレゼンテーショナルでしょう。しかしReduxのStoreとつなげるために、コンテナコンポーネントもいくつか生成する必要があります。この事と下記のデザイン概要は、コンテナコンポーネントが必ずコンポーネントツリーの上位にいなければならないと言っているのではありません。

というのも、コンテナコンポーネントが複雑になりすぎる場合があります。深くネストされたプレゼンテーショナルコンポーネントに、無数のコールバックを伝えるような場合です。このようなときは、コンポーネントツリー内にもう1つコンテナコンポーネントを導入してください。[FAQ](../faq/ReactRedux.md#react-multiple-components)でも言及しています。

技術的に、コンテナコンポーネントを手作業で書くことは可能です。この場合、[`store.subscribe()`](../api/Store.md#subscribe)を使います。しかし、おすすめしません。なぜならReact Reduxは、多くのパフォーマンス最適化を行なっているからです。これを手で書くのは難しいです。このためコンテナコンポーネントは書かずに、React Reduxで用意されている[`connect()`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)関数で生成しましょう。下記で確認できます。

## コンポーネント階層をデザインする

どのように[ルート（大元の）状態オブジェクトの形をデザインした](Reducers.md)か、覚えていますか？　状態オブジェクトの形に合わせて、UI階層をデザインします。これはRedux特有の作業ではありません。[Reactで考えること（Thinking in React）](https://facebook.github.io/react/docs/thinking-in-react.html)は、このプロセスを説明する素晴らしいチュートリアルです。

Todoアプリのデザイン概要はシンプルです。Todo項目のリストを表示したい。クリックで、個々のTodoは完了済みにする。ユーザーが新しいTodo項目を追加する欄を表示したい。すべてのTodo、完了のみ、未完のみを選択表示するために、フッターにトグル（切り替え）を表示したい。

###  プレゼンテーショナルコンポーネントをデザインする

プレゼンテーショナルコンポーネントとそのPropsは、次の概要から浮かび上がってきます：

* **`TodoList`** はTodoを表示するリスト。
  - `todos: Array` はTodo項目の配列。項目の形は`{ id, text, completed }`。
  - `onTodoClick(id: number)` はコールバック。Todo項目をクリックすると呼び出される。
* **`Todo`** は1つのTodo項目。
  - `text: string` は表示するテキスト。
<!-- textlint-disable preset-japanese/no-doubled-joshi -->
  - `completed: boolean` はTodo項目が完了済みかどうか。
<!-- textlint-enable preset-japanese/no-doubled-joshi -->
<!-- textlint-disable preset-jtf-style/1.1.3.箇条書き -->
  - `onClick()` はコールバック。Todo項目をクリックすると呼び出される。
<!-- textlint-enable preset-jtf-style/1.1.3.箇条書き -->
* **`Link`** はコールバックが紐づいたリンク。
  - `onClick()` はコールバック。リンクをクリックすると呼び出される。
* **`Footer`** はユーザーが、現在の表示項目を変更できる場所。
* **`App`** はルートコンポーネント。その他すべてを描画する。

これらは *見た目* を説明しています。しかし *どこから* データが来るか、また *どうやって* データを変化させるかは知りません。ただ与えられたものを描画するだけです。Reduxから何か他へ移行するなら、これらのコンポーネントをまったく同じように保持できます。Reduxへの依存はありません。

### コンテナコンポーネントをデザインする

プレゼンテーショナルコンポーネントをReduxにつなげるため、コンテナコンポーネントも必要です。例えばプレゼンテーショナルコンポーネントの`TodoList`は、`VisibleTodoList`のようなコンテナコンポーネントが必要です。ReduxのStoreを購読して、現在の表示フィルターをどう適用するか把握するためです。表示フィルターを変更するためには、`FilterLink`というコンテナコンポーネントを用意しましょう。プレゼンテーショナルコンポーネントの`Link`を描画し、クリックに応じて適切なActionをDispatchするためです：

* **`VisibleTodoList`** は現在の表示フィルターに従って、Todo項目を選別します。そして`TodoList`を描画します。
* **`FilterLink`** は現在の表示フィルターを取得して、`Link`を描画します。
  - `filter: string` はそれぞれの表示フィルター（すべてのTodo、完了のみ、未完のみ）を表しています。

### 他のコンポーネントをデザインする

あるコンポーネントをプレゼンテーショナルとコンテナコンポーネントのどちらにすべきか、判断しづらいことがあります。例えば、フォームと関数が結びついているような場合です。次の小さなコンポーネントのように：

* **`AddTodo`** は“追加”ボタンのついた入力欄です。

技術的には、2つのコンポーネントに分割できます。しかしこの段階では早すぎるかもしれません。とても小さなコンポーネントでは、プレゼンテーション（表現）とロジック（論理）を一緒にしても問題ありません。アプリが大きくなるにつれて、分割のやり方はより明らかになります。そのため今は、一緒にしておきましょう。

## コンポーネントを実装する

コンポーネントを書きましょう！まずはプレゼンテーショナルコンポーネントからです。従って、まだReduxへのバインディングを考える必要はありません。

### プレゼンテーショナルコンポーネントを実装する

これらはすべて、普通のReactコンポーネントです。そのため詳しくは説明しません。ローカルの状態やライフサイクルメソッドを使う必要がない限り、状態を持たない関数としてコンポーネントを書きます。これはプレゼンテーショナルコンポーネントが関数で *なければならない* という意味ではありません。ただ定義するのが、より簡単になります。関数として定義したコンポーネントは、クラスに書き換えることができます。書き換えるのは、ローカルの状態やライフサイクルメソッドを追加する必要のあるときや、パフォーマンスを最適化しなければならないときです。

#### `components/Todo.js`

```js
import React from 'react'
import PropTypes from 'prop-types'

const Todo = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    style={{
      textDecoration: completed ? 'line-through' : 'none'
    }}
  >
    {text}
  </li>
)

Todo.propTypes = {
  onClick: PropTypes.func.isRequired,
  completed: PropTypes.bool.isRequired,
  text: PropTypes.string.isRequired
}

export default Todo
```

#### `components/TodoList.js`

```js
import React from 'react'
import PropTypes from 'prop-types'
import Todo from './Todo'

const TodoList = ({ todos, onTodoClick }) => (
  <ul>
    {todos.map((todo, index) => (
      <Todo key={index} {...todo} onClick={() => onTodoClick(index)} />
    ))}
  </ul>
)

TodoList.propTypes = {
  todos: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      completed: PropTypes.bool.isRequired,
      text: PropTypes.string.isRequired
    }).isRequired
  ).isRequired,
  onTodoClick: PropTypes.func.isRequired
}

export default TodoList
```

#### `components/Link.js`

```js
import React from 'react'
import PropTypes from 'prop-types'

const Link = ({ active, children, onClick }) => {
  if (active) {
    return <span>{children}</span>
  }

  return (
    <a
      href="#"
      onClick={e => {
        e.preventDefault()
        onClick()
      }}
    >
      {children}
    </a>
  )
}

Link.propTypes = {
  active: PropTypes.bool.isRequired,
  children: PropTypes.node.isRequired,
  onClick: PropTypes.func.isRequired
}

export default Link
```

#### `components/Footer.js`

```js
import React from 'react'
import FilterLink from '../containers/FilterLink'

const Footer = () => (
  <p>
    Show:
    {' '}
    <FilterLink filter="SHOW_ALL">
      All
    </FilterLink>
    {', '}
    <FilterLink filter="SHOW_ACTIVE">
      Active
    </FilterLink>
    {', '}
    <FilterLink filter="SHOW_COMPLETED">
      Completed
    </FilterLink>
  </p>
)

export default Footer
```

### コンテナコンポーネントを実装する

次に、いくつかコンテナコンポーネントを作ります。上記のプレゼンテーショナルコンポーネントをReduxにつなぐためです。技術的に、コンテナはただのReactコンポーネントです。[`store.subscribe()`](../api/Store.md#subscribe)を使い、Reduxの状態ツリーの一部を読み込みます。そしてPropsを、描画するプレゼンテーショナルコンポーネントに渡します。

コンテナコンポーネントは手作業で書けます。しかし、React Reduxライブラリの[`connect()`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)関数で生成することをおすすめします。このライブラリには、多くの役立つ最適化が用意されています。不必要な再描画を避けるためです。
<!-- textlint-disable preset-japanese/sentence-length -->
（これによる1つの成果があります。それは、`shouldComponentUpdate`を自分で実装するという[Reactのパフォーマンス提案（React performance suggestion）](https://facebook.github.io/react/docs/advanced-performance.html)について、心配しなくて良いということです）
<!-- textlint-enable preset-japanese/sentence-length -->

`connect()`を使うためには、`mapStateToProps`という特別な関数を定義する必要があります。この関数は、現在のRedux Storeの状態を、どのようにPropsに転換するか示します。このPropsは、コンテナがラップ（内包）しているプレゼンテーショナルコンポーネントに渡されます。例えば、`VisibleTodoList`は状態の`todos`を計算しなければいけません。計算した`todos`は、`TodoList`に渡します。そのために、`state.visibilityFilter`に従って`state.todos`を選別する関数を定義します。そしてこの関数を、`mapStateToProps`内で使います：

```js
const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed)
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed)
  }
}

const mapStateToProps = state => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
```

状態の読み込みに加えて、コンテナコンポーネントはActionのDispatchもできます。同じようにして、`mapDispatchToProps()`という関数を定義します。この関数は[`dispatch()`](../api/Store.md#dispatch)メソッドを受け取り、 コールバックとなるPropsを返します。このコールバックを、プレゼンテーショナルコンポーネントに渡すためです。例えば`VisibleTodoList`から`TodoList`コンポーネントへ、`onTodoClick`というコールバックをPropとして渡します。そして`onTodoClick`から、`TOGGLE_TODO`というActionをDispatchします：

```js
const mapDispatchToProps = dispatch => {
  return {
    onTodoClick: id => {
      dispatch(toggleTodo(id))
    }
  }
}
```

最後に、`VisibleTodoList`を作ります。そのために`connect()`を呼び出し、上記2つの関数を渡します：

```js
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```

これらはReact Redux APIの基本です。しかしもっと手っ取り早い方法や、有力な選択肢がいくつかあります。そのため[このドキュメント（its documentation）](https://github.com/reactjs/react-redux)を詳しく調べてみると良いでしょう。`mapStateToProps`が新しいオブジェクトを頻繁に作りすぎていると心配になるときは、[reselect](https://github.com/reactjs/reselect)とともに[派生データの計算](../recipes/ComputingDerivedData.md)を学ぶと良いかもしれません。

残りのコンテナコンポーネントを、下記で定義します：

#### `containers/FilterLink.js`

```js
import { connect } from 'react-redux'
import { setVisibilityFilter } from '../actions'
import Link from '../components/Link'

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}

const mapDispatchToProps = (dispatch, ownProps) => {
  return {
    onClick: () => {
      dispatch(setVisibilityFilter(ownProps.filter))
    }
  }
}

const FilterLink = connect(
  mapStateToProps,
  mapDispatchToProps
)(Link)

export default FilterLink
```

#### `containers/VisibleTodoList.js`

```js
import { connect } from 'react-redux'
import { toggleTodo } from '../actions'
import TodoList from '../components/TodoList'

const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed)
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed)
  }
}

const mapStateToProps = state => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}

const mapDispatchToProps = dispatch => {
  return {
    onTodoClick: id => {
      dispatch(toggleTodo(id))
    }
  }
}

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```

### 他のコンポーネントを実装する

#### `containers/AddTodo.js`

[先ほど言った](#designing-other-components)ように、`AddTodo`コンポーネントではプレゼンテーションとロジックが一緒に定義されています。

```js
import React from 'react'
import { connect } from 'react-redux'
import { addTodo } from '../actions'

let AddTodo = ({ dispatch }) => {
  let input

  return (
    <div>
      <form
        onSubmit={e => {
          e.preventDefault()
          if (!input.value.trim()) {
            return
          }
          dispatch(addTodo(input.value))
          input.value = ''
        }}
      >
        <input
          ref={node => {
            input = node
          }}
        />
        <button type="submit">
          Add Todo
        </button>
      </form>
    </div>
  )
}
AddTodo = connect()(AddTodo)

export default AddTodo
```

もし`ref`属性に不慣れなら、この[ドキュメント](https://facebook.github.io/react/docs/refs-and-the-dom.html)を読んでください。`ref`属性の推奨される使い方が分かります。

### 1つのコンポーネント内で、複数のコンテナをまとめる
#### `components/App.js`

```js
import React from 'react'
import Footer from './Footer'
import AddTodo from '../containers/AddTodo'
import VisibleTodoList from '../containers/VisibleTodoList'

const App = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
)

export default App
```

## Storeを渡す

すべてのコンテナコンポーネントは、Redux Storeにアクセスする必要があります。Storeを購読するためです。購読するための1つの方法は、すべてのコンテナコンポーネントにPropとしてStoreを渡すことでしょう。でもこれは面倒です。なぜなら`store`を、プレゼンテーショナルコンポーネントにまで渡す必要があるからです。たまたまプレゼンテーショナルコンポーネントが、コンポーネントツリーの深いところでコンテナを描画するというだけの理由で。

おすすめの方法は、[`<Provider>`](https://github.com/reactjs/react-redux/blob/master/docs/api.md#provider-store)という特別なReact Reduxコンポーネントを使うことです。このコンポーネントは、[魔法のように（magically）](https://facebook.github.io/react/docs/context.html)アプリケーション内のすべてのコンテナコンポーネントでStoreを利用できるようにします。明示的にStoreを渡さなくて良いのです。ルートコンポーネントを描画するときに1度、このコンポーネントを使うだけです：

#### `index.js`

```js
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp)

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

## 次のステップ

[このチュートリアルの完全なソースコード](ExampleTodoList.md)を読んでください。習得した知識を、より深く自分のものにするためです。そして、[上級チュートリアル](../advanced/README.md)へ直行しましょう。ネットワークリクエストとルーティングの処理方法を学ぶためです！
