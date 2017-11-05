# <a href='http://redux.js.org'><img src='https://camo.githubusercontent.com/f28b5bc7822f1b7bb28a96d8d09e7d79169248fc/687474703a2f2f692e696d6775722e636f6d2f4a65567164514d2e706e67' height='60'></a>

ReduxはJavaScriptアプリのための、予測しやすい（意外性のない）状態コンテナです。  
(WordPressのフレームワークと混同しないでください – [Redux Framework](https://reduxframework.com/).)

アプリケーションが異なる環境（クライアント、サーバー、そしてネイティヴ）で一貫して動く手助けをします。テストも簡単です。さらに次のような、開発者のための素晴らしい体験も提供します。

[元に戻したり、やり直したりできるデバッガーで、コードを自動反映しながら編集する (live code editing combined with a time traveling debugger)](https://github.com/gaearon/redux-devtools)

Reduxは[React](https://facebook.github.io/react/)や、他のどんなViewライブリとも使うことができます。
Reduxは軽量です。(依存を含め、2kB)

[![build status](https://img.shields.io/travis/reactjs/redux/master.svg?style=flat-square)](https://travis-ci.org/reactjs/redux)
[![npm version](https://img.shields.io/npm/v/redux.svg?style=flat-square)](https://www.npmjs.com/package/redux)
[![npm downloads](https://img.shields.io/npm/dm/redux.svg?style=flat-square)](https://www.npmjs.com/package/redux)
[![redux channel on discord](https://img.shields.io/badge/discord-%23redux%20%40%20reactiflux-61dafb.svg?style=flat-square)](https://discord.gg/0ZcbPKXt5bZ6au5t)
[![#rackt on freenode](https://img.shields.io/badge/irc-%23rackt%20%40%20freenode-61DAFB.svg?style=flat-square)](https://webchat.freenode.net/)
[![Changelog #187](https://img.shields.io/badge/changelog-%23187-lightgrey.svg?style=flat-square)](https://changelog.com/187)

>**Reduxを開発者から学ぶ:**  
>**[パート１： Reduxを始めよう（Part 1: Getting Started with Redux）](https://egghead.io/series/getting-started-with-redux) （３０本の無料動画）**<br>
>**[パート２： Reduxの特徴的な書き方でアプリケーションを作ろう (Part 2: Building React Applications with Idiomatic Redux )](https://egghead.io/courses/building-react-applications-with-idiomatic-redux) （２７本の無料動画）**

### 推薦文

>[“Reduxでやっていることが大好き (Love what you're doing with Redux)”](https://twitter.com/jingc/status/616608251463909376)  
>Jing Chen, Fluxの開発者

>[“FaceBook社内の、JSについて議論するグループでReduxについて意見を求めました。そしてどこでも、絶賛されました。本当に素晴らしい仕事です。 (I asked for comments on Redux in FB's internal JS discussion group, and it was universally praised. Really awesome work.)”](https://twitter.com/fisherwebdev/status/616286955693682688)  
>Bill Fisher, Fluxドキュメントの著者

>[“まったくFluxをやらないことで、より良いFluxを発明したのがクール。 (It's cool that you are inventing a better Flux by not doing Flux at all.)”](https://twitter.com/andrestaltz/status/616271392930201604)  
>André Staltz, Cycleの開発者

### 先に進む前に

>**こちらも読んでください:**  
>**[あなたにReduxは必要ないかもしれない (You Might Not Need Redux)](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)**

### 開発者の経験

Ruduxは[“タイムトラベルしながら自動で再読み込みする (Hot Reloading with Time Travel)”](https://www.youtube.com/watch?v=xsSnOQynTHs)という、Reactヨーロッパの講演で活動しながら書きました。目的は、最小のAPIでありながら完全に予測可能なふるまいをする状態管理ライブラリです。このライブラリで、次のことができます。開発者から、何も買わなくても。
* ログをとる
* 自動で再読み込み
* タイムトラベル
* ユニバーサルアプリ
* 記録や再生

### 影響

Reduxは[Flux](http://facebook.github.io/flux/)の考えを発展させます。しかし、Fluxの複雑さは排除します。そのための手がかりを、[Elm](https://github.com/evancz/elm-architecture-tutorial/)から得ました。
Elmを使ったことがなくても、Reduxは始めるのに数分しか掛かりません。

### インストール

安定したバージョンをインストールする：

```
npm install --save redux
```

これはパッケージマネージャとして、[npm](https://www.npmjs.com/)を使っていることが前提です。

もし使っていないなら、[unpkgにあるファイルへアクセスして](https://unpkg.com/redux/)ダウンロードするか、 お使いのパッケージマネージャでこれらのファイルを指定できます。

Reduxがもっともよく読み込まれているのは、[CommonJS](http://webpack.github.io/docs/commonjs.html)モジュールのコレクションとしてです。これらのモジュールは、`redux`を[Webpack](https://webpack.js.org/)、 [Browserify](http://browserify.org/)、またはNode.js環境でインポートすると利用できます。 もし最先端でいるのが好きなら、[Rollup](http://rollupjs.org)を使うこともできます。 これも同様に対応しています。

上記のようなモジュールバンドラを使っていなくても、大丈夫です。`redux` npmパッケージには、あらかじめコンパイルされた本番と開発用の[UMD](https://github.com/umdjs/umd)ビルドが同梱されています。 場所は[`dist` フォルダ](https://unpkg.com/redux/dist/)です。バンドラなしで直接使えますし、人気のあるJavaScriptのモジュールローダーや環境の多くと互換性があります。 例えばWebページに[`<script>`タグ](https://unpkg.com/redux/dist/redux.js)で、UMDビルドを読み込むことができます。または、[Bowerでインストールする (tell Bower to install it)](https://github.com/reactjs/redux/pull/1181#issuecomment-167361975)ことも。 UMDビルドは、 Reduxを`window.Redux`というグローバル変数で利用できるようにします。

ReduxのソースコードはES2015で書かれています。しかしCommonJSとUMDビルドの両方で、ES5にあらかじめコンパイルされています。そのため[すべてのモダンブラウザ (any modern browser)](http://caniuse.com/#feat=es5)で動きます。[Reduxを始める (get started with Redux)](https://github.com/reactjs/redux/blob/master/examples/counter-vanilla/index.html)ために、Babelやモジュールバンドラを使う必要はありません。

#### 補完パッケージ

ほとんどの場合、[Reactバインディング (the React bindings)](https://github.com/reactjs/react-redux)と[開発者ツール (the developer tools)](https://github.com/gaearon/redux-devtools)も必要です。

```
npm install --save react-redux
npm install --save-dev redux-devtools
```

Redux自体と違い、Reduxとバインディング（連携）する多くのパッケージはUMDビルドを提供していません。そのため最も快適な開発を体験するためには、CommonJSのモジュールバンドラをおすすめします。[Webpack](https://webpack.js.org/)や[Browserify](http://browserify.org/)などです。

### 要点

アプリのすべての状態は、1つの *Store* 内にある1つのオブジェクトツリーで保持されます。状態ツリーを変化させる唯一の方法は、*Action* を送ることです。Actionは、何が起きたのかを説明するオブジェクトです。どのようにActionが状態ツリーを転換するか明示するために、純粋な（副作用を起こさない）*Reducer* を書きます。

これだけです！

```js
import { createStore } from 'redux'

/**
 * これはReducerです。状態とActionを引数に取り、新たな状態を返す純粋関数です。(state, action) => state
 * Actionによって、状態がどのように新たな状態へ転換されるかを説明しています。
 *
 * 状態の形はあなた次第です。最小限にすることも、配列やオブジェクトにすることもできます。
 * または、Immutable.jsのデータ構造であっても構いません。
 * ただ一つ重要なことは、状態オブジェクトを書き換えるべきではないということです。
 * その代わり、状態が変化したら新たなオブジェクトを返します。
 *
 * この例では、`switch`文と文字列を使います。
 * しかしプロジェクトと整合性があるなら、他のよくある書き方に従ってヘルパーを使うこともできます。
 * たとえば、map関数のような。
 */
function counter(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1
  case 'DECREMENT':
    return state - 1
  default:
    return state
  }
}

// アプリの状態を保持するReduxのStoreをつくります。
// APIは、 { subscribe, dispatch, getState }。
let store = createStore(counter)

// 状態の変化に応じてUIを更新するためにsubscribe()が使えます。
// たいていはsubscribe()を直接呼び出すのではなく、Viewとバインディング（連携）するライブラリを使います。(例： React Redux)。
// しかし、現在の状態をローカルストレージに保持するのも簡便です。

store.subscribe(() =>
  console.log(store.getState())
)

// 内部状態を変更する唯一の方法は、Actionを送ることです。
// Actionはシリアライズ（訳注：データをネットワークで送受信できるように変換すること。直列化）、ログをとる、また保持してもう一度実行などができます。
store.dispatch({ type: 'INCREMENT' })
// 1
store.dispatch({ type: 'INCREMENT' })
// 2
store.dispatch({ type: 'DECREMENT' })
// 1
```

状態を直接変更してはいけません。代わりに *Action* という単なるオブジェクトで、起こしたい変更を明示します。そして *Reducer* という特別な関数を書きます。この関数で、アプリ全体の状態をそれぞれのActionがどのように転換するか決めます。

Fluxを書いたことがあるなら、理解しなければならない1つの重要な違いがあります。それはReduxがDispatcherを持たない、言い換えれば多数のStoreに対応しないということです。代わりに1つのルート（大元）となるReduce関数と、ただ1つのStoreがあります。アプリが大きくなっても、ストアは追加しません。代わりにルートReducerを、複数のより小さなReducerに分割します。これらのReducerは、状態ツリーの異なる部分を独自に処理します。 これはまさにちょうど、Reactアプリで1つのルートコンポーネントを持つようなものです。しかし実際には、たくさんの小さなコンポーネントで構成されているのです。

このアーキテクチャ（構成）は、計数アプリのためにはやり過ぎだと思われるかもしれません。しかしこのパターンの美しさは、とても上手に大きく複雑なアプリへ拡張できることです。また、とても強力な開発者ツールも使えます。なぜなら、アクションを起点としたすべての変更を追跡できるからです。 ユーザーのセッションを記録することや、Actionを再実行してもう一度作ることもできます。

### Reduxを開発者から学ぶ

[Reduxを始めよう (Getting Started with Redux)](https://egghead.io/series/getting-started-with-redux)は、無料の動画コースです。全部で30本あり、Reduxの作者であるDan Abramovが解説しています。ドキュメントの“初級”パートを補足しつつ、いくつかの知見も加えています。不変性、テスト、Reduxのベストプラクティス、そしてReactとともにReduxを使うことについてです。**このコースは無料です。今後も変わらずに。**

>[“@dan_abramov によるegghead.ioの素晴らしいコース - #reduxをどうやって使うかだけじゃない。どうやって、またどうしてReduxが作られたのかを示してくれる。 (Great course on egghead.io by @dan_abramov - instead of just showing you how to use #redux, it also shows how and why redux was built!)”](https://twitter.com/sandrinodm/status/670548531422326785)  
>Sandrino Di Mattia

>[“@dan_abramovの『Reduxを始めよう』をじっくり観ている。 - この動画で、とてもシンプルな概念が理解できることに驚く。 (Plowing through @dan_abramov 'Getting Started with Redux' - its amazing how much simpler concepts get with video.)”](https://twitter.com/chrisdhanaraj/status/670328025553219584)  
>Chris Dhanaraj

>[“@eggheadioにある@dan_abramovによるReduxの動画シリーズは壮観だ！ (This video series on Redux by @dan_abramov on @eggheadio is spectacular!)”](https://twitter.com/eddiezane/status/670333133242408960)  
>Eddie Zaneski

>[“イカした奴がやってきた。揺るぎない原理に則っている。(ありがとう、そして@dan_abramovと@eggheadioはよくやった！) (Come for the name hype. Stay for the rock solid fundamentals. (Thanks, and great job @dan_abramov and @eggheadio!))”](https://twitter.com/danott/status/669909126554607617)  
>Dan

>[“@dan_abramovによるReduxのビデオシリーズに、何度も心を揺さぶられている。いくつか重大なリファクタリングをするつもり。 (This series of videos on Redux by @dan_abramov is repeatedly blowing my mind - gunna do some serious refactoring)”](https://twitter.com/gelatindesign/status/669658358643892224)  
>Laurence Roberts

さぁ、あなたは何をためらっているんですか？

#### [３０本の無料動画を観よう！ (Watch the 30 Free Videos!)](https://egghead.io/series/getting-started-with-redux)

このコースが気に入ったら、[サブスクリプション契約 (buying a subscription)](https://egghead.io/pricing)してEggheadをサポートすることを検討してください。 契約者は動画中の例すべてのソースコードにアクセスできます。加えて、たくさんの上級レッスンもあります。このレッスンにはJavaScriptの深掘り、React、Angular、その他多くが含まれます。多くの[Eggheadインストラクター (Egghead instructors)](https://egghead.io/instructors)は、オープンソースライブラリの作者でもあります。そのためセブスクリプション契約は、彼らがやってきたことに感謝する良い方法です。

### ドキュメント

* [導入](http://redux.js.org/docs/introduction/index.html)
* [初級](http://redux.js.org/docs/basics/index.html)
* [上級](http://redux.js.org/docs/advanced/index.html)
* [レシピ](http://redux.js.org/docs/recipes/index.html)
* [FAQ](http://redux.js.org/docs/FAQ.html)
* [トラブルシューティング](http://redux.js.org/docs/Troubleshooting.html)
* [用語集](http://redux.js.org/docs/Glossary.html)
* [APIレファレンス](http://redux.js.org/docs/api/index.html)

オフラインで読むためのPDF、ePub、そしてMOBIもあります。これらの作り方は、こちらをご覧ください： [paulkogel/redux-offline-docs](https://github.com/paulkogel/redux-offline-docs).

オフラインのためのドキュメントは、 こちらを： [devdocs](http://devdocs.io/redux/)

### 使用例

ほとんどすべての例は、CodeSandboxというサンドボックスに対応しています。オンラインで試せる双方向版のコードです。

* [Counter Vanilla](http://redux.js.org/docs/introduction/Examples.html#counter-vanilla) ([source](https://github.com/reactjs/redux/tree/master/examples/counter-vanilla))
* [Counter](http://redux.js.org/docs/introduction/Examples.html#counter) ([source](https://github.com/reactjs/redux/tree/master/examples/counter), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/counter))
* [Todos](http://redux.js.org/docs/introduction/Examples.html#todos) ([source](https://github.com/reactjs/redux/tree/master/examples/todos), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todos))
* [Todos with Undo](http://redux.js.org/docs/introduction/Examples.html#todos-with-undo) ([source](https://github.com/reactjs/redux/tree/master/examples/todos-with-undo), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todos-with-undo))
* [TodoMVC](http://redux.js.org/docs/introduction/Examples.html#todomvc) ([source](https://github.com/reactjs/redux/tree/master/examples/todomvc), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/todomvc))
* [Shopping Cart](http://redux.js.org/docs/introduction/Examples.html#shopping-cart) ([source](https://github.com/reactjs/redux/tree/master/examples/shopping-cart), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/shopping-cart))
* [Tree View](http://redux.js.org/docs/introduction/Examples.html#tree-view) ([source](https://github.com/reactjs/redux/tree/master/examples/tree-view), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/tree-view))
* [Async](http://redux.js.org/docs/introduction/Examples.html#async) ([source](https://github.com/reactjs/redux/tree/master/examples/async), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/async))
* [Universal](http://redux.js.org/docs/introduction/Examples.html#universal) ([source](https://github.com/reactjs/redux/tree/master/examples/universal))
* [Real World](http://redux.js.org/docs/introduction/Examples.html#real-world) ([source](https://github.com/reactjs/redux/tree/master/examples/real-world), [sandbox](https://codesandbox.io/s/github/reactjs/redux/tree/master/examples/real-world))

NPMのエコシステムが初めてでプロジェクトを立ち上げたり、運営するのに問題を抱えている。あるいは、上記の重要な箇所をどこにペーストすれば良いかよく分からない。そんな方は、 [simplest-redux-example（一番シンプルなReduxの例）](https://github.com/jackielii/simplest-redux-example)を確認してください。Reduxとともに、ReactとBrowserifyを使っています。

### 議論

[Reactiflux](http://www.reactiflux.com)Discordコミュニティの[#redux](https://discord.gg/0ZcbPKXt5bZ6au5t)チャンネルに参加してください。

### 感謝

* [The Elm Architecture](https://github.com/evancz/elm-architecture-tutorial)　Reducerとともに状態更新をするモデルへの素晴らしい導入に
* [Turning the database inside-out](http://www.confluent.io/blog/turning-the-database-inside-out-with-apache-samza/)　心を揺さぶってくれて
* [Developing ClojureScript with Figwheel](https://www.youtube.com/watch?v=j-kj2qwJa_E)　再評価は“ただ動作する”べきだと納得させてくれて
* [Webpack](https://webpack.js.org/concepts/hot-module-replacement/)　自動的なモジュール置き換えに
* [Flummox](https://github.com/acdlite/flummox)　常用文やシングルトンなしにFluxを手がけるよう教えてくれて
* [disto](https://github.com/threepointone/disto)　自動で再読み込みができるStoreの構想を実証してくれて
* [NuclearJS](https://github.com/optimizely/nuclear-js)　このアーキテクチャで高い性能を得られると実証してくれて
* [Om](https://github.com/omcljs/om)　1つの状態原子というアイデアを広めてくれて
* [Cycle](https://github.com/cyclejs/cycle-core) いかに多くの場合に、関数が最善のツールかを示してくれて
* [React](https://github.com/facebook/react)　実用的な革新に

`redux`のNPMパッケージ名を渡してくれた[Jamie Paton](http://jdpaton.github.io)に、特別な感謝を。

### ロゴ

公式ロゴはこちらの [GitHubで](https://github.com/reactjs/redux/tree/master/logo).

### 更新履歴

このプロジェクトは[セマンティック バージョニング (Semantic Versioning)](http://semver.org/)に準拠しています。
すべてのリリースは移行説明とともに、 [リリース (Releases)](https://github.com/reactjs/redux/releases)ページに記録されています。

### パトロン（後援者）

Redux上の作業は[コミュニティによって援助されています。 (funded by the community)](https://www.patreon.com/reactdx).  
これを可能にした、傑出した会社を訪問してください：

* [Webflow](https://github.com/webflow)
* [Ximedes](https://www.ximedes.com/)

[Reduxパトロンの全リストを確認して (See the full list of Redux patrons)](PATRONS.md)ください。 [Reduxを使う人達と会社の (people and companies that use Redux)](https://github.com/reactjs/redux/issues/310)増え続けるリストも。

### ライセンス

MIT
