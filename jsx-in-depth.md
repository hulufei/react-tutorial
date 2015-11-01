# 使用 JSX

利用 JSX 编写 DOM 结构，可以用原生的 HTML 标签，也可以直接像普通标签一样引用 React
组件。这两者约定通过大小写来区分，小写的**字符串**是 HTML 标签，大写开头的**变量**是 React 组件。

**使用 HTML 标签：**

```javascript
import React from 'react';
import { render } from 'react-dom';

var myDivElement = <div className="foo" />;
render(myDivElement, document.getElementById('mountNode'));
```

_HTML 里的 `class` 在 JSX 里要写成 `className`，因为 `class` 在 JS 里是保留关键字。同理某些属性比如 `for` 要写成 `htmlFor`。_

**使用组件：**

```javascript
import React from 'react';
import { render } from 'react-dom';
import MyComponent from './MyComponet';

var myElement = <MyComponent someProperty={true} />;
render(myElement, document.body);
```

## 使用 JavaScript 表达式

属性值使用表达式，只要用 `{}` 替换 `""`:

```javascript
// Input (JSX):
var person = <Person name={window.isLoggedIn ? window.name : ''} />;
// Output (JS):
var person = React.createElement(
  Person,
  {name: window.isLoggedIn ? window.name : ''}
);
```

子组件也可以作为表达式使用：

```javascript
// Input (JSX):
var content = <Container>{window.isLoggedIn ? <Nav /> : <Login />}</Container>;
// Output (JS):
var content = React.createElement(
  Container,
  null,
  window.isLoggedIn ? React.createElement(Nav) : React.createElement(Login)
);
```

## 注释

在 JSX 里使用注释也很简单，就是沿用
JavaScript，唯一要注意的是在一个组件的子元素位置使用注释要用 `{}` 包起来。

```javascript
var content = (
  <Nav>
      {/* child comment, put {} around */}
	  <Person
		/* multi
		   line
		   comment */
		name={window.isLoggedIn ? window.name : ''} // end of line comment
	  />
  </Nav>
);
```

## HTML 转义

React 会将所有要显示到 DOM 的字符串转义，防止 XSS。所以如果 JSX
中含有转义后的实体字符比如 `&copy;` (©) 最后显示到 DOM 中不会正确显示，因为
React 自动把 `&copy;` 中的特殊字符转义了。有几种解决办法：

- 直接使用 UTF-8 字符 ©
- 使用对应字符的 Unicode
编码，[查询编码](http://www.fileformat.info/info/unicode/char/00a9/index.htm)
- 使用数组组装 `<div>{['cc ', <span>&copy;</span>, ' 2015']}</div>`
- 直接插入原始的 HTML

```html
<div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />
```

## 自定义 HTML 属性

如果在 JSX 中使用的属性不存在于 HTML
的规范中，这个属性会被忽略。如果要使用自定义属性，可以用 `data-` 前缀。

[可访问性](http://www.w3.org/WAI/intro/aria)属性的前缀 `aria-` 也是支持的。

## [支持的标签和属性](http://facebook.github.io/react/docs/tags-and-attributes.html)

如果你要使用的某些标签或属性不在这些支持列表里面就可能被 React
忽略，必须要使用的话可以提 issue，或者用前面提到的 `dangerouslySetInnerHTML`。

