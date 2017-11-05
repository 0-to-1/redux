# 先行技術

Reduxは先行する技術を組み合わせています。いくつかのパターンや技術に似ているということです。しかし、そのやり方において重要な違いもあります。 下記で、類似点と相違点をいくつか見てみましょう。

### Flux

Reduxは[Flux](https://facebook.github.io/flux/)の実装だと考えられますか？  
[Yes](https://twitter.com/fisherwebdev/status/616278911886884864)、 そして [No](https://twitter.com/andrestaltz/status/616270755605708800)。

( [Fluxの製作者たち](https://twitter.com/jingc/status/616608251463909376)は[賛同しています](https://twitter.com/fisherwebdev/status/616286955693682688)。 だからもしこれが知りたいことのすべてなら、心配しないで）

Reduxは、Fluxの重要な特質に触発されています。Fluxと同じく、Reduxもアプリケーションの一定の層にモデルの更新ロジックを集約するよう定めています。(Fluxでは“Store”、Reduxでは“Reducer”)
どちらもアプリケーションのコードに直接データを変更させるのではなく、Actionという単なるオブジェクトにすべての変更を記述します。

Fluxと違って、ReduxにはDispatcher(ディスパッチャー)という概念はありません。なぜならイベントエミッターではなく、純粋関数を使っているからです。純粋関数は合成が簡単で、管理するのに追加のエンティティがいりません。Fluxをどう捉えるかによりますが、ReduxがFluxの逸脱や具体的な実装に見えるかもしれません。Fluxはよく、[状態とActionを取り、新たな状態を返すと説明されます (`(state, action) => state`)](https://speakerdeck.com/jmorrell/jsconf-uy-flux-those-who-forget-the-past-dot-dot-dot-1)。この意味では、ReduxはFluxアーキテクチャだと言えます。しかし純粋関数のおかげで、よりシンプルになっています。

もう1つFluxとの重要な違いがあります。それは、**Reduxはデータを決して書き換えないと想定している** ということです。状態のために単なるオブジェクトや配列を使うのは構いません。しかしReducerの内部で状態に手を加えることは、強く反対します。 状態を書き換えるのではなく、常に新しいオブジェクトを返すべきです。これは[ECMAScriptで提案されている、オブジェクトのスプレッド演算子 (object spread operator proposal)](../recipes/UsingObjectSpreadOperator.md)や、[Immutable](https://facebook.github.io/immutable-js)のようなライブラリを使えば簡単です。

パフォーマンスが求められる場合、[純粋でないReducerを書く (write impure reducers)](https://github.com/reactjs/redux/issues/328#issuecomment-125035516)ことは技術的には *可能* です。しかし強く反対します。タイムトラベル（元に戻す/やり直す）や記録/再生、自動再読み込みなどの開発機能は、すべて壊れてしまいます。 さらに実際のアプリではほとんどの場合、不変性がパフォーマンスの問題になるようなことは、ほとんどありません。[Om](https://github.com/omcljs/om)が実証したように、オブジェクトの割り当てで遅れをとったとしても、まだ優位に立てます。なぜならReducerが純粋なおかげで、何が変化したかはっきり分かります。そのためコストの高い再描画や再計算を避けられるからです。

### Elm

[Elm](http://elm-lang.org/)は関数型のプログラミング言語です。Haskellに触発され、[Evan Czaplicki](https://twitter.com/czaplic)によって作られました。 [モデルビューの更新アーキテクチャ(a “model view update” architecture)](https://github.com/evancz/elm-architecture-tutorial/)を強制します。 これは更新が、次の形式に従うということです： `(action, state) => state`。 Elmの “Updater”は、ReduxのReducerと同じ目的を担います。

Reduxと違い、Elmは言語です。そのために多くの恩恵が得られます。純粋性の強制や、静的型付、枠を超えた不変性、そして（`case`表現を使った）パターンマッチングなど。たとえElmを使うつもりがなくても、Elmのアーキテクチャは読んで試してみるべきです。

<!-- textlint-disable preset-japanese/sentence-length -->
興味深い、[同様のアイデアを実装したJavaScriptライブラリを試す場所 (JavaScript library playground implementing similar ideas)](https://github.com/paldepind/noname-functional-frontend-framework)があります。 Reduxのひらめきのために、ここを見るべきです！
<!-- textlint-enable preset-japanese/sentence-length -->

Elmの静的型付に慣れる、1つの方法があります。それは [Flowのような、ゆるやかな型付けの解決策を使うこと (using a gradual typing solution like Flow)](https://github.com/reactjs/redux/issues/290)です。

### Immutable

[Immutable](https://facebook.github.io/immutable-js)はJavaScriptのライブラリです。一貫性のあるデータ構造を実装しています。パフォーマンスが高く、慣用的なJavaScriptのAPIを備えています。

Immutableや他の類似ライブラリのほとんどは、Reduxに影響しません。遠慮なく使ってください！

**Reduxは *どのように* 状態が保持されるか気にしません。単なるオブジェクトでもいいし、Immutableのオブジェクトや、他の何でも構いません。** ユニバーサルアプリを書くためには、おそらく（デ）シリアライゼーション（訳注：シリアライゼーションとは、データをネットワークで送受信できるように変換すること。直列化。 デシリアライゼーションとは、変換されたデータをソフトが読めるように復元すること。非直列化。
）の仕組みが欲しくなるでしょう。サーバーからの状態を溶け込ませるためです。しかし *不変性に対応している限り*、それ以外にどんなデータストレージを使っても構いません。例えば、Reduxの状態としてBackboneを使うのは理にかなっていません。なぜならBackboneのモデルは変更されるからです。

注意することがあります。それは、たとえ不変性のあるライブラリがカーソルに対応していたとしても、Reduxのアプリでは使うべきではないということです。すべての状態ツリーは読み込み専用と考えるべきです。状態ツリーを直接変更するのではなく、Reduxを状態更新や、更新の購読のために使うのです。そのためReduxでカーソルから書き込みをするのは理にかなっていません。

**UIツリーから状態ツリーを分離して、段階的にカーソルを改良する。これがカーソルを使う唯一のユースケースなら、代わりにセレクターを考慮すべきです。** セレクターは、合成可能なGetter関数です。[reselect](http://github.com/faassen/reselect)は合成可能なセレクターを本当に素晴らしく、また簡潔に実装しています。

### Baobab

[Baobab](https://github.com/Yomguithereal/baobab)は、ふつうのJavaScriptオブジェクトを更新するために不変性を持ったAPIを実装している、もう1つの人気ライブラリです。 Reduxとともに使えますが、一緒に使う利点はほとんどありません。

Baobabが用意しているほとんどの機能は、カーソルでデータを更新することに関連しています。しかしReduxでデータを更新する唯一の方法は、アクションを送ることです。つまりBaobabとReduxは、同じ問題を異なる方法で解決しています。そしてこの2つは、補い合うことができません。

Immutableとは違い、Baobabは内部で特別に効率的なデータ構造をまだ何も実装していません。そのためReduxと一緒に使っても、本当に何も得るものはありません。 ただ単にふつうのオブジェクトを使った方が簡単です。

### RxJS

<!-- textlint-disable preset-japanese/sentence-length -->
[RxJS](https://github.com/ReactiveX/RxJS)は非同期的なアプリの複雑性を管理する見事な方法です。実際、[ライブラリを作ろうと努力されています。このライブラリは互いに依存したObservableとして、人間とコンピュータの相互作用を形づくります。(there is an effort to create a library that models human-computer interaction as interdependent observables)](http://cycle.js.org)
<!-- textlint-enable preset-japanese/sentence-length -->

RxJSとReduxを一緒に使う意味はあるでしょうか？　もちろん！素晴らしく協働してくれます。例えば、ReduxのStoreをObservableとして置くのは簡単です：

```js
function toObservable(store) {
  return {
    subscribe({ next }) {
      const unsubscribe = store.subscribe(() => next(store.getState()))
      next(store.getState())
      return { unsubscribe }
    }
  }
}
```

同様に、異なる非同期ストリームをActionへ変えるために合成することもできます。`store.dispatch()`へ送る前に行います。

疑問： すでにRxを使っているなら、Reduxは本当に必要？　たぶん必要ありません。[RxでReduxを再実装する (re-implement Redux in Rx)](https://github.com/jas-chen/rx-redux)のは、難しくありません。Rxの`.scan()`メソッドを使った2系統だという人もいます（訳注：2つのストリームという意味か？1つはActionをStoreに流す、もう1つはStoreの状態変更をビューに流す）。 それはとても良いかも！

嘘だと思うなら、Reduxのソースコードを確認してください（そんなに違いはありません）。エコシステムも同様です（例えば、[開発者ツール (the developer tools)](https://github.com/gaearon/redux-devtools))。こういったことをあまり気にかけず、ずっとリアクティブなデータフローでやりたいなら、代わりに何か他を調べても良いでしょう。[Cycle](http://cycle.js.org)などがあります。Reduxと組み合わせても良いかもしれません。どうなるか教えてください！
