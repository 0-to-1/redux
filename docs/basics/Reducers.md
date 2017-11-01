# Reducer

[Action](./Actions.md) は、*何かが起きた* ということを示します。
しかしアクションに対して、どのようにアプリケーションの状態を変化させるかは明示しません。それはReducerのやることです.

## 状態の形をデザインする

Reduxでは、すべてのアプリケーションの状態は一つのオブジェクトとして保持されます。
コードを書く前に、状態の形について考えるのは良いアイデアです。一つのオブジェクトとして、あなたのアプリケーションの状態をとても簡単に表現するにはどうすれば良いでしょう？

私たちのTodoアプリでは、２つの異なる情報を保持します:

* いま選択されているフィルター
* 実際のTodoリスト

状態ツリーで、何かのデータとUIの状態を保持したいことがよくあると思います。それは構いません。ただ、データとUIの状態は常に分けるようにしましょう。

```js
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```

>##### 参照についての注意

>より複雑なアプリでは、異なるエンティティが互いを参照したいことがあるでしょう。そんな時はいつも、まったくネストせず、できるだけ平準化することをお勧めします。一つのオブジェクトの中にあるすべてのエンティティは、キーとしてIDを持つのです。このIDを使って、他のエンティティやそのリストを参照するのです。アプリの状態を、データベースとして考えてください。この考え方は、 [normalizr's](https://github.com/paularmstrong/normalizr) のドキュメントで詳しく説明されています。
例えば実際のアプリでは、 状態の中に `todosById: { id -> todo }` と `todos: array<id>` を持つ方が良いでしょう。しかしこのTodoアプリは例なので、シンプルなままにしておきましょう。

## Actionを処理する

先ほど、状態オブジェクトがどのようなものかを決めました。これでReducerを書く準備ができました。Reducerは前の状態とActionを取り、次の状態を返す純粋関数です。

```js
(previousState, action) => newState
```

なぜReducerと呼ばれるのでしょう？それは、[`Array.prototype.reduce(reducer, ?initialValue)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) に渡すタイプの関数だからです。純粋関数であることは、とても大切です。Reducerで、 **絶対に** してはいけないこと:

* 引数に手を加える
* 副作用を起こす。例）APIコールやページ遷移
* 純粋ではない関数を呼び出す。 例）`Date.now()` や `Math.random()`

[advanced walkthrough](../advanced/README.md)で、副作用の扱い方を深く見てみます。今のところは、Reducerが必ず純粋でなければならないとだけを覚えていてください。
 **引数が与えられると、次の状態を計算して返すのです。びっくりすることはありません。副作用もありません。API呼び出しもありません。変更もありません。ただ計算するだけです。**

これを頭に入れた上で、Reducerについて書き始めましょう。先に説明した[Action](Actions.md) を理解できるよう、ゆっくり教えていきます。

まず、初期状態を明示しましょう。Reduxは最初、`undefined`状態とともにReducerを呼び出します。この時が、初期状態を返すチャンスです:

```js
import { VisibilityFilters } from './actions'

const initialState = {
  visibilityFilter: VisibilityFilters.SHOW_ALL,
  todos: []
}

function todoApp(state, action) {
  if (typeof state === 'undefined') {
    return initialState
  }

  // 今のところ, 何のアクションも処理していない
  // ただ、与えられた状態を返しているだけ。
  return state
}
```

よりコンパクトに書くには、 [ES6 default arguments syntax](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/default_parameters) を使うことです:

```js
function todoApp(state = initialState, action) {
  // 今のところ, 何のアクションも処理ていない
  // ただ、与えられた状態を返しているだけ。
  return state
}
```

それでは、`SET_VISIBILITY_FILTER`を処理しましょう. 必要なのは、状態の`visibilityFilter`を変えるだけ。簡単です:

```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
```

注意事項:

1. **`state`をいじっている訳ではありません** [`Object.assign()`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)でコピーを作っています。 `Object.assign(state, { visibilityFilter: action.filter })`も間違いです : これは最初の引数に手を加えています。最初の引数として、**必ず** 空のオブジェクトを渡してください。代わりに [object spread operator proposal](../recipes/UsingObjectSpreadOperator.md) で `{ ...state, ...newState }` と書くこともできます。

2. **`default（既定）` ケースとして、前の`state（状態）を返します`。** すべての不明なActionには、前の`state`を返すのが重要です。

>##### `Object.assign`についての注意

>[`Object.assign()`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) はES6の一部です。古いブラウザでは、まだ対応していません。対応するためにはポリフィルか、[Babel プラグイン](https://www.npmjs.com/package/babel-plugin-transform-object-assign), または[`_.assign()`](https://lodash.com/docs#assign)のような別のライブラリのヘルパーが必要です。

>##### `switch` と常用文についての注意

>`switch`文は、本当の常用文では*ありません*。 Fluxの本当の常用文は、概念的です: 更新を発行する必要があるし、 DispatcherとともにStoreを登録する必要があるし、Storeは一つのオブジェクトにする必要があります（ユニバーサルアプリにしたいなら、複雑になります）。 Reduxはイベントを発行する代わりに純粋なReducerを使うことで、これらの問題を解決します。

>多くの人がまだ、ドキュメントに`switch`文が載っているかどうかでフレームワークを選んでいるのは残念です。もし`switch`が好きでなければ、アクションの処理を対応づける（マッピングする）ために、 特別な`createReducer`関数を使うこともできます。この関数は [“reducing boilerplate”](../recipes/ReducingBoilerplate.md#reducers)で説明しています。

## もっとアクションを処理する

処理しなければいけない、さらに２つのActionがあります！ `SET_VISIBILITY_FILTER`でやったように、`ADD_TODO`と`TOGGLE_TODO`Actionをインポートします。そして`ADD_TODO`を処理するためにReducerを拡張します。

```js
import {
  ADD_TODO,
  TOGGLE_TODO,
  SET_VISIBILITY_FILTER,
  VisibilityFilters
} from './actions'

...

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    default:
      return state
  }
}
```

先ほどと同じように、`state`とその中身を直接書き換えてはいけません。代わりに新しいオブジェクトを返します。 新しい`todos`は、古い`todos`の最後に１つTodo項目を付け加えたのと同じです。　新たなTodo項目はアクションのデータから作られます。

最後に、`TOGGLE_TODO`ハンドラを実装します。何も驚くようなことはありません:

```js
case TOGGLE_TODO:
  return Object.assign({}, state, {
    todos: state.todos.map((todo, index) => {
      if (index === action.index) {
        return Object.assign({}, todo, {
          completed: !todo.completed
        })
      }
      return todo
    })
  })
