# Data Flow

Reduxのアーキテクチャは、**厳格な一方向のデータフロー** を中心に設計されています。

アプリケーション内のすべてのデータが、同じライフサイクルパターンをたどるということです。これによりアプリのロジックがより予測しやすく、理解しやすくなります。また、データの正規化が促されます。そのためお互いにつながりが無い、同じデータの複製をいくつも作らなくてすみます。

まだピンとこないなら、[モチベーション](../introduction/Motivation.md)と[Fluxが役立つ場合（The Case for Flux）](https://medium.com/@dan_abramov/the-case-for-flux-379b7d1982c6)を読んでください。一方向のデータフローを支持する、説得力のある主張です。[Reduxは正確にはFluxではない](../introduction/PriorArt.md)のですが、主な利点は同じです。

どんなReduxアプリでも、データのライフサイクルは次の４ステップをたどります：

1. [`store.dispatch(action)`](../api/Store.md#dispatch)**を呼び出す**。

  [Action](Actions.md)は *起きたこと* を表す単なるオブジェクトです。例えば：

   ```js
    { type: 'LIKE_ARTICLE', articleId: 42 }
    { type: 'FETCH_USER_SUCCESS', response: { id: 3, name: 'Mary' } }
    { type: 'ADD_TODO', text: 'Reduxのドキュメントを読む。' }
   ```

  Actionを、とても簡潔なニュースの断片だと考えてください。“メアリーは記事42にいいねした。” とか “‘Reduxのドキュメントを読む。’がTodoリストに追加された。” など。

  アプリのどこからでも[`store.dispatch(action)`](../api/Store.md#dispatch)を呼び出せます。コンポーネント（構成要素）やXHRコールバック、あらかじめ決められた間隔でも呼び出せます。

2. **Redux Storeが、与えられたReducer関数を呼び出す。**

  [Store](Store.md)は、２つの引数を[Reducer](Reducers.md)に渡します。現在の状態ツリーとActionです。例えばTodoアプリで、ルート（大元の）Reducerは次のような状態とActionを受け取るでしょう：

   ```js
    // 現在のアプリケーション状態（Todoのリストと、選択されたフィルター）
    let previousState = {
      visibleTodoFilter: 'SHOW_ALL',
      todos: [
        {
          text: 'ドキュメントを読む。',
          complete: false
        }
      ]
    }

    // 実行されたAction（Todoの追加）
    let action = {
      type: 'ADD_TODO',
      text: '流れを理解する'
    }

    // Reducerは次のアプリケーション状態を返す
    let nextState = todoApp(previousState, action)
   ```

  Reducerは純粋関数であることに注意してください。次の状態を *計算する* だけです。完全に予測可能であるべきです。つまり同じ入力には、何度やっても同じ出力を返すのです。副作用は、どんなものでも行うべきではありません。例えば、API呼び出しやルート遷移など。これらは、Actionを送信する前に行うべきです。

3. **ルートReducerは、複数のReducerの結果を１つの状態ツリーにまとめてよい。**

  ルートReducerをどのように構成するかは、すべてあなた次第です。Reduxは[`combineReducers()`](../api/combineReducers.md)というヘルパー関数を提供しています。ルートReducerを複数の関数へ“分割する”のに役立ちます。それぞれの関数は、 状態ツリーの一部を処理します。

  [`combineReducers()`](../api/combineReducers.md)がどう機能するか示します。ここでは2つのReducerがあります。1つはTodoリストのため、もう1つは今選択されているフィルター設定のためです：

   ```js
    function todos(state = [], action) {
      // 計算は省略...
      return nextState
    }

    function visibleTodoFilter(state = 'SHOW_ALL', action) {
      // 計算は省略...
      return nextState
    }

    let todoApp = combineReducers({
      todos,
      visibleTodoFilter
    })
   ```

  Actionを発行すると、`combineReducers`によって返された`todoApp`は両方のReducerを呼び出します：

   ```js
    let nextTodos = todos(state.todos, action)
    let nextVisibleTodoFilter = visibleTodoFilter(state.visibleTodoFilter, action)
   ```

  そして両方の結果を、1つの状態ツリーにまとめます：

   ```js
    return {
      todos: nextTodos,
      visibleTodoFilter: nextVisibleTodoFilter
    }
   ```

  [`combineReducers()`](../api/combineReducers.md)は手軽なヘルパー関数ですが、使わなくても構いません。ルートReducerの書き方は、あなた次第です！

4. **ReduxはルートReducerによって返された、完全な状態ツリーを保存します。**

  上記で、新しいツリーがアプリの次の状態となりました！[`store.subscribe(listener)`](../api/Store.md#subscribe)で登録されたすべてのリスナーは、このとき呼び出されます。リスナーは[`store.getState()`](../api/Store.md#getState)で現在の状態を得ることもできます。

  そして、UIの更新が可能となりました。現在の新しい状態を反映するためです。[React Redux](https://github.com/gaearon/react-redux)のようなバインディング（連携プログラム）を使っているなら、この時に`component.setState(newState)`が呼び出されます。

## 次のステップ

これで、Reduxがどのように機能するか分かりました。次は[Reactアプリにつなげましょう](UsageWithReact.md)。

>##### 上級ユーザーへの注意
>基本的なコンセプトをよく理解していて、以前にこのチュートリアルを終えた方へ。[上級チュートリアル](../advanced/README.md)の[非同期フロー](../advanced/AsyncFlow.md)を忘れずにチェックしてください。Reducerに届く前に、ミドルウェアが[非同期Action](../advanced/AsyncActions.md)をどう変換するか学べます。
