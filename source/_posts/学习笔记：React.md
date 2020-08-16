---
title: 学习笔记：React
date: 2020-08-07 10:46:01
tags: [ React ]
---

## 核心概念

### JSX 简介

* JSX 是一个 JavaScript 的语法扩展
* JSX 可以生成 React 元素

```jsx
const element = <h1>Hello, world!</h1>;
```

#### 为什么使用 JSX？

* React 认为**渲染逻辑**本质上与其他 UI 逻辑内在耦合

* React 并没有采用将*标记与逻辑进行分离到不同文件*这种人为地分离方式，而是通过将二者共同存放在称之为“组件”的松散耦合单元之中，来实现[*关注点分离*](https://en.wikipedia.org/wiki/Separation_of_concerns)。

	* > 关注点分离在[计算机科学](https://zh.wikipedia.org/wiki/計算機科學)中，是将计算机程序分隔为不同部分的设计原则，是[面向对象的程序设计](https://zh.wikipedia.org/wiki/面向对象的程序设计)的核心概念。

### 元素渲染

* 元素是构成 React 应用的最小砖块

> 与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

##### 将一个元素渲染为 DOM

* 想要将一个 React 元素渲染到根 DOM 节点中，只需把它们一起传入  `React DOM.render()`

```jsx
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

#### 更新已渲染的元素

* React 元素是不可变对象。一个元素就像电影的单帧，代表了某个特定时刻的 UI
* 更新 UI 唯一的方式是创建一个全新的元素，并将其传入 `React DOM.render()`

#### React 只更新它需要更新的部分

> React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

### 组件 & Props

* 组件允许你将 UI 拆分为独立可复用的代码片段，并对每个片段进行独立构思
* 组件，从概念上类似于 JavaScript 函数。接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素

#### 渲染组件

> 当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）以及子组件（children）转换为单个对象传递给组件，这个对象被称之为 “props”。
>
> **注意：** 组件名称必须以大写字母开头。

* props 是只读的

### State & 生命周期

#### 正确地使用 State

* 不要直接修改 State，用 `setState({ key: value })`
* State  的更新可能是异步的
	* 出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用
	* 因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态
	* 要解决这个问题，可以让 `setState()` 接收一个函数而不是一个对象
* 调用 `setState()` 的时候，React 会把对象合并到当前的 state，因此只需调用需要更改的部分
* **不要将 props 的值复制给 state**，props 更新不会影响 state，**只有在刻意忽略 prop 更新的情况下使用**

##### 数据是自上而下的单向数据流

#### 生命周期

##### 生命周期方法

* componentDidMount：组件已经被渲染到 DOM 中后运行
* componentWillUnmount：组件从 DOM 中被移除时运行

### 事件处理

* 事件命名采用小驼峰，传入函数作为事件处理函数

```html
<!-- html -->
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

```jsx
// jsx
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

* 不能通过 `return false` 阻止默认行为，必须显示使用 `preventDefault`

```html
<!-- html -->
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

```jsx
// jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

> e 是一个合成事件，根据 W3C 规范定义 e，不必担心跨浏览器的兼容问题

* 通常的做法是将事件处理函数声明为 class 中的方法

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 为了在回调中使用 `this`，这个绑定是必不可少的
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

> 在 JavaScript 中，class 的方法默认不会[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。
>
> 如果觉得使用 `bind` 很麻烦，这里有两种方式可以解决
>
> * 实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)
> * 回调中使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
> 	* 每次渲染都会创建不同的回调函数，如果传入子组件会进行额外的重新渲染
> 	* 建议不要使用这种方式

* 向事件处理程序传递参数

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

### 条件渲染

* 依据应用的不同状态，只渲染对应状态下的部分内容

* 使用变量来储存元素，称为元素变量
* 通过花括号包裹代码可以嵌入表达式
	* &&
	* 三目运算符
* `return null` 不进行渲染，不影响组件生命周期

### 列表 & Key

* key 帮助 React 识别哪些元素改变了，应当给数组中的每一个元素赋予一个确定的标识
* 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串
* 一个好的经验法则是：在 `map()` 方法中的元素需要设置 key 属性
* 数组元素中使用的 key 在其兄弟节点之间应该是独一无二的，不需要全局唯一
* key 会传递信息给 React ，但不会传递给组件

### 表单

#### 受控组件

* state 成为“唯一数据源”，组件控制表单操作
* 对于受控组件来说，输入的值始终由 React 的 state 驱动
* 当需要处理多个 `input` 元素时，可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作
* 受控组件组织用户更改输入，如果指定 `value` 仍可以编辑，可能 `value` 为 `undefined` 或 `null`

### 状态提升

* 多个组件需要反映相同的变化数据，建议将共享状态提升到最近的共同父组件中

> 任何可变数据应当只有一个相对应的唯一“数据源
>
> state 都是首先添加到需要渲染数据的组件中去。然后，如果其他组件也需要这个 state，那么你可以将它提升至这些组件的最近共同父组件中
>
> 应当依靠[自上而下的数据流](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#the-data-flows-down)，而不是尝试在不同组件间同步 state

### 组合 vs 继承

* 推荐使用组合实现代码重用

#### 包含关系

* 通过 `props.children` 将获取所有子标签

#### 特例关系

* “特殊”组件可以通过 props 定制并渲染“一般”组件

> 不建议用继承。
>
> 组件可以接受任意 props，包括基本数据类型，React 元素以及函数。
>
> 如果你想要在组件间复用非 UI 的功能，我们建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（import）而无需通过 extend 继承它们。

### React 哲学

1. 将 UI 划分为组件层级
	* 单一功能原则

2. 创建一个不含交互功能的静态版本
	* 将渲染 UI 和添加交互这两个过程分开
	* 该阶段**不应该使用 state**
	* 自上而下：适合比较简单的项目
	* 自下而上：适合大型的项目，并同时为低层组件编写测试
3. 确定 UI state 的最小表示
	* DRY ( Don't Repeat Yourself )
	* 只保留应用所需的可变 state 的最小集合，其他数据均由它们计算产生
		* 如：只需要保存数组，无需保存数组长度，因为长度可以通过数组的 length 属性获取
	* check list：
		* 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
		* 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
		* 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。
4. 确定 state 放置的位置
	* 对于应用中的每一个 state：
		- 找到根据这个 state 进行渲染的所有组件。
		- 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
		- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
		- 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。
5. 添加反向数据流
	* 处于较低层级的组件更新较高层级的组件中的 state
	* 父组件将一个能够触发 state 改变的回调函数（callback）传递给子组件

## 高级指引

### 无障碍

### 代码分割

* 代码分割能够“懒加载”当前用户所需要的内容，显著地提高应用性能
* 没有减少应用整体的代码体积，可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量

#### 动态 import() 语法

```jsx
import { add } from './math';
console.log(add(16, 26));
```

```jsx
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

> 当使用 [Babel](https://babel.docschina.org/) 时，你要确保 Babel 能够解析动态 import 语法而不是将其进行转换。对于这一要求你需要 [babel-plugin-syntax-dynamic-import](https://yarnpkg.com/en/package/babel-plugin-syntax-dynamic-import) 插件。

#### React.lazy

```jsx
import OtherComponent from './OtherComponent';
```

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

> `React.lazy` 接受一个函数，这个函数需要动态调用 `import()`。它必须返回一个 `Promise`，该 Promise 需要 resolve 一个 `default` export 的 React 组件。
>
> 然后应在 `Suspense` 组件中渲染 lazy 组件，`fallback` 属性接受任何在组件加载过程中你想展示的 React 元素。你可以将 `Suspense` 组件置于懒加载组件之上的任何位置。你甚至可以用一个 `Suspense` 组件包裹多个懒加载组件。

```jsx
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

#### 异常捕获边界（Error boundaries）

* 如果模块加载失败（如网络问题），它会触发一个错误，可以通过[异常捕获边界（Error boundaries）](https://zh-hans.reactjs.org/docs/error-boundaries.html)技术来处理这些情况

#### 基于路由的代码分割

#### 命名导出（Named Exports）

> `React.lazy` 目前只支持默认导出（default exports）。如果你想被引入的模块使用命名导出（named exports），你可以创建一个中间模块，来重新导出为默认模块。这能保证 tree shaking 不会出错，并且不必引入不需要的组件。

### Context

* 组件之间共享的值，如 主题、当前用户、首选语言 等全局属性
* API
	* `React.createContext`
	* `Context.Provider`
	* `Class.contextType`
	* `Context.Consumer`
	* `Context.displayName`

### 错误边界（Error Boundaries）

* 错误边界是一种 React 组件，这种组件**可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI**，而不是渲染那些崩溃了的子组件树。
* 错误边界在渲染期间、生命周期方法和整个组件树的构造函数中捕获错误。
* 错误边界的工作方式类似于 JavaScript 的 `catch {}`，不同的地方在于错误边界只针对 React 组件。
* 只有 class 组件才可以成为错误边界组件。
* 大多数情况下，你只需要声明一次错误边界组件，并在整个应用中使用它。
* 注意**错误边界仅可以捕获其子组件的错误**，它无法捕获其自身的错误。
* **任何未被错误边界捕获的错误将会导致整个 React 组件树被卸载**。

#### 关于事件处理器

* 错误边界**无法**捕获事件处理器内部的错误。
* 如果需要在事件处理器内部捕获错误，使用普通的 JavaScript `try` / `catch` 语句。

### Refs & DOM

* 在典型的 React 数据流中，[props](https://zh-hans.reactjs.org/docs/components-and-props.html) 是父组件与子组件交互的唯一方式
* Refs 提供了一种方式，允许访问 DOM 节点或在 render 方法中创建的 React 元素

#### 场景

* 管理焦点，文本选择或媒体播放
* 触发强制动画
* 集成第三方 DOM 库

#### 勿过度使用 Refs

* 创建：构造函数中 `this.myRef = React.createRef()`
* 访问：`const node = this.myRef.current`
	* 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
	* 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
	* **不能在函数组件上使用 `ref` 属性**，因为他们没有实例。

### Refs 转发

* Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧

### 非受控组件

* 受控组件，表单数据是由 React 组件来管理的
* 非受控组件，表单数据将交由 DOM 节点来处理
	* 可以 [使用 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 来从 DOM 节点中获取表单数据
* 默认值
	* 在 React 渲染生命周期时，表单元素上的 `value` 将会覆盖 DOM 节点中的值，在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 `defaultValue` 属性，而不是 `value`。
* 在 React 中，`<input type="file" />` 始终是一个非受控组件，因为它的值只能由用户设置，而不能通过代码控制。

### Fragments

* Fragments 将子列表分组，无需向 DOM 添加额外节点

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment key={...}>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

#### 短语法（不支持 key）

```jsx
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

### 高阶组件（Higher-Order Components）

* 用于复用组件逻辑的一种高级技巧，基于 React 的组合特性而形成的**设计模式**
* 组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件
* **高阶组件是参数为组件，返回值为新组件的函数**

### Render Props

* **render prop 是一个用于告知组件需要渲染什么内容的函数 prop**
* 可以使用带有 render prop 的常规组件来实现大多数[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)

### 与第三方库协同

* React 不会理会 React 自身之外的 DOM 操作
* 它根据内部虚拟 DOM 来决定是否需要更新，而且如果同一个 DOM 节点被另一个库操作了，React 会觉得困惑而且没有办法恢复。

### 深入 JSX

* 实际上，JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖

### Profiler

* 将子节点渲染到存在于父组件以外的 DOM 节点

`ReactDOM.createPortal(child, container)`

* 场景当父组件有 `overflow: hidden` 或 `z-index` ，但需要子组件能够在视觉上“跳出”其容器。
	* 如对话框、悬浮卡以及提示框
* 其他方面，其行为和普通的 React 子节点行为一致
	* 如冒泡事件，从 portal 内部触发的事件会一直冒泡至包含React 树的祖先，即便这些元素并不是DOM 树中的祖先

### 性能优化

### Profiler

* 测量渲染一个 React 应用多久渲染一次以及渲染一次的“代价”，目的是识别渲染慢的部分，以便优化

### 静态类型检查

* PropTypes

* 建议大型代码库中使用 Flow 或 TypeScript 来代替 `PropTypes`

### 严格模式

### 协调（diffing 算法）& key

> 当组件的 props 或 state 发生变化时，React 通过将最新返回的元素与原先渲染的元素进行比较，来决定是否有必要进行一次实际的 DOM 更新。当它们不相等时，React 才会更新 DOM。这个过程被称为“协调”。

* 对比不同类型的元素
	* 根节点为不同类型的元素时，React 会拆卸原有的树并建立新的树
	* 当拆卸一棵树时，对应的 DOM 节点也会被销毁，执行 `componentWillUnmount`
	* 当建立一棵新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中，执行 `componentWillMount` 及 `componentDidMount`

* 对比同类型的元素
	* 保留 DOM 节点，仅比对及更新有改变的属性

* 对比同类型的组件元素
	* 当一个组件更新时，组件实例保持不变
* 对子节点进行递归
	* 在默认条件下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation
	* 直接列表尾部插入没问题，但首部插入时，无法感知原来的元素被移动了，都会因匹配不成功生成新元素
	* 用唯一 key 标识元素，因此建议不用下标做 key，因为下标也会因为移动而改变，而无法达到识别元素移动的目的

## API REFERENCE

### React

#### React.Component

##### [生命周期图谱](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

* **挂载**：当组件实例被创建并插入 DOM 中
	* **constructor()**
		* 如果不初始化 state 或不进行方法绑定，则不需要实现
	* static getDerivedStateFromProps()
	* **render()**
		* `render()` 应该为纯函数
		* class 组件中唯一必须实现的方法
	* **componentDidMount()**
		* 组件挂载后（插入 DOM 树中）立即调用
		* 依赖于 DOM 节点的初始化应该放在这里
* **更新**：props 或 state 发生变化时触发更新
	* static getDerivedStateFromProps() 
	* shouldComponentUpdate()
	* **render()**
	* getSnapshotBeforeUpdate()
	* **componentDidUpdate()**
		* 更新后会被立即调用
		* 首次渲染不会执行此方法
* **卸载**：组件从 DOM 中移除
	* **componentWillUnmount()**
		* 组件卸载及销毁之前直接调用
		* 执行必要的清理操作
* **错误处理**：抛出错误时
	* static getDerivedStateFromError() 
	* componentDidCatch()

## HOOK

## 测试

* [Jest](https://jestjs.io/)