# 开发环境配置

要搭建一个现代的前端开发环境配套的工具有很多，比如 Grunt / Gulp / Webpack
/ Broccoli，都是要解决前端工程化问题，这个主题很大，这里为了使用 React 我们只关注其中的两个点：

- JSX 支持
- ES6 支持

好消息是业界领先的 ES6 编译工具 [Babel](http://babeljs.io/) 随着作者被 Facebook [招入麾下](https://twitter.com/sebmck/status/620736636830285824)，已经内置了对 JSX 的支持，我们只需要配置 Babel 一个编译工具就可以了，配合 webpack 非常方便。
