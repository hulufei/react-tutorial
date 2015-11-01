# 组件生命周期

一个组件类必须由调用 `React.createClass` 创建，并且提供一个 `render`
方法以及其他可选的生命周期函数、组件相关的事件或方法定义。

{% include './share/simple-component.md' %}

## `getInitialState`

初始化 `this.state` 的值，只在组件装载之前调用一次。

## `getDefaultProps`

只在组件创建时调用一次并缓存返回的对象（即在 `React.createClass` 之后就会调用）。

因为这个方法在实例初始化之前调用，所以在这个方法里面不能依赖
`this` 获取到这个组件的实例。

在组件装载之后，这个方法缓存的结果会用来保证访问 `this.props` 的属性时，当这个属性没有在父组件中传入（在这个组件的 JSX
属性里设置），也总是有值的。

## `render`

**必须**

组装生成这个组件的 HTML 结构（使用原生 HTML 标签或者子组件），也可以返回 `null` 或者 `false`，这时候 React
会将组件生成一个 `<noscript>` 标签，并且 `this.getDOMNode()` 会返回 `null`。

## 生命周期函数

### 装载组件

`componentWillMount`

只会在装载之前调用一次，在 `render` 之前调用，你可以在这个方法里面调用 `setState`
改变状态，并且不会导致额外调用一次 `render`

`componentDidMount`

只会在装载完成之后调用一次，在 `render` 之后调用，从这里开始可以通过
`this.getDOMNode()` 获取到组件的 DOM 节点。

### 更新组件状态

这些方法不会在首次 `render` 组件的周期调用

- `componentWillReceiveProps`
- `shouldComponentUpdate`
- `componentWillUpdate`
- `componentDidUpdate`

### 卸载（删除）组件

- `componentWillUnmount`

更多关于组件相关的方法说明，参见：

- [Component Specs](http://facebook.github.io/react/docs/component-specs.html)
- [Component
  Lifecycle](http://facebook.github.io/react/docs/working-with-the-browser.html#component-lifecycle)
- [Component API](http://facebook.github.io/react/docs/component-api.html)
