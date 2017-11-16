# React Routerと使う

Reduxアプリでルーティングしたいなら、[React Router](https://github.com/reactjs/react-router)が使えます。Reduxは、データにとって情報源（真実の出所）です。そしてReact Routerは、URLにとって情報源です。ほとんどの場合、ReduxとReact Routerは分離しておくのが **好ましい** です。ただし、URLの変更をきっかけにしたタイムトラベルとActionの巻き戻しが必要な場合、この限りではありません。

## React Routerをインストール
`react-router-dom`はnpmで利用可能です。ここでは、`react-router-dom@^4.1.1`を使っている前提です。

`npm install --save react-router-dom`

## フォールバックURL（訳注：404のとき、代わりになるURL）を設定

React Routerと統合する前に、開発サーバーを設定しないといけません。サーバーが、React Routerの設定で宣言されたルートを把握していない場合に備えるためです。例えば`/todos`にアクセスして再読み込みしたら、開発サーバーは`index.html`を返す必要があります。なぜなら、シングルページアプリだから（訳注：`/todos`へのルーティングを設定しないから）です。人気の開発サーバーでこのフォールバックを有効にする方法は、下記の通りです。

>### Create React Appについての注意

> Create React Appを使っているなら、フォールバックURLを設定する必要はありません。自動的に設定されます。

### Expressで設定
Expressから`index.html`を返しているなら：
```js
app.get('/*', (req, res) => {
  res.sendFile(path.join(__dirname, 'index.html'))
})
```

### WebpackDevServerで設定
WebpackDevServerから`index.html`を返しているなら：
webpack.config.dev.jsに追記してください：
```js
devServer: {
  historyApiFallback: true
}
```

## Redux AppとReact Routerをつなぐ

このページを通して、[Todos](https://github.com/reactjs/redux/tree/master/examples/todos)を例として使います。これをクローンして、ページを読み進めるのがおすすめです。

まず、React Routerから`<Router />`と`<Route />`をインポートする必要があります。やり方は下記の通りです：

```js
import { BrowserRouter as Router, Route } from 'react-router-dom'
```

Reactアプリは、たいてい`<Router />`で`<Route />`をラップ（内包）します。こうするとURLが変化したとき、`<Router />`はそのルーティング設定を、内包する`<Route />`とマッチングできます。そして、マッチングした`<Route />`で指定されたコンポーネントを描画します。`<Route />`は、ルーティング設定とアプリケーションのコンポーネント階層を宣言的な形で対応づけるために使われます。具体的には、`path`でパスを宣言します。このパスは、URLで使われます。ルーティング設定がURLとマッチングしたら、`component`で指定した1つのコンポーネントが描画されます。

<!-- textlint-disable preset-japanese/sentence-length -->
（訳注：上記、原文を補足しています。もし誤りがあればご指摘ください。原文は次の通りです。
“In a React app, usually you would wrap `<Route />` in `<Router />` so that when the URL changes, `<Router />` will match a branch of its routes, and render their configured components. `<Route />` is used to declaratively map routes to your application's component hierarchy. You would declare in `path` the path used in the URL and in `component` the single component to be rendered when the route matches the URL.”）
<!-- textlint-enable preset-japanese/sentence-length -->

```js
const Root = () => (
  <Router>
    <Route path="/" component={App} />
  </Router>
)
```

`<Router />`だけでなく、Reduxのアプリには`<Provider />`も必要です。`<Provider />`は、React Reduxによって提供される高階コンポーネントです。ReduxをReactにバインド（連携）させることができます（[Reactと使う](../basics/UsageWithReact.md)をご確認ください）。

下記のように、React Reduxから`<Provider />`をインポートします：

```js
import { Provider } from 'react-redux'
```

`<Provider />`で`<Router />`をラップします。ルーティング設定を処理する関数（ルートハンドラ）が、[`store`へアクセス](../basics/UsageWithReact.md#storeを渡す)できるようにするためです。

```js
const Root = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Route path="/" component={App} />
    </Router>
  </Provider>
)
```

 URLが'/'とマッチしたら、`<App />`コンポーネントが描画されます。加えて、`/`に`:filter?`という任意のパラメータを追加します。後ほどURLから`:filter`パラメータを読み込もうとするときに、必要となるからです。

```js
<Route path="/:filter?" component={App} />
```

#### `components/Root.js`
```js
import React from 'react'
import PropTypes from 'prop-types'
import { Provider } from 'react-redux'
import { BrowserRouter as Router, Route } from 'react-router-dom'
import App from './App'

const Root = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Route path="/:filter?" component={App} />
    </Router>
  </Provider>
)

Root.propTypes = {
  store: PropTypes.object.isRequired
}

export default Root
```

`index.js`もリファクタリングします。DOMに`<Root />`コンポーネントを描画するためです。

#### `index.js`
```js
import React from 'react'
import { render } from 'react-dom'
import { createStore } from 'redux'
import todoApp from './reducers'
import Root from './components/Root'

let store = createStore(todoApp)

render(
  <Root store={store} />,
  document.getElementById('root')
)
```

## React Routerでリンクを設定する

React Routerには、[`<Link />`](https://github.com/ReactTraining/react-router/blob/v3/docs/API.md#link) コンポーネントが用意されています。このコンポーネントは、アプリのリンクを設定します。スタイルをいくつか加えたいなら、 [`<NavLink />`](https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/docs/api/NavLink.md)という特別な`<Link />`もあります。このコンポーネントは、スタイリングのためのPropsを受け取ります。例えば`activeStyle`は、アクティブな状態のスタイルを適用できます。

この例では`<NavLink />`を、新しいコンテナコンポーネントの`<FilterLink />`でラップします。動的にURLを変更するためです。


#### `containers/FilterLink.js`
```js
import React from 'react'
import { NavLink } from 'react-router-dom'

const FilterLink = ({ filter, children }) => (
  <NavLink
    to={filter === 'SHOW_ALL' ? '/' : `/${ filter }`}
    activeStyle={{
      textDecoration: 'none',
      color: 'black'
    }}
  >
    {children}
  </NavLink>
)

export default FilterLink
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

`<FilterLink />`をクリックすると、URLが変わるようになりました。URLは、`'/SHOW_COMPLETED'`、 `'/SHOW_ACTIVE'`、 そして `'/'`の間で変わります。ブラウザで戻っても、ブラウザ履歴が使われて効率的に前のURLへ移ります。

## URLから読み込む

今のところ、TodoリストはURLが変わってもフィルタリングされません。なぜなら`<VisibleTodoList />`の`mapStateToProps()`からフィルタリングしているからです。 `mapStateToProps()`はURLではなく、状態にバインド（束縛）されたままです。`mapStateToProps`には任意の引数として、`ownProps`という第2引数を設定できます。`ownProps`は`<VisibleTodoList />`に渡されるすべてのPropsです。
#### `containers/VisibleTodoList.js`
```js
const mapStateToProps = (state, ownProps) => {
  return {
    todos: getVisibleTodos(state.todos, ownProps.filter) // 以前は、getVisibleTodos(state.todos, state.visibilityFilter) だった
  }
}
```

`<App />`に何も渡していないので、このままだと`ownProps`は空のオブジェクトです。URLに従ってTodoをフィルタリングするには、`<VisibleTodoList />`にURLパラメータを渡しましょう。

先ほど次のように書きました： `<Route path="/:filter?" component={App} />`  これで`App`内から、`params`プロパティが利用可能になりました。

`params`プロパティは`match`オブジェクトの中にあり、URLで指定されたすべてのパラメータを持ったオブジェクトです。*例：リンク先が`localhost:3000/SHOW_COMPLETED`なら、`match.params`は`{ filter: 'SHOW_COMPLETED' }`となります。そして`<App />`から、このURLを読み込めます。*

`<VisibleTodoList />`へ`params`を渡すのに、このプロパティで[ES6のDestructuring](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)を使っています。気をつけてください。

#### `components/App.js`
```js
const App = ({ match: { params } }) => {
  return (
    <div>
      <AddTodo />
      <VisibleTodoList filter={params.filter || 'SHOW_ALL'} />
      <Footer />
    </div>
  )
}
```

## 次のステップ

ここまでで、基本的なルーティングのやり方が分かりました。[React Router API](https://github.com/reactjs/react-router/tree/v3/docs/)で、さらに学べます。

>##### 他のルーティングライブラリに関する注意

>*Redux Router* は実験的なライブラリです。Redux Store内で、URLの状態をすべて保持できます。APIは、React Router APIと同じです。しかしreact-routerより、コミュニティの支援は少ないです。

>*React Router Redux* はReduxアプリとreact-routerの間にバインドを作ります。これにより、Reduxアプリとreact-routerを同期させます。このバインドがなければ、タイムトラベルでActionを巻き戻すことはできないでしょう。タイムトラベルが必要なければ、React RouterとReduxは完全に分離して運用できます。
