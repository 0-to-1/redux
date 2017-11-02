# エコシステム

Reduxは小さなライブラリです。しかし決まりごととAPIは注意深く選ばれています。ツールと拡張機能のエコシステムを生み出すためです。

拡張機能の一覧として、[Awesome Redux](https://github.com/xgrommx/awesome-redux)がおすすめです。使用例や常用文、ミドルウェア、ユーティリティのライブラリなどが載っています。 [React/Redux Links](https://github.com/markerikson/react-redux-links)には、 チュートリアルやその他の役立つ資料が掲載されています。ReactやReduxを学ぶ、すべての人に役立ちます。そして[Redux Ecosystem Links](https://github.com/markerikson/redux-ecosystem-links)は、多くのReduxに関連したライブラリとアドオンを列挙しています。

このページでは、Reduxのメンテナ（保守管理する人）たちが個人的に調査したものだけを紹介するつもりです。だからと言って、その他のものを試してみる気をなくさないでください！エコシステムはあまりに早く成長しているし、私たちの時間は限られています。そのため、すべてを確認することはできません。 これら“スタッフ選定”を検討し、Reduxとともに何かすごいものを作ったら、ためらわずにPR（プルリクエスト）を送ってください。

## Reduxを学ぶ

### 動画

* **[Reduxを始めよう (Getting Started with Redux)](https://egghead.io/series/getting-started-with-redux)** — 作者から直接、Reduxの基本を学びます。 (30本の無料動画）
* **[Reduxを学ぶ (Learn Redux)](https://learnredux.com)** — シンプルな写真アプリを作ります。Redux、React Router、そしてReact.jsの背後にある、核となるアイデアを分かりやすくしています。

### アプリ使用例

* [公式使用例 (Official Examples)](Examples.md) — 公式の使用例です。それぞれ異なるReduxのテクニックを取り扱っています。
* [SoundRedux](https://github.com/andrewngu/sound-redux) — Reduxで作られたSoundCloudのクライアントアプリです。
* [grafgiti](https://github.com/mohebifar/grafgiti) — GitHubのコントリビューションウォール（訳注：日本では「草」や「芝生」などと呼ばれている）で落書きをつくります。
* [React-lego](https://github.com/peter-mouland/react-lego) — 一度に1つの機能だけ、Reactに組み込む手順。（訳注：簡単に比較できるよう、GitHubのブランチごとに様々なReact関連技術を追加している）

### チュートリアルと記事

* [Redux Tutorial](https://github.com/happypoulp/redux-tutorial)
* [Redux Egghead Course Notes](https://github.com/tayiorbeii/egghead.io_redux_course_notes)
* [Integrating Data with React Native](http://makeitopen.com/tutorials/building-the-f8-app/data/)
* [What the Flux?! Let's Redux.](https://blog.andyet.com/2015/08/06/what-the-flux-lets-redux)
* [Leveling Up with React: Redux](https://css-tricks.com/learning-react-redux/)
* [A cartoon intro to Redux](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6)
* [Understanding Redux](http://www.youhavetolearncomputers.com/blog/2015/9/15/a-conceptual-overview-of-redux-or-how-i-fell-in-love-with-a-javascript-state-container)
* [Handcrafting an Isomorphic Redux Application (With Love)](https://medium.com/@bananaoomarang/handcrafting-an-isomorphic-redux-application-with-love-40ada4468af4)
* [Full-Stack Redux Tutorial](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html)
* [Getting Started with React, Redux, and Immutable](http://www.theodo.fr/blog/2016/03/getting-started-with-react-redux-and-immutable-a-test-driven-tutorial-part-2/)
* [Secure Your React and Redux App with JWT Authentication](https://auth0.com/blog/2016/01/04/secure-your-react-and-redux-app-with-jwt-authentication/)
* [Understanding Redux Middleware](https://medium.com/@meagle/understanding-87566abcfb7a)
* [Angular 2 — Introduction to Redux](https://medium.com/google-developer-experts/angular-2-introduction-to-redux-1cf18af27e6e)
* [Apollo Client: GraphQL with React and Redux](https://medium.com/apollo-stack/apollo-client-graphql-with-react-and-redux-49b35d0f2641)
* [Using redux-saga To Simplify Your Growing React Native Codebase](https://shift.infinite.red/using-redux-saga-to-simplify-your-growing-react-native-codebase-2b8036f650de)
* [Build an Image Gallery Using Redux Saga](http://joelhooks.com/blog/2016/03/20/build-an-image-gallery-using-redux-saga)
* [Working with VK API (in Russian)](https://www.gitbook.com/book/maxfarseer/redux-course-ru/details)

### 講演

* [ライブリアクト： 自動再読み込みとタイムトラベル (Live React: Hot Reloading and Time Travel)](http://youtube.com/watch?v=xsSnOQynTHs) — Reduxによって課された制約がどのようにタイムトラベル（時間の行き来）と自動再読み込みを簡単にするかを見る
* [ヤニをとる： Firefoxの開発者ツール内でReactを使う (Cleaning the Tar: Using React within the Firefox Developer Tools)](https://www.youtube.com/watch?v=qUlRpybs7_c) — 既存のMVCアプリケーションを段階的にReactへ移行する方法を学ぶ
* [Redux： アプリケーションの状態を分かりやすくする (Redux: Simplifying Application State)](https://www.youtube.com/watch?v=okdC5gcD-dM) — Reduxアーキテクチャの導入

## Reduxを使う

### バインディング（連携プログラム）

* [react-redux](https://github.com/gaearon/react-redux) — React
* [ng-redux](https://github.com/wbuchwalter/ng-redux) — Angular
* [ng2-redux](https://github.com/wbuchwalter/ng2-redux) — Angular 2
* [backbone-redux](https://github.com/redbooth/backbone-redux) — Backbone
* [redux-falcor](https://github.com/ekosz/redux-falcor) — Falcor
* [deku-redux](https://github.com/troch/deku-redux) — Deku
* [polymer-redux](https://github.com/tur-nr/polymer-redux) - Polymer
* [ember-redux](https://github.com/toranb/ember-redux) - Ember.js

### ミドルウェア

* [redux-thunk](http://github.com/gaearon/redux-thunk) — 非同期Actionクリエイターを書く、最も簡単な方法
* [redux-promise](https://github.com/acdlite/redux-promise) — [FSA](https://github.com/acdlite/flux-standard-action)に対応したPromise（プロミス）のミドルウェア
* [redux-axios-middleware](https://github.com/svrcekmichal/redux-axios-middleware) — axiosのHTTPクライアントでデータ取得するReduxのミドルウェア
* [redux-observable](https://github.com/redux-observable/redux-observable/) — "Epics"を使った、Actionの副作用のためのRxJSのミドルウェア
* [redux-cycles](https://github.com/cyclejs-community/redux-cycles) — Cycle.jsを使ってReduxの非同期アクションを処理
* [redux-logger](https://github.com/fcomb/redux-logger) — すべてのReduxアクションと次の状態のログを取る
* [redux-immutable-state-invariant](https://github.com/leoasis/redux-immutable-state-invariant) — 開発中に、状態変更を警告する
* [redux-unhandled-action](https://github.com/socialtables/redux-unhandled-action) — 開発中に、状態変更を引き起こさないActionを警告する
* [redux-analytics](https://github.com/markdalgleish/redux-analytics) — Reduxのための解析ミドルウェア
* [redux-gen](https://github.com/weo-edu/redux-gen) — Reduxのためのジェネレータのミドルウェア
* [redux-saga](https://github.com/yelouafi/redux-saga) — Reduxのための、別の副作用モデル
* [redux-action-tree](https://github.com/cerebral/redux-action-tree) — Reduxのための、合成可能なCerebral形式のシグナル
* [apollo-client](https://github.com/apollostack/apollo-client) — シンプルなキャッシュクライアント。Redux上でつくられた全てのGraphQLとUIフレームワークに対応

### ルーティング（ページ遷移）

* [react-router-redux](https://github.com/reactjs/react-router-redux) — ReactルータとReduxの同期を保つ、徹底してシンプルなバインディング（連携プログラム）
* [redial](https://github.com/markdalgleish/redial) — ユニバーサルなデータ取得やルートのライフサイクルをReactのために管理する。Reduxとの相性も良い

### コンポーネント（構成要素）

* [redux-form](https://github.com/erikras/redux-form) — ReduxでReactフォームの状態を保つ
* [react-redux-form](https://github.com/davidkpiano/react-redux-form) — ReduxでReactのフォームを簡単に作る
* [redux-resource](https://github.com/jmeas/redux-resource) — Reduxでリモートサーバのリソースを管理する

### エンハンサー（強化材）

* [redux-batched-subscribe](https://github.com/tappleby/redux-batched-subscribe) — ストアの購読者のために、一括処理やデバウンシングした呼び出し（訳注：抑制した呼び出し。例えば、最大で○秒に1回しか呼び出されないようにする）をカスタマイズ
* [redux-history-transitions](https://github.com/johanneslumpe/redux-history-transitions) — 任意のアクションに基づいたルート遷移
* [redux-optimist](https://github.com/ForbesLindesay/redux-optimist) — あとでコミットされるか、または元に戻される可能性のあるActionを楽観的に適用する
* [redux-optimistic-ui](https://github.com/mattkrick/redux-optimistic-ui) — 型にとらわれず（訳注：JSのオブジェクトだけでなく、 immutable.jsなどでも良い）、楽観的に更新するReducerのエンハンサー
* [redux-undo](https://github.com/omnidan/redux-undo) —  Ruducerのための手っ取り早い、元に戻す/やり直す（Undo/Redo)とAction履歴
* [redux-ignore](https://github.com/omnidan/redux-ignore) — 配列かフィルター関数によって、ReduxのActionを無視（訳注：特定のActionを無視することで、パフォーマンスの問題を回避）
* [redux-recycle](https://github.com/omnidan/redux-recycle) — 特定のActionに対して、Reduxの状態をリセット
* [redux-batched-actions](https://github.com/tshelburne/redux-batched-actions) — 1つの購読者通知で、複数のActionを送信
* [redux-search](https://github.com/treasure-data/redux-search) —  Web Workerで自動的にリソースをインデックスし、ブロッキングなしで検索
* [redux-electron-store](https://github.com/samiskin/redux-electron-store) — Electronの複数プロセス間でReduxのStoreを同期するStoreエンハンサー
* [redux-loop](https://github.com/raisemarketplace/redux-loop) — Reducerから返すことで、純粋かつ自然に作用を並べる（訳注：Reducerは通常、同期的な状態遷移のみを扱う。つまり、Actionに対してReducerから新しい状態が返される。しかしredux-loopが加えられたReducerは、Actionに対して非同期関数の実行も定義できる。そしてその関数の結果次第で、新たなActionを呼び出す。ただし通常のReducerと同じく、最終的に返すのは状態としての単なるオブジェクト）
* [redux-side-effects](https://github.com/salsita/redux-side-effects) — Generatorを利用して、純粋なReducerから宣言の形で副作用をyieldする

### ユーティリティ（便利プログラム）

* [reselect](https://github.com/faassen/reselect) — NuclearJSに触発された効率的な生成データのセレクタ
* [normalizr](https://github.com/paularmstrong/normalizr) — Reducerが簡単に受け取って処理できるように、ネストされたAPIレスポンスを標準化
* [redux-actions](https://github.com/acdlite/redux-actions) — ReducerとAction Creatorを書くときに、常用文を減らす（訳注：書かないといけないコード量を削減する）
* [redux-act](https://github.com/pauldijou/redux-act) — ReducerとAction Creatorを作るためのライブラリ
* [redux-transducers](https://github.com/acdlite/redux-transducers) — Reduxのための変換ユーティリティ
* [redux-immutable](https://github.com/gajus/redux-immutable) — Reduxの`combineReducers`と同等で、[Immutable.js](https://facebook.github.io/immutable-js/)の状態と協働する関数
* [redux-tcomb](https://github.com/gcanti/redux-tcomb) — Reduxのための、不変で型検査された状態とAction
* [redux-mock-store](https://github.com/arnaudbenard/redux-mock-store) — アプリをテストするため、ReduxのStoreをモック
* [redux-actions-assertions](https://github.com/dmitry-zaets/redux-actions-assertions) — ReduxのActionをテストするためのアサーション
* [redux-bootstrap](https://github.com/remojansen/redux-bootstrap) — Reduxアプリケーションのための、ブートストラッピング関数（訳注：StoreやReducerの作成などを自動化）
* [redux-data-structures](https://redux-data-structures.js.org/) — Reducerファクトリ（高階関数）。Counter、Map、List（キュー、スタック）、Setなど、共通処理を扱うReducerが作れる

### 開発者ツール

* [Redux DevTools](http://github.com/gaearon/redux-devtools) — タイムトラベルUIの付いたActionロガー、Reducerのための自動再読み込みとエラーハンドリング。 [Reactヨーロッパで初めてデモされた (first demoed at React Europe)](https://www.youtube.com/watch?v=xsSnOQynTHs)
* [Redux DevTools Extension](https://github.com/zalmoxisus/redux-devtools-extension) — Redux開発者ツールをラップして追加機能を提供する、Chromeの拡張機能

### 開発者ツールのモニター

* [Log Monitor](https://github.com/gaearon/redux-devtools-log-monitor) — Redux開発者ツールのデフォルトモニター。ツリー状の表示ができる
* [Dock Monitor](https://github.com/gaearon/redux-devtools-dock-monitor) — Redux開発者ツールのモニターのための、サイズ変更と移動が可能なDock
* [Slider Monitor](https://github.com/calesce/redux-slider-monitor) — Redux開発者ツールのためのカスタムモニター。記録されたActionを再生する
* [Inspector](https://github.com/alexkuz/redux-devtools-inspector) — Redux開発者ツールのためのカスタムモニター。Actionのフィルタリング、差分の調査、深くネストされた状態の変更監視のために状態のパスをピン留めしたりできる
* [Diff Monitor](https://github.com/whetstone/redux-devtools-diff-monitor) — Redux開発者ツールのためのモニター。Action間でのStoreの差分が取れる
* [Filterable Log Monitor](https://github.com/bvaughn/redux-devtools-filterable-log-monitor/) — Redux開発者ツールのためのモニター。フィルタリング可能なツリー状の表示ができる
* [Chart Monitor](https://github.com/romseguy/redux-devtools-chart-monitor) — Redux開発者ツールのためのモニター。チャート表示ができる
* [Filter Actions](https://github.com/zalmoxisus/redux-devtools-filter-actions) — Redux開発者ツールのための組み立てできる（訳注：他のモニターを内包できる）モニター。Actionをフィルタリングできる


### コミュニティ規約

* [Flux Standard Action](https://github.com/acdlite/flux-standard-action) — Flux Actionオブジェクトのための、 人に優しい基準
* [Canonical Reducer Composition](https://github.com/gajus/canonical-reducer-composition) — ネストされたReducer合成のための、独自の意見を持った基準
* [Ducks: Redux Reducer Bundles](https://github.com/erikras/ducks-modular-redux) — Reducer、Action Type、Actionを束ねる提案

### 翻訳

* [中文文档](http://camsong.github.io/redux-in-chinese/) — 中国語
* [繁體中文文件](https://github.com/chentsulin/redux) — 繁体字中国語
* [Redux in Russian](https://github.com/rajdee/redux-in-russian) — ロシア語
* [Redux en Español](http://es.redux.js.org/) — スペイン語

## その他

* [Awesome Redux](https://github.com/xgrommx/awesome-redux) はReduxに関連したリポジトリの、広範囲なリストです。
* [React-Redux Links](https://github.com/markerikson/react-redux-links) はReact、Redux、ES6などについて、高品質な記事やチュートリアル、関連コンテンツなどが集められています。
* [Redux Ecosystem Links](https://github.com/markerikson/redux-ecosystem-links)  はReduxに関連したライブラリ、アドオン、ユーティリティを分類したコレクションです。
