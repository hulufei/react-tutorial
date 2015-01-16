# React 概览

React 的核心思想是：封装组件，各个组件维护自己的状态和
UI，当状态变更，自动重新渲染整个组件的 UI。

基于这种方式的一个直观感受就是我们不再需要不厌其烦地来回查找某个 DOM
元素，然后操作 DOM 去更改 UI。

React 大体包含下面这些概念：

- 组件
- JSX
- Virtual DOM
- Data Flow

这里通过一个简单的组件来快速了解这些概念，以及建立起对 React 的一个总体认识。

```javascript
var HelloMessage = React.createClass({
  render: function() {
    return <div>Hello {this.props.name}</div>;
  }
});

// 加载组件到 DOM 元素 mountNode
React.render(<HelloMessage name="John" />, mountNode);
```

## 组件

React 应用都是构建在组件之上。

`HelloMessage` 就是一个 React
构建的组件，最后一句 `React.render` 会把这个组件显示到页面上的某个元素 `mountNode` 里面，显示的内容就是 `<div>Hello John</div>`。

`props` 是组件包含的两个核心概念之一，另一个是 `state`（这个组件没用到）。可以把 `props` 看作是组件的配置属性，在组件内部是不变的，只是在调用这个组件的时候传入不同的属性（比如这里的 `name`）来定制显示这个组件。

## Virtual DOM

当组件状态 `state` 有更改的时候，React 会自动调用组件的 `render` 方法重新渲染整个组件的 UI。

为了性能上面的考虑，React 内部实现了一个_虚拟
DOM_，组件就是映射到这个虚拟 DOM 上，当要更新组件的时候，React 在这个虚拟 DOM
上实现了一个 diff 算法，寻找到要变更的 DOM 节点，再把这个更新到浏览器对应要修改的
DOM 节点上，所以实际上不是真的渲染整个 DOM 树。这个虚拟 DOM 是一个纯粹的 JS 数据结构，所以性能会比原生 DOM 快很多。

## JSX

从上面的代码可以看到将 HTML 直接嵌入了 JS 代码里面，这个就是 React 提出的一种叫 _JSX_ 的语法，这应该是最开始接触 React 最不能接受的设定之一，因为被代码分离“洗脑”太久了。好消息是你可以不一定使用这种语法，后面会进一步介绍 JSX，到时候你可能就会喜欢上了。现在要知道的是，要使用包含 JSX 的组件，是需要“编译”输出 JS 代码才能使用的，之后就会讲到开发环境。

## Data Flow

“单向数据绑定”是 React
推崇的一种应用架构的方式。当应用足够复杂时才能体会到它的好处，虽然在一般应用场景下你可能不会意识到它的存在，也不会影响你开始使用
React，你只要先知道有这么个概念。
