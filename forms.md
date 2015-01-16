# 表单

表单不同于其他 HTML 元素，因为它要响应用户的交互，显示不同的状态，所以在 React
里面会有点特殊。

## 状态属性

表单元素有这么几种属于状态的属性：

- `value`，对应 `<input>` 和 `<textarea>` 所有
- `checked`，对应类型为 `checkbox` 和 `radio` 的 `<input>` 所有
- `selected`，对应 `<option>` 所有

_在 HTML 中 `<textarea>` 的值可以由子节点（文本）赋值，但是在 React 中，要用
`value` 来设置。_

表单元素包含以上任意一种状态属性都支持 `onChange` 事件监听状态值的更改。

针对这些状态属性不同的处理策略，表单元素在 React 里面有两种表现形式。

## 受控组件

对于设置了上面提到的对应“状态属性“值的表单元素就是受控表单组件，比如：

```javascript
render: function() {
	return <input type="text" value="hello"/>;
}
```

一个受控的表单组件，它所有状态属性更改涉及 UI 的变更都由 React
来控制（状态属性绑定 UI）。比如上面代码里的 `<input>`
输入框，用户输入内容，用户输入的内容不会显示（输入框总是显示状态属性 `value` 的值 `hello`），这有点颠覆我们的认知了，所以说这是**受控**组件，不是原来默认的表单元素了。

如果你希望输入的内容反馈到输入框，就要用 `onChange` 事件改变状态属性 `value`
的值：

```javascript
getInitialState: function() {
	return {value: 'hello'};
},
handleChange: function(event) {
	this.setState({value: event.target.value});
},
render: function() {
	var value = this.state.value;
	return <input type="text" value={value} onChange={this.handleChange} />;
}
```

使用这种模式非常容易实现类似对用户输入的验证，或者对用户交互做额外的处理，比如截断最多输入140个字符：

```javascript
handleChange: function(event) {
	this.setState({value: event.target.value.substr(0, 140)});
}
```

## 非受控组件

和受控组件相对，如果表单元素没有设置自己的“状态属性”，或者属性值设置为
`null`，这时候就是非受控组件。

它的表现就符合普通的表单元素，正常响应用户的操作。

同样，你也可以绑定 `onChange` 事件处理交互。

如果你想要给“状态属性”设置默认值，就要用 React 提供的特殊属性
`defaultValue`，对于 `checked` 会有 `defaultChecked`，`<option>`
也是使用 `defaultValue`。

## 为什么要有受控组件？

引入受控组件不是说它有什么好处，而是因为 React 的 UI
渲染机制，对于表单元素不得不引入这一特殊的处理方式。

在浏览器 DOM 里面是有区分 _attribute_ 和 _property_ 的。_attribute_  是在 HTML
里指定的属性，而每个 HTML 元素在 JS 对应是一个 DOM
节点对象，这个对象拥有的属性就是 _property_（可以在 console 里展开一个 DOM 节点对象看一下，HTML _attributes_  只是对应其中的一部分属性），_attribute_ 对应的 _property_ 会从 _attribute_
拿到初始值，有些会有相同的名称，但是有些名称会不一样，比如 _attribute_ `class` 对应的 _property_ 就是
`className`。（详细解释：[.prop](http://api.jquery.com/prop/)，[.prop() vs
.attr()](http://stackoverflow.com/questions/5874652/prop-vs-attr)）

回到 React 里的 `<input>` 输入框，当用户输入内容的时候，输入框的 `value` _property_
会改变，但是 `value`  _attribute_ 依然会是 HTML 上指定的值（_attribute_ 要用
`setAttribute` 去更改）。

React 组件必须呈现这个组件的状态视图，这个视图 HTML 是由 `render` 生成，所以对于

```javascript
render: function() {
	return <input type="text" value="hello"/>;
}
```

在任意时刻，这个视图总是返回一个显示 `hello` 的输入框。

## `<select>`

在 HTML 中 `<select>` 标签指定选中项都是通过对应 `<option>` 的 `selected`
属性来做的，但是在 React 修改成统一使用 `value`。

**所以没有一个 `selected` 的状态属性。**

```html
<select value="B">
	<option value="A">Apple</option>
	<option value="B">Banana</option>
	<option value="C">Cranberry</option>
</select>
```

你可以通过传递一个数组指定多个选中项：`<select multiple={true} value={['B', 'C']}>`
