# 非同期なフロー

[ミドルウェア](Middleware.md)がなければ、[同期的なデータフロー](../basics/DataFlow.md)だけに対応します。これは[`createStore()`](../api/createStore.md)を、特に設定せず使った場合です。

[`applyMiddleware()`](../api/applyMiddleware.md)により、[`createStore()`](../api/createStore.md)の性能を高めることができます。絶対に必要というわけではありませんが、[簡潔なやり方で非同期なActionを表現](AsyncActions.md)できます。

[redux-thunk](https://github.com/gaearon/redux-thunk)や[redux-promise](https://github.com/acdlite/redux-promise)などの非同期なミドルウェアは、Storeの[`dispatch()`](../api/Store.md#dispatch)メソッドをラップ（内包）します。Action以外のもの、例えば関数やPromiseをDispatch（送信）できるようにするのです。どんなミドルウェアも、Dispatchされたものを解釈・実行します。そして順番に、チェーン内にある次のミドルウェアへActionを渡します。例えば、PromiseのミドルウェアはPromiseを途中でつかまえます。そして、それぞれのPromiseに応じて開始/終了というActionのペアを、非同期にDispatchします。

チェーン内にある最後のミドルウェアがActionをDispatchするさい、そのActionは普通のObjectである必要があります。このときに、[同期的なReduxデータフロー](../basics/DataFlow.md)が行われます。

[非同期を用いた例の、ソースコード完全版](ExampleRedditAPI.md)を確認してください。

## 次のステップ

ここまで、Reduxのミドルウェアができることの一例を見てきました。次は実際にミドルウェアがどう機能し、そして自分で作るにはどうすれば良いかを学びましょう。詳しく[ミドルウェア](Middleware.md)を学べるセクションへ進みましょう。
