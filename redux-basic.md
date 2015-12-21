# Redux 的基础概念

## 三个基本原则

- 整个应用只有唯一一个可信数据源，也就是只有一个 Store
- State 只能通过触发 Action 来更改
- State 的更改必须写成纯函数，也就是每次更改总是返回一个新的
  State，在 Redux 里这种函数称为 Reducer

## Actions

Action 很简单，就是一个单纯的包含 `{ type, payload }` 的对象，`type`
是一个常量用来标示动作类型，`payload` 是这个动作携带的数据。Action 需要通过
`store.dispatch()` 方法来发送。

比如一个最简单的 action：

```javascript
{
  type: 'ADD_TODO',
  text: 'Build my first Redux app'
}
```

一般来说，会使用函数（Action Creators）来生成
action，这样会有更大的灵活性，Action Creators 是一个 **pure
function**，它最后会返回一个 action 对象：

```javascript
function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text
  }
}
```

所以现在要触发一个动作只要调用 `dispatch`: `dispatch(addTodo(text))`

稍后会讲到如何拿到 `store.dispatch`

## Reducers

Reducer 用来处理 Action 触发的对状态树的更改。

所以一个 reducer 函数会接受 `oldState` 和 `action` 两个参数，返回一个新的
state：`(oldState, action) => newState`。一个简单的 reducer 可能类似这样：

```javascript
const initialState = {
  a: 'a',
  b: 'b'
};

function someApp(state = initialState, action) {
  switch (action.type) {
    case 'CHANGE_A':
      return { ...state, a: 'Modified a' };
    case 'CHANGE_B':
      return { ...state, b: action.payload };
    default:
      return state
  }
}
```

值得注意的有两点：

- 我们用到了 [object spread 语法](https://github.com/sebmarkbage/ecmascript-rest-spread) 确保不会更改到 `oldState` 而是返回一个 `newState`
- 对于不需要处理的 action，直接返回 `oldState`

Reducer 也是 **pure function**，这点非常重要，所以绝对不要在 reducer
里面做一些引入 side-effects 的事情，比如：

- 直接修改 state 参数对象
- 请求 API
- 调用不纯的函数，比如 `Data.now()` `Math.random()`

因为 Redux 里面只有一个 Store，对应一个 State 状态，所以整个 State
对象就是由一个 reducer 函数管理，但是如果所有的状态更改逻辑都放在这一个 reducer
里面，显然会变得越来越巨大，越来越难以维护。得益于纯函数的实现，我们只需要稍微变通一下，让状态树上的每个字段都有一个
reducer 函数来管理就可以拆分成很小的 reducer 了：

```javascript
function someApp(state = {}, action) {
  return {
    a: reducerA(state.a, action),
    b: reducerB(state.b, action)
  };
}
```

对于 `reducerA` 和 `reducerB` 来说，他们依然是形如：`(oldState, action) => newState` 的函数，只是这时候的 state 不是整个状态树，而是树上的特定字段，每个 reducer 只需要判断 action，管理自己关心的状态字段数据就好了。

Redux 提供了一个工具函数 `combineReducers` 来简化这种 reducer 合并：

```javascript
import { combineReducers } from 'redux';

const someApp = combineReducers({
  a: reducerA,
  b: reducerB
});
```

如果 reducer 函数名字和字段名字相同，利用 ES6 的 Destructuring
可以进一步简化成：`combineReducers({ a, b })`

象 `someApp` 这种管理整个 State 的 reducer，可以称为 **root reducer**。

## Store

现在有了 Action 和 Reducer，Store 的作用就是连接这两者，Store
的作用有这么几个：

- Hold 住整个应用的 State 状态树
- 提供一个 `getState()` 方法获取 State
- 提供一个 `dispatch()` 方法发送 action 更改 State
- 提供一个 `subscribe()` 方法注册回调函数监听 State 的更改

创建一个 Store 很容易，将 **root reducer** 函数传递给 `createStore` 方法即可：

```javascript
import { createStore } from 'redux';
import someApp from './reducers';
let store = createStore(someApp);

// 你也可以额外指定一个初始 State（initialState），这对于服务端渲染很有用
// let store = createStore(someApp, window.STATE_FROM_SERVER);
```

现在我们就拿到了 `store.dispatch`，可以用来分发 action 了：

```javascript
let unsubscribe = store.subscribe(() => console.log(store.getState()));

// Dispatch
store.dispatch({ type: 'CHANGE_A' });
store.dispatch({ type: 'CHANGE_B', payload: 'Modified b' });

// Stop listening to state updates
unsubscribe();
```

## Data Flow

以上提到的 `store.dispatch(action) -> reducer(state, action) ->
store.getState()` 其实就构成了一个“单向数据流”，我们再来总结一下。

**1. 调用 `store.dispatch(action)`**

Action 是一个包含 `{ type, payload }` 的对象，它描述了“发生了什么”，比如：

```javascript
{ type: 'LIKE_ARTICLE', articleID: 42 }
{ type: 'FETCH_USER_SUCCESS', response: { id: 3, name: 'Mary' } }
{ type: 'ADD_TODO', text: 'Read the Redux docs.' }
```

你可以在任何地方调用 `store.dispatch(action)`，比如组件内部，Ajax
回调函数里面等等。

**2. Action 会触发给 Store 指定的 root reducer**

**root reducer** 会返回一个完整的状态树，State 对象上的各个字段值可以由各自的
reducer 函数处理并返回新的值。

- reducer 函数接受 `(state, action)` 两个参数
- reducer 函数判断 `action.type` 然后处理对应的 `action.payload` 数据来更新并返回一个新的 state

**3. Store 会保存 root reducer 返回的状态树**

新的 State 会替代就的 State，然后所有 `store.subscribe(listener)`
注册的回调函数会被调用，在回调函数里面可以通过 `store.getState()` 拿到新的
State。

这就是 Redux 的运作流程，接下来看如何在 React 里面使用 Redux。
