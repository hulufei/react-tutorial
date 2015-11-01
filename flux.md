# Flux

React 标榜自己是 MVC 里面 V 的部分，那么 Flux 就相当于添加 M 和 C 的部分。

Flux 是 Facebook 使用的一套前端应用的架构模式。

一个 Flux 应用主要包含四个部分：

- the dispatcher

  _处理动作分发，维护 Store 之间的依赖关系_
- the stores

  _数据和逻辑部分_
- the views

  _React 组件，这一层可以看作 controller-views，作为视图同时响应用户交互_
- the actions

  _提供给 dispatcher 传递数据给 store_

针对上面提到的 Flux
这些概念，需要写一个简单的类库来实现衔接这些功能，市面上有很多种实现，这里讨论 Facebook
官方的一个实现 [Dispatcher.js](https://github.com/facebook/flux/blob/master/src/Dispatcher.js)

## 单向数据流

先来了解一下 Flux 的核心“单向数据流“怎么运作的：

```
Action -> Dispatcher -> Store -> View
```

更多时候 View 会通过用户交互触发 Action，所以一个简单完整的数据流类似这样：

![flux overview](image/flux-overview.png)

整个流程如下：

- 首先要有 action，通过定义一些 _action creator_ 方法根据需要创建 Action 提供给 dispatcher
- View 层通过用户交互（比如 onClick）会触发 Action
- Dispatcher 会分发触发的 Action 给所有注册的 Store 的回调函数
- Store 回调函数根据接收的 Action 更新自身数据之后会触发一个 _change_ 事件通知 View 数据更改了
- View 会监听这个 _change_ 事件，拿到对应的新数据并调用 `setState` 更新组件 UI

所有的状态都由 Store
来维护，通过 Action 传递数据，构成了如上所述的单向数据流循环，所以应用中的各部分分工就相当明确，高度解耦了。

这种单向数据流使得整个系统都是透明可预测的。

## Dispatcher

一个应用只需要一个 dispatcher 作为分发中心，管理所有数据流向，分发动作给
Store，没有太多其他的逻辑（一些 _action creator_ 方法也可以放到这里）。

Dispatcher 分发动作给 Store
注册的回调函数，这和一般的订阅/发布模式不同的地方在于：

- 回调函数不是订阅到某一个特定的事件/频道，每个动作会分发给所有注册的回调函数
- 回调函数可以指定在其他回调之后调用

基于 Flux 的架构思路，[Dispatcher.js](https://github.com/facebook/flux/blob/master/src/Dispatcher.js) 提供的 API 很简单：

- **register(function callback): string** 注册回调函数，返回一个 token
  供在 `waitFor()` 使用
- **unregister(string id): void** 通过 token 移除回调
- **waitFor(array<string> ids): void**
  在指定的回调函数执行之后才执行当前回调。这个方法只能在分发动作的回调函数中使用
- **dispatch(object payload): void** 分发动作 payload 给所有注册回调
- **isDispatching(): boolean** 返回 Dispatcher 当前是否处在分发的状态

dispatcher 只是一个粘合剂，剩余的 Store、View、Action 就需要按具体需求去实现了。

接下来结合
[flux-todomvc](https://github.com/facebook/flux/tree/master/examples/flux-todomvc/js)
这个简单的例子，提取其中的关键部分，看一下实际应用中如何衔接 Flux 整个流程，希望能对 Flux
各个部分有更直观深入的理解。

## Action

首先要创建动作，通过定义一些 _action creator_ 方法来创建，这些方法用来暴露给外部调用，通过 `dispatch` 分发对应的动作，所以 _action creator_ 也称作 _dispatcher helper methods_ 辅助 dipatcher 分发。
参见
[actions/TodoActions.js](https://github.com/facebook/flux/blob/master/examples/flux-todomvc/js/actions/TodoActions.js)

```javascript
var AppDispatcher = require('../dispatcher/AppDispatcher');
var TodoConstants = require('../constants/TodoConstants');

var TodoActions = {
  create: function(text) {
    AppDispatcher.dispatch({
      actionType: TodoConstants.TODO_CREATE,
      text: text
    });
  },

  updateText: function(id, text) {
    AppDispatcher.dispatch({
      actionType: TodoConstants.TODO_UPDATE_TEXT,
      id: id,
      text: text
    });
  },

  // 不带 payload 数据的动作
  toggleCompleteAll: function() {
    AppDispatcher.dispatch({
      actionType: TodoConstants.TODO_TOGGLE_COMPLETE_ALL
    });
  }
};
```

`AppDispatcher` 直接继承自
Dispatcher.js，在这个简单的例子中没有提供什么额外的功能。`TodoConstants` 定义了动作的类型名称常量。

类似 `create`、`updateText` 就是 _action creator_，这两个动作会通过 View 上的用户交互触发（比如输入框）。 除了用户交互会创建动作，服务端接口调用也可以用来创建动作，比如通过 Ajax 请求的一些初始数据也可以创建动作提供给 dispatcher，再分发给 store 使用这些初始数据。

> action creators are nothing more than a call into the dispatcher.

可以看到所谓动作就是用来封装传递数据的，动作只是一个简单的对象，包含两部分：payload（数据）和 type（类型），type 是一个字符串常量，用来标识动作。

## Store

Stores 包含应用的状态和逻辑，不同的 Store 管理应用中不同部分的状态。如
[stores/TodoStore.js](https://github.com/facebook/flux/blob/master/examples/flux-todomvc/js/stores/TodoStore.js)

```javascript
var AppDispatcher = require('../dispatcher/AppDispatcher');
var EventEmitter = require('events').EventEmitter;
var TodoConstants = require('../constants/TodoConstants');
var assign = require('object-assign');

var CHANGE_EVENT = 'change';

var _todos = {};

// 先定义一些数据处理方法
function create(text) {
  var id = (+new Date() + Math.floor(Math.random() * 999999)).toString(36);
  _todos[id] = {
    id: id,
    complete: false,
    text: text
  };
}
function update(id, updates) {
  _todos[id] = assign({}, _todos[id], updates);
}
// ...

var TodoStore = assign({}, EventEmitter.prototype, {
  // Getter 方法暴露给外部获取 Store 数据
  getAll: function() {
    return _todos;
  },
  // 触发 change 事件
  emitChange: function() {
    this.emit(CHANGE_EVENT);
  },
  // 提供给外部 View 绑定 change 事件
  addChangeListener: function(callback) {
    this.on(CHANGE_EVENT, callback);
  }
});

// 注册到 dispatcher，通过动作类型过滤处理当前 Store 关心的动作
AppDispatcher.register(function(action) {
  var text;

  switch(action.actionType) {
    case TodoConstants.TODO_CREATE:
      text = action.text.trim();
      if (text !== '') {
        create(text);
      }
      TodoStore.emitChange();
      break;

    case TodoConstants.TODO_UPDATE_TEXT:
      text = action.text.trim();
      if (text !== '') {
        update(action.id, {text: text});
      }
      TodoStore.emitChange();
      break;
  }
});
```

在 Store 注册给 dispatcher 的回调函数中会接受到分发的 action，因为每个 action 都会分发给所有注册的回调，所以回调函数里面要判断这个 action 的 _type_ 并调用相关的内部方法处理更新 action 带过来的数据（payload），再通知 view 数据变更。

Store 里面不会暴露直接操作数据的方法给外部，暴露给外部调用的方法都是 Getter
方法，没有 Setter 方法，唯一更新数据的手段就是通过在 dispatcher 注册的回调函数。

## View

View 就是 React 组件，从 Store 获取状态（数据），绑定 change
事件处理。如
[components/TodoApp.react.js](https://github.com/facebook/flux/blob/master/examples/flux-todomvc/js/components/TodoApp.react.js)

```javascript
var React = require('react');
var TodoStore = require('../stores/TodoStore');

function getTodoState() {
  return {
    allTodos: TodoStore.getAll(),
    areAllComplete: TodoStore.areAllComplete()
  };
}

var TodoApp = React.createClass({

  getInitialState: function() {
    return getTodoState();
  },

  componentDidMount: function() {
    TodoStore.addChangeListener(this._onChange);
  },

  componentWillUnmount: function() {
    TodoStore.removeChangeListener(this._onChange);
  },

  render: function() {
    return <div>/*...*/</div>
  },

  _onChange: function() {
    this.setState(getTodoState());
  }
});
```

一个 View 可能关联多个 Store 来管理不同部分的状态，得益于 React 更新 View 如此简单（`setState`），复杂的逻辑都被 Store 隔离了。

## 更多资料

- [Flux chat](https://speakerdeck.com/fisherwebdev/fluxchat) 很简洁明了的一个
  Slide
- [flux-chat source
  code](https://github.com/facebook/flux/tree/master/examples/flux-chat/js)
  一个更复杂一点的例子
