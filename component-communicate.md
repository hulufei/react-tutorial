# 组件间通信

## 父子组件间通信

这种情况下很简单，就是通过 `props`
属性传递，在父组件设置子组件的属性，所以子组件就可以通过 `props`
或者事件绑定
访问到父组件的方法，这样就搭建起了父子组件间通信的桥梁。

_这种将数据属性从父级往下传递的方式就是 React 里面的 Data
Flow（”单向数据流“），之后会进一步介绍。_

```javascript
var GroceryList = React.createClass({
	handleClick: function(i) {
		console.log('You clicked: ' + this.props.items[i]);
	},

	render: function() {
		return (
			<div>
				{this.props.items.map(function(item, i) {
					return (
						<div onClick={this.handleClick.bind(this, i)} key={i}>{item}</div>
					);
				}, this)}
			</div>
		);
	}
});

React.render(
	<GroceryList items={['Apple', 'Banana', 'Cranberry']} />, mountNode
);
```

`div` 可以看作一个子组件，指定它的 `onClick` 事件调用父组件的方法。

注意 `this.handleClick.bind(this, i)`
给事件处理函数传递额外参数的方式：`bind(this, arg1, arg2, ...)`。

父组件访问子组件？用 `refs`

## 非父子组件间的通信

使用全局事件 Pub/Sub 模式，在 `componentDidMount` 里面订阅事件，在
`componentWillUnmount` 里面取消订阅，当收到事件触发的时候调用 `setState` 更新
UI。

这种模式在复杂的系统里面可能会变得难以维护，所以看个人权衡是否将组件封装到大的组件，甚至整个页面或者应用就封装到一个组件。
