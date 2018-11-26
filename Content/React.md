#  React学习

参考链接：http://www.runoob.com/react/react-tutorial.html

## 简介

 * React 是一个用于构建用户界面的 JAVASCRIPT 库。

 * React主要用于构建UI，很多人认为 React 是 MVC 中的 V（视图）。

 * React 起源于 Facebook 的内部项目，用来架设 Instagram 的网站，并于 2013 年 5 月开源。

 * React 拥有较高的性能，代码逻辑非常简单，越来越多的人已开始关注和使用它。


## 特点

 * 声明式设计 − React采用声明范式，可以轻松描述应用。

 * 高效 − React通过对DOM的模拟，最大限度地减少与DOM的交互。

 * 灵活 − React可以与已知的库或框架很好地配合。

 * JSX − JSX 是 JavaScript 语法的扩展。React 开发不一定使用 JSX ，但我们建议使用它。

 * 组件 − 通过 React 构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。

 * 单向响应的数据流 − React 实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。


 ## JSX优点

 * JSX 执行更快，因为它在编译为 JavaScript 代码后进行了优化。

 * 它是类型安全的，在编译过程中就能发现错误。

 * 使用 JSX 编写模板更加简单快速。


## React渲染HTML标签

```javascript

var myDivElement = <div className="foo" />;
ReactDOM.render(myDivElement, document.getElementById('example'));

```

## React渲染React组件

```javascript

var MyComponent = React.createClass({/*...*/});
var myElement = <MyComponent someProperty={true} />;
ReactDOM.render(myElement, document.getElementById('example'));

```



## React组件

### 使用函数定义组件

```javascript

function HelloMessage(props) {
    return <h1>Hello {props.name}!</h1>;
}

const element = <HelloMessage name="Runoob"/>;

ReactDOM.render(
    element,
    document.getElementById('example')
);

```


### 使用ES6定义组件(ECMAScript 6)

```javascript

class HelloMessage extends React.Component {
  render() {
    return <h1>Hello {this.props.name} </h1>;
  }
}

const element = <HelloMessage name="Runoob"/>;

ReactDOM.render(
    element,
    document.getElementById('example')
);

```


## React State

生命周期钩子：
constructor：构造方法
componentDidMount：在组件输出到DOM后会执行钩子（挂载）
componentWillUnmount：卸载


```JavaScript

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>现在是 {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('example')
);

```

## React 事件处理
## React 条件渲染
## React 列表 & Keys
## React 组件 API
## React 组件生命周期
## React AJAX
## React 表单与事件
## React Refs

