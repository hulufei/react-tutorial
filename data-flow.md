# Data Flow

React 里面有所谓的“单向数据绑定”一说，像数据通过 `props` 从父级往下传递。 这种数据绑定（流向）的方式对开发者来说是完全透明的，并且也完全由开发者来控制。

Data Flow
只是一种应用架构的方式，比如数据如何存放，在哪里触发事件等等，所以它不是 React
提供的额外的什么新功能，可以看成是使用 React
构建大型应用的一种最佳实践。

正因为它是这样一种概念，所以涌现了[许多实现](https://github.com/enaqx/awesome-react#flux)，这里主要关注两种实现：

- 官方的 [Flux](http://facebook.github.io/flux/docs/overview.html)
- 更优雅的 [Reflux](https://github.com/spoike/refluxjs)
