# Mixins

虽然组件的原则就是模块化，彼此之间相互独立，但是有时候不同的组件之间可能会共用一些功能，共享一部分代码。所以 React
提供了 `mixins` 这种方式来处理这种问题。

Mixin 就是用来定义一些方法，使用这个 mixin
的组件能够自由的使用这些方法（就像在组件中定义的一样），所以 mixin
相当于组件的一个扩展，在 mixin 中也能定义“生命周期”方法。

比如一个定时器的 mixin：

```javascript
var SetIntervalMixin = {
	componentWillMount: function() {
		this.intervals = [];
	},
	setInterval: function() {
		this.intervals.push(setInterval.apply(null, arguments));
	},
	componentWillUnmount: function() {
		this.intervals.map(clearInterval);
	}
};

var TickTock = React.createClass({
	mixins: [SetIntervalMixin], // Use the mixin
	getInitialState: function() {
		return {seconds: 0};
	},
	componentDidMount: function() {
		this.setInterval(this.tick, 1000); // Call a method on the mixin
	},
	tick: function() {
		this.setState({seconds: this.state.seconds + 1});
	},
	render: function() {
		return (
			<p>
				React has been running for {this.state.seconds} seconds.
			</p>
		);
	}
});

React.render(
	<TickTock />,
	document.getElementById('example')
);
```

React 的 `mixins` 的强大之处在于，如果一个组件使用了多个 mixins，其中几个 `mixins`
定义了相同的“生命周期方法”，这些方法会在组件相应的方法执行完之后按 mixins
指定的数组顺序执行。
