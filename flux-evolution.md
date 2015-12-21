# 进化 Flux

我们可以先通过对比 Redux 和 Flux 的实现来感受一下 Redux 带来的惊艳。

首先是 _action creators_，Flux 是直接在 action 里面调用 dispatch：

```javascript
export function addTodo(text) {
  AppDispatcher.dispatch({
    type: ActionTypes.ADD_TODO,
    text: text
  });
}
```

Redux 把它简化成了这样：

```javascript
export function addTodo(text) {
  return {
    type: ActionTypes.ADD_TODO,
    text: text
  };
}
```

这一步把 dispatcher 和 action 解藕了，很快我们就能看到它带来的好处。

接下来是 Store，这是 Flux 里面的 Store：

```javascript
let _todos = [];
const TodoStore = Object.assign(new EventEmitter(), {
  getTodos() {
    return _todos;
  }
});
AppDispatcher.register(function (action) {
  switch (action.type) {
  case ActionTypes.ADD_TODO:
    _todos = _todos.concat([action.text]);
    TodoStore.emitChange();
    break;
  }
});
export default TodoStore;
```

Redux 把它简化成了这样：

```javascript
const initialState = { todos: [] };
export default function TodoStore(state = initialState, action) {
  switch (action.type) {
  case ActionTypes.ADD_TODO:
    return { todos: state.todos.concat([action.text]) };
  default:
    return state;
}
```

同样把 dispatch 从 Store 里面剥离了，Store 变成了一个 **pure function**：`(state,
action) => state`

### 什么是 pure function

如果一个函数没有任何副作用（side-effects)，不会影响任何外部状态，对于任何一个相同的输入（参数），无论何时调用这个函数总是返回同样的结果，这个函数就是一个
pure function。所谓 side-effects 就是会改变外部状态的因素
，比如 Ajax 请求就有
side-effects，因为它带来了不确定性。

所以现在 Store
不再**拥有**状态，而只是**管理**状态，所以首先要明确一个概念，Store 和 State
是有区别的，Store 并不是一个简单的数据结构，State 才是，Store
会包含一些方法来**管理** State，比如获取／修改 State。

基于这样的 Store，可以做很多扩展，这也是
Redux 强大之处。

来源：[The Evolution of Flux Frameworks](https://medium.com/@dan_abramov/the-evolution-of-flux-frameworks-6c16ad26bb31)
