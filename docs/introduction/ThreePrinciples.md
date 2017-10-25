# ３つの原則

Reduxは３つの基本的な原則で説明できます:

### 真実の出どころは１つ

**アプリケーション全体の[状態](../Glossary.md#state)。それは１つの[Store](../Glossary.md#store)の中にある、１つのオブジェクトツリーで保持されます。**

これはユニバーサルアプリの制作を簡単にします。なぜならサーバからの状態は、クライアントへシリアライズ（訳注：データをネットワークで送受信できるように変換する。直列化）して溶け込ませることができるからです。余分なコーディングの労力は要りません。 １つの状態ツリーは、デバッグや詳細調査も簡単にします。開発中のアプリの状態に一貫性が出るので、より早い開発サイクルも保てます。またこれまで、実行が難しかった機能があります。例えば、元に戻す/やり直し(Undo/Redo)です。これも突然、実行が簡単になります。なぜなら、すべての状態が１つのツリーで保持されるからです。

```js
console.log(store.getState())

/* Prints
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Reduxの使用を考える',
      completed: true,
    },
    {
      text: '１つのツリーですべての状態を保持する',
      completed: false
    }
  ]
}
*/
```

### 状態は読み込み専用

**状態を変える唯一の方法は、[Action](../Glossary.md#action)を送ることです。Actionは、何が起きたかを記述するオブジェクトです。**

これは、決して状態に直接書き込まないことを保証します。ビューだろうと、ネットワークのコールバックだろうと。その代わり、状態を転換する意図を表します。 すべての変化は集約され、厳密な順番で一つづつ実行されます。気をつけて見なければならない、捉えにくい競合状態はまったくないのです。Actionは単なるオブジェクトです。そのためログをとったり、直列化したり、保管したり、デバックやテストのためにもう一度実行したりできます。

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

### 変化は純粋（副作用のない）関数でつくる

**Actionによってどのように状態ツリーが転換されるか明示するために、純粋な[Reducer](../Glossary.md#reducer)を書きます。**

Reducersはただの純粋関数です。前の状態とActionを引数に取り、次の状態を返します。忘れないで欲しいのは、前の状態を変更するのではなく、新しい状態オブジェクトを返すということです。まず１つのReducerから始めて、アプリが大きくなるにつれて小さく分割できます。この小さな複数のReducerは、それぞれ状態ツリーの特定部分を管理します。 Reducerはただの関数です。そのため呼び出す順番を制御したり、追加データを渡すこともできます。あるいはページ繰り（ページング）のような共通タスクのために、再利用可能なReducerをつくることもできます。

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
const reducer = combineReducers({ visibilityFilter, todos })
const store = createStore(reducer)
```

おしまいです！これでReduxの概要がすべて分かりました。
