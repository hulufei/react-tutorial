# 在 React 应用中使用 Redux

和 Flux 类似，Redux 也是需要注册一个回调函数 `store.subscribe(listener)` 来获取
State 的更新，然后我们要在 `listener` 里面调用 `setState()` 来更新 React
组件。

Redux 官方提供了 [react-redux](https://github.com/rackt/react-redux) 来简化
React 和 Redux 之间的绑定，不再需要像 Flux 那样手动注册／解绑回调函数。

接下来看一下是怎么做到的，react-redux 只有两个 API

## &lt;Provider&gt;

`<Provider>` 作为一个容器组件，用来接受 Store，并且让 Store
对子组件可用，用法如下：

```javascript
import { render } from 'react-dom';
import { Provider } from 'react-redux';
import App from './app';

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

这时候 `<Provider>` 里面的子组件 `<App />` 才可以使用 `connect` 方法关联
store。

[`<Provider>`](https://github.com/rackt/react-redux/blob/master/src/components/Provider.js) 的实现很简单，他利用了 React 一个（暂时）隐藏的特性 `Contexts`，`Context` 用来传递一些父容器的属性对所有子孙组件可见，在某些场景下面避免了用 `props` 传递多层组件的繁琐，要想更详细了解 `Contexts` 可以参考[这篇文章](https://www.tildedave.com/2014/11/15/introduction-to-contexts-in-react-js.html)。

## Connect

`connect()`
这个方法略微复杂一点，主要是因为它的用法非常灵活：`connect([mapStateToProps],
mapDispatchToProps], [mergeProps],
[options])`，它最多接受4个参数，都是可选的，并且这个方法调用会返回另一个函数，这个返回的函数来接受一个组件类作为参数，最后才返回一个和
Redux store 关联起来的新组件，类似这样：

```javascript
class App extends Component { ... }

export default connect()(App);
```

这样就可以在 `App` 这个组件里面通过 `props` 拿到 Store 的 `dispatch`
方法，但是注意现在的 `App` 没有监听 Store 的状态更改，如果要监听 Store
的状态更改，必须要指定 `mapStateToProps` 参数。

先来看它的参数：

- `[mapStateToProps(state, [ownProps]): stateProps]`:
  第一个可选参数是一个函数，只有指定了这个参数，这个关联（connected）组件才会监听
  Redux Store 的更新，每次更新都会调用 `mapStateToProps`
  这个函数，返回一个字面量对象将会合并到组件的 `props` 属性。
  `ownProps` 是可选的第二个参数，它是传递给组件的 `props`，当组件获取到新的
  `props` 时，`ownProps` 都会拿到这个值并且执行 `mapStateToProps` 这个函数。
- `[mapDispatchProps(dispatch, [ownProps]): dispatchProps]`:
  这个函数用来指定如何传递 `dispatch` 给组件，在这个函数里面直接 dispatch
  action creator，返回一个字面量对象将会合并到组件的 `props` 属性，这样关联组件可以直接通过 `props` 调用到 `action`，
  Redux 提供了一个 [`bindActionCreators()`](http://rackt.github.io/redux/docs/api/bindActionCreators.html) 辅助函数来简化这种写法。
  如果省略这个参数，默认直接把 `dispatch` 作为 `props` 传入。`ownProps` 作用同上。

剩下的两个参数比较少用到，更详细的说明参看[官方文档](https://github.com/rackt/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)，其中提供了很多简单清晰的用法示例来说明这些参数。

## 一个具体一点的例子

Redux 创建 Store，Action，Reducer 这部分就省略了，这里只看 react-redux 的部分。

```javascript
import React, { Component } from 'react';
import someActionCreator from './actions/someAction';
import * as actionCreators from './actions/otherAction';

function mapStateToProps(state) {
  return {
    propName: state.propName
  };
}

function mapDispatchProps(dispatch) {
  return {
    someAction: (arg) => dispatch(someActionCreator(arg)),
    otherActions: bindActionCreators(actionCreators, dispatch)
  };
}

class App extends Component {
  render() {
    // `mapStateToProps` 和 `mapDispatchProps` 返回的字段都是 `props`
    const { propName, someAction, otherActions } = this.props;
    return (
      <div onClick={someAction.bind(this, 'arg')}>
        {propName}
      </div>
    );
  }
}

export default connect(mapStateToProps, mapDispatchProps)(App);
```

如前所述，这个 connected 的组件必须放到 `<Provider>` 的容器里面，当 State
更改的时候就会自动调用 `mapStateToProps` 和 `mapDispatchProps` 从而更新组件的 `props`。
组件内部也可以通过 `props` 调用到 action，如果没有省略了
`mapDispatchProps`，组件要触发 action 就必须手动
dispatch，类似这样：`this.props.dispatch(someActionCreator('arg'))`。
