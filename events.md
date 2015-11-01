# 事件处理

{% include './share/simple-component.md' %}

可以看到 React 里面绑定事件的方式和在 HTML
中绑定事件类似，使用驼峰式命名指定要绑定的 `onClick` 属性为组件定义的一个方法 `{this.handleClick.bind(this)}`。

注意要显式调用 `bind(this)` 将事件函数上下文绑定要组件实例上，这也是 React
推崇的原则：没有黑科技，尽量使用显式的容易理解的 JavaScript 代码。

## “合成事件”和“原生事件”

React 实现了一个“合成事件”层（synthetic event system），这个事件模型保证了和
W3C
标准保持一致，所以不用担心有什么诡异的用法，并且这个事件层消除了 IE 与 W3C 标准实现之间的兼容问题。

“合成事件”还提供了额外的好处：

**事件委托**

“合成事件”会以事件委托（event
delegation）的方式绑定到组件最上层，并且在组件卸载（unmount）的时候自动销毁绑定的事件。

**什么是“原生事件”？**

比如你在 `componentDidMount` 方法里面通过 `addEventListener`
绑定的事件就是浏览器原生事件。

使用原生事件的时候注意在 `componentWillUnmount` 解除绑定 `removeEventListener`。

所有通过 JSX
这种方式绑定的事件都是绑定到“合成事件”，除非你有特别的理由，建议总是用 React
的方式处理事件。

**Tips**

关于这两种事件绑定的使用，这里有必要分享一些额外的人生经验

如果混用“合成事件”和“原生事件”，比如一种常见的场景是用原生事件在 document
上绑定，然后在组件里面绑定的合成事件想要通过 `e.stopPropagation()`
来阻止事件冒泡到 document，这时候是行不通的，参见 [Event
delegation](http://stackoverflow.com/a/24421834/581094)，因为 `e.stopPropagation` 是内部“合成事件” 层面的，解决方法是要用 `e.nativeEvent.stopImmediatePropagation()`

”合成事件“ 的 `event` 对象只在当前 event loop
有效，比如你想在事件里面调用一个 promise，在 resolve 之后去拿 `event`
对象会拿不到（并且没有错误抛出）：

```javascript
handleClick(e) {
  promise.then(() => doSomethingWith(e));
}
```

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
