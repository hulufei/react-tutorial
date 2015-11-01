# DOM 操作

大部分情况下你不需要通过查询 DOM 元素去更新组件的
UI，你只要关注设置组件的状态（`setState`）。但是可能在某些情况下你确实需要直接操作 DOM。

首先我们要了解 `ReactDOM.render` 组件返回的是什么？

它会返回对组件的引用也就是组件实例（对于无状态状态组件来说返回 null），注意 JSX
返回的不是组件实例，它只是一个 `ReactElement` 对象（还记得我们用纯 JS 来构建 JSX
的方式吗），比如这种：

```javascript
// A ReactElement
const myComponent = <MyComponent />

// render
const myComponentInstance = ReactDOM.render(myComponent, mountNode);
myComponentInstance.doSomething();
```

## findDOMNode()

当组件加载到页面上之后（mounted），你都可以通过 `react-dom` 提供的 `findDOMNode()` 方法拿到组件对应的
DOM 元素。

```javascript
import { findDOMNode } from 'react-dom';

// Inside Component class
componentDidMound() {
  const el = findDOMNode(this);
}
```

`findDOMNode()` 不能用在无状态组件上。

## Refs

另外一种方式就是通过在要引用的 DOM 元素上面设置一个 `ref`
属性指定一个名称，然后通过 `this.refs.name` 来访问对应的 DOM 元素。

比如有一种情况是必须直接操作 DOM 来实现的，你希望一个 `<input/>`
元素在你清空它的值时 focus，你没法仅仅靠 `state` 来实现这个功能。

```javascript
class App extends Component {
  constructor() {
    return { userInput: '' };
  }

  handleChange(e) {
    this.setState({ userInput: e.target.value });
  }

  clearAndFocusInput() {
    this.setState({ userInput: '' }, () => {
      this.refs.theInput.focus();
    });
  }

  render() {
    return (
      <div>
        <div onClick={this.clearAndFocusInput.bind(this)}>
          Click to Focus and Reset
        </div>
        <input
          ref="theInput"
          value={this.state.userInput}
          onChange={this.handleChange.bind(this)}
        />
      </div>
    );
  }
}
```

如果 `ref` 是设置在原生 HTML 元素上，它拿到的就是 DOM
元素，如果设置在自定义组件上，它拿到的就是组件实例，这时候就需要通过
`findDOMNode` 来拿到组件的 DOM 元素。

因为无状态组件没有实例，所以 ref
不能设置在无状态组件上，一般来说这没什么问题，因为无状态组件没有实例方法，不需要
ref 去拿实例调用相关的方法，但是如果想要拿无状态组件的 DOM
元素的时候，就需要用一个状态组件封装一层，然后通过 `ref` 和 `findDOMNode`
去获取。

## 总结

- 你可以使用 ref 到的组件定义的任何公共方法，比如 `this.refs.myTypeahead.reset()`
- Refs 是访问到组件内部 DOM 节点唯一**可靠**的方法
- Refs 会自动销毁对子组件的引用（当子组件删除时）

### 注意事项

- 不要在 `render` 或者 `render` 之前访问 `refs`
- 不要滥用 `refs`，比如只是用它来按照传统的方式操作界面 UI：找到 DOM -> 更新 DOM
