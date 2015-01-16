# DOM 操作

大部分情况下你不需要通过查询 DOM 元素去更新组件的
UI，你只要关注设置组件的状态（`setState`）。但是可能在某些情况下你确实需要直接操作 DOM。

## getDOMNode()

当组件加载到页面上之后（mounted），你就可以通过 `getDOMNode()` 方法拿到组件对应的
DOM 元素。

## Refs

另外一种方式就是通过在要引用的 DOM 元素上面设置一个 `ref`
属性指定一个名称，然后通过 `this.refs.name` 来访问对应的 DOM 元素。

比如有一种情况是必须直接操作 DOM 来实现的，你希望一个 `<input/>`
元素在你清空它的值时 focus，你没法仅仅靠 `state` 来实现这个功能。

```javascript
var App = React.createClass({
	getInitialState: function() {
		return {userInput: ''};
	},
	handleChange: function(e) {
		this.setState({userInput: e.target.value});
	},
	clearAndFocusInput: function() {
		this.setState({userInput: ''}, function() {
			// This code executes after the component is re-rendered
			this.refs.theInput.getDOMNode().focus();
		});
	},
	render: function() {
		return (
			<div>
				<div onClick={this.clearAndFocusInput}>
					Click to Focus and Reset
				</div>
				<input
					ref="theInput"
					value={this.state.userInput}
					onChange={this.handleChange}
				/>
			</div>
		);
	}
});
```

注意 `ref` 引用到的元素其实是一个 React 组件，所以要用
`this.refs.theInput.getDOMNode()` 来拿到它的 DOM 元素，实际上 React
会把组件里面的所有子元素都认为是组件对象，因为 HTML 元素在 Virtual DOM
里面就是描述为一个 JS
对象（[`ReactElement`](http://facebook.github.io/react/docs/glossary.html)）。

所以，`ref` 不仅仅可以引用 HTML 元素，也可以直接引用子组件，比如
`<Typeahead ref="myTypeahead" />`.

## 总结

- 你可以使用引用组件定义的任何公共方法，比如 `this.refs.myTypeahead.reset()`
- Refs 是访问到组件内部 DOM 节点唯一**可靠**的方法
- Refs 会自动销毁对子组件的引用（当子组件删除时）

### 注意事项

- 不要在 `render` 或者 `render` 之前访问 `refs`
- 不要滥用 `refs`，通过它来按照传统的方式：找到 DOM -> 更新 DOM
