# 使用例

Reduxではいくつかの使用例が、その[ソースコード](https://github.com/reactjs/redux/tree/master/examples)とともに提供されています。これら使用例のほとんどは、 [CodeSandbox](https://codesandbox.io)にもあります。CodeSandboxはオンラインで使用例を試せる、オンラインエディタです。
## 普通のカウンター (Counter Vanilla)

[Counter Vanilla](https://github.com/reactjs/redux/tree/master/examples/counter-vanilla)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/counter-vanilla
open index.html
```

ビルドシステムやビューフレームワークは必要ありません。ES5で生のRedux APIを確認できます。

## カウンター

[Counter](https://github.com/reactjs/redux/tree/master/examples/counter)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/counter
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/counter)を確認してください。

ReactとともにReduxを使った、最も基本的な使用例です。分かりやすくするため、Storeが変化したら手動でReactコンポーネントを再度レンダリング（描画）しています。 実際のプロジェクトでは、代わりに高性能な[React Redux](https://github.com/reactjs/react-redux)バインディング（連携プログラム）を使いたくなるでしょう。

この使用例は、テストを含んでいます。

## Todo

[Todos](https://github.com/reactjs/redux/tree/master/examples/todos)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/todos
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todos)を確認してください。

状態更新とReduxのコンポーネント（構成要素）が、どのように協働するか。これを深く理解するのに最適な使用例です。具体的には、Reducerが他のReducerへActionの処理を委任する方法。そしてプレゼンテーショナルコンポーネントからコンテナコンポーネントを作りだすために、[React Redux](https://github.com/reactjs/react-redux)を使う方法。この2つを示しています。

この使用例は、テストを含んでいます。

## やり直し（Undo)付きTodo

[Todos with Undo](https://github.com/reactjs/redux/tree/master/examples/todos-with-undo)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/todos-with-undo
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todos-with-undo)を確認してください。

先ほどの使用例に、手を加えたものです。ほどんど同じですが、 Reducerを[Redux Undo](https://github.com/omnidan/redux-undo) でどのようにラップ（内包）するかを示しています。数行のコードで、元に戻す/やり直す（Undo/Redo)機能が追加できます。

## TodoMVC

[TodoMVC](https://github.com/reactjs/redux/tree/master/examples/todomvc)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/todomvc
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todomvc)を確認してください。

古典的な[TodoMVC](http://todomvc.com/)の例です。比較のために掲載しています。しかし、Todoの例と同じ要点を取り扱っています。

この使用例は、テストを含んでいます。

## ショッピングカート（買い物かご）

[Shopping Cart](https://github.com/reactjs/redux/tree/master/examples/shopping-cart)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/shopping-cart
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/shopping-cart)を確認してください。

この使用例は、アプリが成長するにつれて重要になる慣用的なReduxパターンを示しています。特に、IDによって正規化された方法でエンティティを保持する方法。階層化した複数のReducerを合成する方法。そして状態の形についての認識をカプセル化するために、ReducerとともにSelector（訳注：セレクターは状態の一部を引数として受け取り、処理して返す関数）を定義する方法。この3つについて示しています。また[Redux Logger](https://github.com/fcomb/redux-logger)でログ取得や、 ミドルウェアの[Redux Thunk](https://github.com/gaearon/redux-thunk)を使って条件次第でActionのDispatch（送信）もしています。

## ツリー表示

[Tree View](https://github.com/reactjs/redux/tree/master/examples/tree-view)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/tree-view
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/tree-view)を確認してください。

この使用例では、深くネストされたツリー表示のレンダリングと、その状態を正規化された形で表すデモをしています。状態を正規化された形にすることで、Reducerの更新が簡単になります。レンダリングのパフォーマンスを高めるには、コンテナコンポーネントが描画するツリーノードだけを高い粒度で購読することです。

この使用例は、テストを含んでいます。

## 非同期

[Async](https://github.com/reactjs/redux/tree/master/examples/async)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/async
npm install
npm start

open http://localhost:3000/
```

または、 [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/async)を確認してください。

この使用例は非同期APIからの読み込み、ユーザー入力に対するデータ取得、取得したデータのキャッシュ、キャッシュの無効化が含まれています。また、ミドルウェアの[Redux Thunk](https://github.com/gaearon/redux-thunk)が使われています。 非同期の副作用をカプセル化するためです。

## ユニバーサル

[Universal](https://github.com/reactjs/redux/tree/master/examples/universal) を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/universal
npm install
npm start

open http://localhost:3000/
```

ReduxとReactで[サーバーレンダリング (server rendering)](../recipes/ServerRendering.md)をする基礎的なデモです。サーバーでStoreの初期状態を準備して、その状態をクライアントに受け渡す方法を示しています。クライアントのStoreは、サーバーから渡された既存の状態から立ち上げることができます。

## リアルワールド

[Real World](https://github.com/reactjs/redux/tree/master/examples/real-world)を実行する：

```
git clone https://github.com/reactjs/redux.git

cd redux/examples/real-world
npm install
npm start

open http://localhost:3000/
```

または [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/real-world)を確認してください。

もっとも高度な使用例です。多くのデザインパターンが詰まっています。正規化したキャッシュで、取得したエンティティを保持。API呼び出しのために、カスタマイズしたミドルウェアを実行。部分的に読み込んだデータをレンダリング。ページ表示。レスポンスのキャッシュ。エラーメッセージの表示。そしてルーティング。加えて、Reduxの開発者ツール（DevTools)も含まれます。

## 他の使用例

[Awesome Redux](https://github.com/xgrommx/awesome-redux)で、もっと多くの使用例を確認できます。
