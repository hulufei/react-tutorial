# Webpack 配置 React 开发环境

[Webpack](http://webpack.github.io/)
是一个前端资源加载/打包工具，只需要相对简单的配置就可以提供前端工程化需要的各种功能，并且如果有需要它还可以被整合到其他比如
Grunt / Gulp 的工作流。

安装 Webpack：`npm install -g webpack`

Webpack 使用一个名为 `webpack.config.js` 的配置文件，要编译 JSX，先安装对应的
loader: `npm install jsx-loader --save-dev`

假设我们在当前工程目录有一个入口文件 `entry.js`，React 组件放置在一个
`components/` 目录下，组件被 `entry.js` 引用，要使用
`entry.js`，我们把这个文件指定输出到 `dist/bundle.js`，Webpack 配置如下：

```javascript
module.exports = {
	entry: './entry.js',
	output: {
		path: __dirname,
		filename: 'bundle.js'
	},
	resolve: {
		extensions: ['', '.js', '.jsx']
	},
	module: {
		loaders: [
			{ test: /\.jsx$/, loaders: ['jsx?harmony'] }
		]
	}
}
```

`resolve` 指定可以被 `require` 的文件后缀。比如 `Hello.jsx`
这样的文件就可以直接用 `require(./Hello)` 引用。

`loaders` 指定 jsx-loader 编译后缀名为 `.jsx` 的文件，建议给含有 JSX
的文件添加 `.jsx` 后缀，当然你也可以直接使用 `.js` 后缀， 相应的 `test`
配置正则要修改匹配就是。

Webpack 内置支持 CommonJS，所以可以直接用 npm 下载安装模块，然后直接 require
使用模块。

- 安装 React: `npm install react --save`
- 使用 React: `var React = require('react');`

监听编译: `webpack -d --watch`

更多关于 Webpack 的介绍

- [webpack-howto](https://github.com/petehunt/webpack-howto)
