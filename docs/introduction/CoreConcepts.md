# 核となるコンセプト

Redux自体はとてもシンプルです。

アプリの状態が、単なるオブジェクトで記述されているのを想像してください。例えば、ToDoアプリの状態がこうだとしたら:

```js
{
  todos: [{
    text: 'ごはんを食べる',
    completed: true
  }, {
    text: 'エクササイズ',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

このオブジェクトは、setter（設定関数）がないことを除けばモデルのようです。setterがないので、コードの異なる部分が状態を任意に変えるのは難しいです。そのため、バグを出しにくくなります。

状態の何かを変えるには、Actionを送る必要があります。Actionは何が起きたか記述する、単なるJavaScriptのオブジェクトです（どんな魔法も紹介していないことに気づきましたか？）。 いくつかActionの例をあげましょう:

```js
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```

すべての変化に対して、Actionとして記述することを強制します。これにより、アプリで何が起きているかがはっきり分かります。つまりもし何か変化したら、なぜ変化したのか分かります。Actionは起きたことを伝える、パンくずのようなものです。
最後に、Reducerという関数を書きます。状態とActionを結びつけるためです。これも、決して魔法ではありません。ただの関数です。状態とActionを引数に取り、アプリの次の状態を返します。大きなアプリだと、このような関数を１つ書くのは難しいでしょう。そのため状態の一部を管理する、より小さな関数をいくつか書きます:

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map(
        (todo, index) =>
          action.index === index
            ? { text: todo.text, completed: !todo.completed }
            : todo
      )
    default:
      return state
  }
}
```

そして状態全体を管理するもう一つのReducerを書きます。このReducerは、それぞれ対応する状態のキーとともに、上記２つのReducerを呼び出します:

```js
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
}
```

これが基本となる、Reduxの全体的なアイデアです。ReduxのAPIはまったく使っていないことに注意してください。このパターンを容易にするためのユーティリティはいくつかあります。しかし主なアイデアは、アクションのオブジェクトに対し、どうやって状態を更新するか記述することです。そしてコードの90%
は、ただふつうのJavaScriptです。Redux自体や、ReduxのAPI、そしてどんな魔法も使っていません。
