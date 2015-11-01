# Webpack 配置 React 开发环境

[Webpack](http://webpack.github.io/)
是一个前端资源加载/打包工具，只需要相对简单的配置就可以提供前端工程化需要的各种功能，并且如果有需要它还可以被整合到其他比如
Grunt / Gulp 的工作流。

安装 Webpack：`npm install -g webpack`

Webpack 使用一个名为 `webpack.config.js` 的配置文件，要编译 JSX，先安装对应的
loader: `npm install babel-loader --save-dev`

假设我们在当前工程目录有一个入口文件 `entry.js`，React 组件放置在一个
`components/` 目录下，组件被 `entry.js` 引用，要使用
`entry.js`，我们把这个文件指定输出到 `dist/bundle.js`，Webpack 配置如下：

```javascript
var path = require('path');

module.exports = {
	entry: './entry.js',
	output: {
		path: path.join(__dirname, '/dist'),
		filename: 'bundle.js'
	},
	resolve: {
		extensions: ['', '.js', '.jsx']
	},
	module: {
		loaders: [
			{ test: /\.js|jsx$/, loaders: ['babel'] }
		]
	}
}
```

`resolve` 指定可以被 `import` 的文件后缀。比如 `Hello.jsx`
这样的文件就可以直接用 `import Hello from 'Hello'` 引用。

`loaders` 指定 babel-loader 编译后缀名为 `.js` 或者 `.jsx`
的文件，这样你就可以在这两种类型的文件中自由使用 JSX 和 ES6 了。

监听编译: `webpack -d --watch`

更多关于 Webpack 的介绍

- [webpack-howto](https://github.com/petehunt/webpack-howto)
