# JSX

## 为什么要引入 JSX 这种语法

传统的 MVC
是将模板放在其他地方，比如 `<script>` 标签或者模板文件，再在 JS 中通过某种手段引用模板。按照这种思路想想多少次我们面对四处分散的模板片段不知所措？纠结模板引擎，纠结模板存放位置，纠结如何引用模板……下面 React 想法：

> We strongly believe that components are the right way to separate concerns
> rather than "templates" and "display logic." We think that markup and the
> code that generates it are intimately tied together. Additionally, display
> logic is often very complex and using template languages to express it
> becomes cumbersome.

简单来说，React
认为**组件**才是王道，而组件是和模板紧密关联的，组件模板和组件逻辑分离让问题复杂化了。显而易见的道理，关键是怎么做？

所以就有了 JSX 这种语法，就是为了把 HTML 模板直接嵌入到 JS
代码里面，然后通过一个工具编译输出纯粹的 JS 代码。

## JSX 是可选的

我们可以直接用 React 提供的 DOM 构建方法来写模板，比如一个 JSX
写的一个链接：

```html
<a href="http://facebook.github.io/react/">Hello!</a>
```

用 JS 代码来写就成这样了：

```javascript
React.createElement('a', {href: 'http://facebook.github.io/react/'}, 'Hello!')
```

你可以通过 `React.createElement` 来构造组件的 DOM
树。第一个参数是标签名，第二个参数是属性对象，第三个参数是子元素。

一个包含子元素的例子：

```javascript
var child = React.createElement('li', null, 'Text Content');
var root = React.createElement('ul', { className: 'my-list' }, child);
React.render(root, document.body);
```

对于常见的 HTML 标签，React 已经内置了工厂方法：

```javascript
var root = React.DOM.ul({ className: 'my-list' },
             React.DOM.li(null, 'Text Content')
           );
```

所以 JSX 和 JS 之间的转换也很简单直观，用 JSX 的好处就是它基本上就是
HTML（后面会讲到有一些小差异），对于构造 DOM 来说我们更熟悉，更具可读性。

关于 JSX 映射成 JS 对象，也就是 Virtual DOM 的内部描述，参见[Virtual DOM
Terminology](http://facebook.github.io/react/docs/glossary.html)，如果你不想使用
JSX，直接使用 JS 就是用这里面提到的接口方法。
