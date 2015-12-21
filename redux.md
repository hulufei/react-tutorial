# Redux

[Dan Abramov](https://medium.com/@dan_abramov) 在 React Europe 2015 上作了一场令人印象深刻的演示 [Hot Reloading
with Time Travel](https://www.youtube.com/watch?v=xsSnOQynTHs)，之后 Redux
迅速成为最受人关注的 Flux 实现之一。

[Redux](https://github.com/rackt/redux) 把自己标榜为一个“可预测的状态容器”，其实也是 Flux
里面“单向数据流”的思想，只是它充分利用函数式的特性，让整个实现更加优雅纯粹，使用起来也更简单。

`Redux(oldState) => newState`

Redux 是超越 Flux 的一次进化。