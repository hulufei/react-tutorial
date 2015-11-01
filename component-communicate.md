# 组件间通信

## 父子组件间通信

这种情况下很简单，就是通过 `props` 属性传递，在父组件给子组件设置 `props`，然后子组件就可以通过 `props` 访问到父组件的数据／方法，这样就搭建起了父子组件间通信的桥梁。

```javascript
import React, { Component } from 'react';
import { render } from 'react-dom';

class GroceryList extends Component {
  handleClick(i) {
    console.log('You clicked: ' + this.props.items[i]);
  }

  render() {
    return (
      <div>
        {this.props.items.map((item, i) => {
          return (
            <div onClick={this.handleClick.bind(this, i)} key={i}>{item}</div>
          );
        })}
      </div>
    );
  }
}

render(
  <GroceryList items={['Apple', 'Banana', 'Cranberry']} />, mountNode
);
```

`div` 可以看作一个子组件，指定它的 `onClick` 事件调用父组件的方法。

父组件访问子组件？用 `refs`

## 非父子组件间的通信

使用全局事件 Pub/Sub 模式，在 `componentDidMount` 里面订阅事件，在
`componentWillUnmount` 里面取消订阅，当收到事件触发的时候调用 `setState` 更新
UI。

这种模式在复杂的系统里面可能会变得难以维护，所以看个人权衡是否将组件封装到大的组件，甚至整个页面或者应用就封装到一个组件。

一般来说，对于比较复杂的应用，推荐使用类似 Flux 这种单项数据流架构，参见[Data
Flow](data-flow.md)。
