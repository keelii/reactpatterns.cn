import "highlight.js/styles/github-gist.css";
import "../styles/old-layout.css";
import Head from "next/head";

# React 模式（中文版） [on Github](https://github.com/keelii/reactpatterns.cn)

## 目录

## 函数组件 (Function component)

[函数组件](https://reactjs.org/docs/components-and-props.html#function-and-class-components) 是最简单的一种声明可复用组件的方法

他们就是一些简单的函数。

```jsx
function Greeting() {
  return <div>Hi there!</div>;
}
```

从第一个形参中获取属性集 (props)

```jsx
function Greeting(props) {
  return <div>Hi {props.name}!</div>;
}
```

按自己的需要可以在函数组件中定义任意变量

**最后一定要返回你的 React 组件。**

```jsx
function Greeting(props) {
  let style = {
    fontWeight: "bold",
    color: context.color
  };

  return <div style={style}>Hi {props.name}!</div>;
}
```

使用 `defaultProps` 为任意必有属性设置默认值

```jsx
function Greeting(props) {
  return <div>Hi {props.name}!</div>;
}
Greeting.defaultProps = {
  name: "Guest"
};
```

---

## 属性解构 (Destructuring props)

[解构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 是一种 JavaScript 特性。  

出自 ES2015 版的 JavaScript 新规范。

所以看起来可能并不常见。

好比字面量赋值的反转形式。

```js
let person = { name: "chantastic" };
let { name } = person;
```

同样适用于数组。

```js
let things = ["one", "two"];
let [first, second] = things;
```

解构赋值被用在很多 [函数组件](#函数组件-function-component) 中。

下面声明的这些组件是相同的。

```jsx
function Greeting(props) {
  return <div>Hi {props.name}!</div>;
}

function Greeting({ name }) {
  return <div>Hi {name}!</div>;
}
```

有一种语法可以在对象中收集剩余属性。

叫做 [剩余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)，看起来就像这样。

```jsx
function Greeting({ name, ...restProps }) {
  return <div>Hi {name}!</div>;
}
```

那三个点 (`...`) 会把所有的剩余属性分配给 `restProps` 对象

然而，你能使用 `restProps` 做些什么呢？

继续往下看...

---

## JSX 中的属性展开 (JSX spread attributes)

属性展开是 [JSX](https://reactjs.org/docs/introducing-jsx.html) 中的一个的特性。

它是一种语法，专门用来把对象上的属性转换成 JSX 中的属性

参考上面的 [属性解构](#属性解构-(Destructuring-props)),  
我们可以 **扩散** `restProps` 对象的所有属性到 div 元素上

```jsx
function Greeting({ name, ...restProps }) {
  return <div {...restProps}>Hi {name}!</div>;
}
```

这让 `Gretting` 组件变得非常灵活。

我们可以通过传给 Gretting 组件 DOM 属性并确定这些属性一定会被传到 `div` 上

```jsx
<Greeting name="Fancy pants" className="fancy-greeting" id="user-greeting" />
```

避免传递非 DOM 属性到组件上。
解构赋值是如此的受欢迎，是因为它可以分离 `组件特定的属性` 和 `DOM/平台特定属性`

```jsx
function Greeting({ name, ...platformProps }) {
  return <div {...platformProps}>Hi {name}!</div>;
}
```

---

## 合并解构属性和其它值 (Merge destructured props with other values)

组件就是一种抽象。

好的抽象是可以扩展的。

比如说下面这个组件使用 `class` 属性来给按钮添加样式。

```jsx
function MyButton(props) {
  return <button className="btn" {...props} />;
}
```
一般情况下这样做就够了，除非我们需要扩展其它的样式类

```jsx
<MyButton className="delete-btn">Delete...</MyButton>
```
在这个例子中把 `btn` 替换成 `delete-btn`

[JSX 中的属性展开](#JSX-中的属性展开-(JSX-spread-attributes)) 对先后顺序是敏感的

扩散属性中的 `className` 会覆盖组件上的 `className`。

我们可以改变它两的顺序，但是目前来说 `className` 只有 `btn`。

```jsx
function MyButton(props) {
  return <button {...props} className="btn" />;
}
```
我们需要使用解构赋值来合并入参 props 中的 `className` 和基础的（组件中的） `className`。
可以通过把所有的值放在一个数组里面，然后使用一个空格连接它们。

```jsx
function MyButton({ className, ...props }) {
  let classNames = ["btn", className].join(" ");

  return <button className={classNames} {...props} />;
}
```

为了保证 `undefined` 不被显示在 className 上，可以使用 [默认值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Default_values_2)。

```jsx
function MyButton({ className = "", ...props }) {
  let classNames = ["btn", className].join(" ");

  return <button className={classNames} {...props} />;
}
```

## 条件渲染 (Conditional rendering)

不可以在一个组件声明中使用 if/else 语句
You can't use if/else statements inside a component declarations.  
所以可以使用 [条件（三元）运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) 和 [短路计算](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation)。

### `如果`

```jsx
{
  condition && <span>Rendered when `truthy`</span>;
}
```

### `除非`

```jsx
{
  condition || <span>Rendered when `falsy`</span>;
}
```

### `如果-否则`

```jsx
{
  condition ? (
    <span>Rendered when `truthy`</span>
  ) : (
    <span>Rendered when `falsy`</span>
  );
}
```

## 子元素类型 (Children types)

很多类型都可以做为 React 的子元素。

多数情况下会是 `数组` 或者 `字符串`。

### 字符串 `String`

```jsx
<div>Hello World!</div>
```

### 数组 `Array`

```jsx
<div>{["Hello ", <span>World</span>, "!"]}</div>
```

## 数组做为子元素 (Array as children)

将数组做为子元素是很常见的。

列表是如何在 React 中被绘制的。

我们使用 `map()` 方法创建一个新的 React 元素数组

```jsx
<ul>
  {["first", "second"].map(item => (
    <li>{item}</li>
  ))}
</ul>
```

这和使用字面量数组是一样的。

```jsx
<ul>{[<li>first</li>, <li>second</li>]}</ul>
```
这个模式可以联合解构、JSX 属性扩散以及其它组件一起使用，看起来简洁无比

```jsx
<ul>
  {arrayOfMessageObjects.map(({ id, ...message }) => (
    <Message key={id} {...message} />
  ))}
</ul>
```

## 函数做为子元素 (Function as children)

React 组件不支持函数类型的子元素。

然而 [渲染属性](#渲染属性-render-prop) 是一种可以创建组件并以函数作为子元素的模式。

## 渲染属性 (Render prop)

这里有个组件，使用了一个渲染回调函数 children。

这样写并没有什么用，但是可以做为入门的简单例子。

```jsx
const Width = ({ children }) => children(500);
```
组件把 children 做为函数调用，同时还可以传一些参数。上面这个 `500` 就是实参。

为了使用这个组件，我们可以在调用组件的时候传入一个子元素，这个子元素就是一个函数。

```jsx
<Width>{width => <div>window is {width}</div>}</Width>
```

我们可以得到下面的输出。

```jsx
<div>window is 500</div>
```
有了这个组件，我们就可以用它来做渲染策略。

```jsx
<Width>
  {width => (width > 600 ? <div>min-width requirement met!</div> : null)}
</Width>
```

如果有更复杂的条件判断，我们可以使用这个组件来封装另外一个新组件来利用原来的逻辑。

```jsx
const MinWidth = ({ width: minWidth, children }) => (
  <Width>{width => (width > minWidth ? children : null)}</Width>
);
```
显然，一个静态的 `Width` 组件并没有什么用处，但是给它绑定一些浏览器事件就不一样了。下面有个实现的例子。

```jsx
class WindowWidth extends React.Component {
  constructor() {
    super();
    this.state = { width: 0 };
  }

  componentDidMount() {
    this.setState(
      { width: window.innerWidth },
      window.addEventListener("resize", ({ target }) =>
        this.setState({ width: target.innerWidth })
      )
    );
  }

  render() {
    return this.props.children(this.state.width);
  }
}
```

许多开发人员都喜欢 [高阶组件](#高阶组件-higher-order-component) 来实现这种功能。但这只是个人喜好问题。

## 子组件的传递 (Children pass-through)

你可能会创建一个组件，这个组件会使用 `context` 并且渲染它的子元素。

```jsx
class SomeContextProvider extends React.Component {
  getChildContext() {
    return { some: "context" };
  }

  render() {
    // 如果能直接返回 `children` 就完美了
  }
}
```

你将面临一个选择。把 `children` 包在一个 div 中并返回，或者直接返回 `children`。第一种情况需要要你添加额外的标记（这可能会影响到你的样式）。第二种将产生一个没什么用处的错误。

```jsx
// option 1: extra div
return <div>{children}</div>;

// option 2: unhelpful errors
return children;
```

最好把 `children` 做为一种不透明的数据类型对待。React 提供了 `React.Children` 方法来处理 `children`。

```jsx
return React.Children.only(this.props.children);
```

## 代理组件 (Proxy component)

_(我并不确定这个名字的准确叫法 `译：代理、中介、装饰?`)_

按钮在 web 应用中随处可见。并且所有的按钮都需要一个 `type="button"` 的属性。

```jsx
<button type="button">
```

重复的写这些属性很容易出错。我们可以写一个高层组件来代理 `props` 到底层组件。

```jsx
const Button = props =>
  <button type="button" {...props}>
```

我们可以使用 `Button` 组件代替 `button` 元素，并确保 `type` 属性始终是 button。

```jsx
<Button />
// <button type="button"><button>

<Button className="CTA">Send Money</Button>
// <button type="button" class="CTA">Send Money</button>
```

## 样式组件 (Style component)

这也是一种 [代理组件](#代理组件-proxy-component)，用来处理样式。

假如我们有一个按钮，它使用了「primary」做为样式类。

```jsx
<button type="button" className="btn btn-primary">
```

我们使用一些单一功能组件来生成上面的结构。

```jsx
import classnames from "classnames";

const PrimaryBtn = props => <Btn {...props} primary />;

const Btn = ({ className, primary, ...props }) => (
  <button
    type="button"
    className={classnames("btn", primary && "btn-primary", className)}
    {...props}
  />
);
```

可以可视化的展示成下面的样子。

```jsx
PrimaryBtn()
  ↳ Btn({primary: true})
    ↳ Button({className: "btn btn-primary"}, type: "button"})
      ↳ '<button type="button" class="btn btn-primary"></button>'
```

使用这些组件，下面的这几种方式会得到一致的结果。

```jsx
<PrimaryBtn />
<Btn primary />
<button type="button" className="btn btn-primary" />
```

这对于样式维护来说是非常好的。它将样式的所有关注点分离到单个组件上。

## 组织事件 (Event switch)

当我们在写事件处理函数的时候，通常会使用 `handle{事件名字}` 的命名方式。

```jsx
handleClick(e) { /* do something */ }
```
当需要添加很多事件处理函数的时候，这些函数名字会显得很重复。这些函数的名字并没有什么价值，因为它们只代理了一些动作或者函数。

```jsx
handleClick() { require("./actions/doStuff")(/* action stuff */) }
handleMouseEnter() { this.setState({ hovered: true }) }
handleMouseLeave() { this.setState({ hovered: false }) }
```

可以考虑写一个事件处理函数来根据不同的 `event.type` 来组织事件。

```jsx
handleEvent({type}) {
  switch(type) {
    case "click":
      return require("./actions/doStuff")(/* action dates */)
    case "mouseenter":
      return this.setState({ hovered: true })
    case "mouseleave":
      return this.setState({ hovered: false })
    default:
      return console.warn(`No case for event type "${type}"`)
  }
}
```
另外，对于简单的组件，你可以在组件中使用箭头函数直接调用导入的动作或者函数

```jsx
<div onClick={() => someImportedAction({ action: "DO_STUFF" })}
```

在遇到性能问题之前，不要担心性能优化。真的不要

## 布局组件 (Layout component)

布局组件表现为一些静态 DOM 元素的形式。它们一般并不需要经常更新。

就像下面的这个组件一样，两边各自渲染了一个 children。

```jsx
<HorizontalSplit
  leftSide={<SomeSmartComponent />}
  rightSide={<AnotherSmartComponent />}
/>
```

我们可以优化这个组件。

HorizontalSplit 组件是两个子组件的父元素，我们可以告诉组件永远都不要更新

```jsx
class HorizontalSplit extends React.Component {
  shouldComponentUpdate() {
    return false;
  }

  render() {
    <FlexContainer>
      <div>{this.props.leftSide}</div>
      <div>{this.props.rightSide}</div>
    </FlexContainer>
  }
}
```

## 容器组件 (Container component)

「容器用来获取数据然后渲染到子组件上，仅仅如此。」&mdash;[Jason Bonta](https://twitter.com/jasonbonta)

这有一个 `CommentList` 组件。

```jsx
const CommentList = ({ comments }) => (
  <ul>
    {comments.map(comment => (
      <li>
        {comment.body}-{comment.author}
      </li>
    ))}
  </ul>
);
```
我们可以创建一个新组件来负责获取数据渲染到上面的 `CommentList` 函数组件中。

```jsx
class CommentListContainer extends React.Component {
  constructor() {
    super()
    this.state = { comments: [] }
  }

  componentDidMount() {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: comments =>
        this.setState({comments: comments});
    })
  }

  render() {
    return <CommentList comments={this.state.comments} />
  }
}
```

对于不同的应用上下文，我们可以写不同的容器组件。

## 高阶组件 (Higher-order component)

[高阶函数](https://zh.wikipedia.org/zh-cn/%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0) 是至少满足下列一个条件的函数：

* 接受一个或多个函数作为输入
* 输出一个函数

所以高阶组件又是什么呢？

如果你已经用过 [容器组件](#容器组件-container-component), 这仅仅是一些泛化的组件, 包裹在一个函数中。

让我们以 `Greeting` 组件开始

```jsx
const Greeting = ({ name }) => {
  if (!name) {
    return <div>连接中...</div>;
  }

  return <div>Hi {name}!</div>;
};
```

如果 `props.name` 存在，组件会渲染这个值。否则将展示「连接中...」。现在来添加点高阶的感觉

```jsx
const Connect = ComposedComponent =>
  class extends React.Component {
    constructor() {
      super();
      this.state = { name: "" };
    }

    componentDidMount() {
      // this would fetch or connect to a store
      this.setState({ name: "Michael" });
    }

    render() {
      return <ComposedComponent {...this.props} name={this.state.name} />;
    }
  };
```
这是一个返回了入参为组件的普通函数

接着，我们需要把 `Greeting` 包裹到 `Connect` 中

```jsx
const ConnectedMyComponent = Connect(Greeting);
```

这是一个强大的模式，它可以用来获取数据和给定数据到任意 [函数组件](#函数组件-function-component) 中。

## 状态提升 (State hoisting)

[函数组件](#函数组件-function-component) 没有状态 (就像名字暗示的一样)。

事件是状态的变化。

它们的数据需要传递给状态化的父 [容器组件](#容器组件-container-component)


这就是所谓的「状态提升」。

它是通过将回调从容器组件传递给子组件来完成的

```jsx
class NameContainer extends React.Component {
  render() {
    return <Name onChange={newName => alert(newName)} />;
  }
}

const Name = ({ onChange }) => (
  <input onChange={e => onChange(e.target.value)} />
);
```

`Name` 组件从 `NameContainer` 组件中接收 `onChange` 回调，并在 input 值变化的时候调用。

上面的 `alert` 调用只是一个简单的演示，但它并没有改变状态

让我们来改变 `NameContainer` 组件的内部状态。

```jsx
class NameContainer extends React.Component {
  constructor() {
    super();
    this.state = { name: "" };
  }

  render() {
    return <Name onChange={newName => this.setState({ name: newName })} />;
  }
}
```

这个状态 _被提升_ 到了容器中，通过添加回调函数，回调中可以更新本地状态。这就设置了一个很清晰边界，并且使功能组件的可重用性最大化。

这个模式并不限于函数组件。因为函数组件没有生命周期事件，你也可以在类组件中使用这种模式。

_[受控输入](#受控输入-controlled-input) 是一种与状态提升同时使用时很重要的模式_

_(最好是在一个状态化的组件上处理事件对象)_

## 受控输入 (Controlled input)

讨论受控输入的抽象并不容易。让我们以一个不受控的（通常）输入开始。

```jsx
<input type="text" />
```

当你在浏览器中调整此输入时，你会看到你的更改。 这个是正常的

受控的输入不允许 DOM 变更，这使得这个模式成为可能。通过在组件范围中设置值而不是直接在 DOM 范围中修改

```jsx
<input type="text" value="This won't change. Try it." />
```

显示静态的输入框值对于用户来说并没有什么用处。所以，我们从状态中传递一个值到 input 上。

```jsx
class ControlledNameInput extends React.Component {
  constructor() {
    super();
    this.state = { name: "" };
  }

  render() {
    return <input type="text" value={this.state.name} />;
  }
}
```

然后当你改变组件的状态的时候 input 的值就自动改变了。

```jsx
return (
  <input
    value={this.state.name}
    onChange={e => this.setState({ name: e.target.value })}
  />
);
```
这是一个受控的输入框。它只会在我们的组件状态发生变化的时候更新 DOM。这在创建一致 UI 界面的时候非常有用。

_如果你使用 [函数组件](#函数组件-function-component) 做为表单元素，那就得阅读 [状态提升](#状态提升-state-hoisting) 一节，把状态转移到上层的组件树上。_
