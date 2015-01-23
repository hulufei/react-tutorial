# 事件处理

{{ ./share/simple-component.md }}

可以看到 React 里面绑定事件的方式和在 HTML
中绑定事件类似，使用驼峰式命名指定要绑定的 `onClick` 属性为组件定义的一个方法 `{this.handleClick}`。

## “合成事件”和“原生事件”

React 实现了一个“合成事件”层（synthetic event system），这个事件模型保证了和
W3C
标准保持一致，所以不用担心有什么诡异的用法，并且这个事件层消除了 IE 与 W3C 标准实现之间的兼容问题。

“合成事件”额外提供了两个好处：

**自动绑定上下文和事件委托**

“合成事件”自动将事处理件方法的上下文绑到当前组件，所以 `handleClick`
方法里面可以直接使用 `this.setState`。

“合成事件”会以事件委托（event
delegation）的方式绑定到组件最上层，并且在组件卸载（unmount）的时候自动销毁绑定的事件。

**什么是“原生事件”？**

比如你在 `componentDidMount` 方法里面通过 `addEventListener`
绑定的事件就是浏览器原生事件。

使用原生事件的时候注意在 `componentWillUnmount` 解除绑定 `removeEventListener`。

所有通过 JSX
这种方式绑定的事件都是绑定到“合成事件”，除非你有特别的理由，建议总是用 React
的方式处理事件。

## 参数传递

给事件处理函数传递额外参数的方式：`bind(this, arg1, arg2, ...)`

```javascript
render: function() {
	return <p onClick={this.handleClick.bind(this, 'extra param')}>;
},
handleClick: function(param, event) {
	// handle click
}
```

[React 支持的事件列表](http://facebook.github.io/react/docs/events.html)