```

変更のためにソートし直すことなく、配列にある特定の項目を更新したいのです。そのためインデックスで示された項目以外は同じになる、新しい配列を作らないといけません。もしこのような処理をよく書いていることに気づいたら、[immutability-helper](https://github.com/kolodny/immutability-helper)や[updeep](https://github.com/substantial/updeep)のようなヘルパーを使うと良いでしょう。または[Immutable](http://facebook.github.io/immutable-js/)のように、ネストされた更新にもともと対応しているライブラリもあります。とにかく`state`の中に何かを割り当てるときは、まず最初に複製することを忘れないでください。

## Reducerを分割する

ここまでのコードです。ごちゃごちゃしてます:

```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if (index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
```

もっと分かりやすくする方法はないでしょうか? `todos`と`visibilityFilter`は完全に独立して更新されているように見えます。状態のそれぞれが互いに依存しているような場合は、もっと考慮が必要です。 しかしこの例では、`todos`を簡単に別の関数に分割して更新できそうです:

```js
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    default:
      return state
  }
}
```

気をつけてほしいのは、`todos`も`state`を受け取りますが、配列だということです！`todoApp`は状態の一部だけ渡します。そして`todos`は渡された状態についてのみ、どのように更新すべきか把握しています。**これは *Reducer合成* と呼ばれ、Reduxアプリをつくる基本パターンです。**

Reducer合成をもっと見てみましょう。Reducerから`visibilityFilter`も切り出せないでしょうか？もちろんできます。

下記のインポートでは、`SHOW_ALL`を宣言するために[ES6 Object Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)を使いましょう:
```js
const { SHOW_ALL } = VisibilityFilters
```

そして:
```js
function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}
```

いま大元のReducerを書き換えました。このReducerは、状態の一部を処理するReducerを呼び出し、一つのオブジェクトとして合成する関数です。もう初期状態の全体を把握する必要はありません。ただ最初に`undefined`が与えられると、配下のReducerがそれぞれの初期状態を返すことが分かっていれば良いのです。

```js
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}

function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}

function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```

**それぞれのReducerは、グローバルの状態のうち自身が担当する部分だけを処理します。そのため`state`引数はすべてのReducerで異なり、処理する状態だけが渡されます。**

これで良くなりました！アプリが大きくなったらReducerを別のファイルに分割しても構いません。ファイルを分けることで、異なるデータ領域の処理について完全な独立性を保てます。

最後に、Reduxは[`combineReducers()`](../api/combineReducers.md)という便利な関数呼び出しを用意しています。これは上記の`todoApp`でやったのと同じ常用的なロジックです。この関数により、`todoApp`を下記のように書き換えられます:

```js
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

これは下記と同じです:

```js
export default function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```

異なるキーを渡したり、異なる関数を呼び出すこともできます。下記２つの合成したReducerは同等です:

```js
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})
```

```js
function reducer(state = {}, action) {
  return {
    a: doSomethingWithA(state.a, action),
    b: processB(state.b, action),
    c: c(state.c, action)
  }
}
```

[`combineReducers()`](../api/combineReducers.md)がやるのは、複数のReducerを呼び出す関数の生成です。呼び出されたReducerには、キーによって対応づけられた状態の一部が渡されます。 そして[`combineReducers()`](../api/combineReducers.md)はReducerが返した結果を一つのオブジェクトにまとめ直します。[It's not magic.（これは魔法ではありません。）](https://github.com/reactjs/redux/issues/428#issuecomment-129223274)引数として渡されたすべてのReducerが状態を変えなければ、新しいオブジェクトは作られません。これは他のReducerと同じです。

>##### ES6に精通したユーザーへの注意

>`combineReducers`は一つのオブジェクトを待ち構えています。そのため最上位にある複数のReducerを別々のファイルに入れて`export`できます。
こうすると`import * as reducers`で、それぞれのReducerの関数名をキーにした一つのオブジェクトが得られます:

>```js
>import { combineReducers } from 'redux'
>import * as reducers from './reducers'
>
>const todoApp = combineReducers(reducers)
>```
>
>`import *`はまだ新しい構文なので、このドキュメントでは[confusion（混乱）](https://github.com/reactjs/redux/issues/428#issuecomment-129223274)を避けるために他では使いません。しかしどこかのコミュニティで、例として出くわすかもしれません。

## ソースコード

#### `reducers.js`

```js
import { combineReducers } from 'redux'
import {
  ADD_TODO,
  TOGGLE_TODO,
  SET_VISIBILITY_FILTER,
  VisibilityFilters
} from './actions'
const { SHOW_ALL } = VisibilityFilters

function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

## 次のステップ

次に、[ReduxのStoreをつくる](Store.md) 方法を学びましょう。Storeは状態を保持します。そしてActionが送られると、Reducerの呼び出しを処理します。
