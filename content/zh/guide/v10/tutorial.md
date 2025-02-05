---
name: 快速教程
description: '编写您的第一个 Preact 应用'
---

# 教程

本指南将引导您构建一个简单的“滴答时钟”组件。如果您是 Virtual DOM 的新手，建议尝试[完整 Preact 教程](/tutorial)。

> :information_desk_person: 本指南假设您已完成[开始上手](/guide/v10/getting-started)并已成功配置开发环境。若尚未完成，请从[Vite 配置](/guide/v10/getting-started#create-a-vite-powered-preact-app)开始。

---

<div><toc></toc></div>

---

## Hello World

在任意 Preact 代码库中，您总会看到两个核心函数：`h()` 和 `render()`。`h()` 函数用于将 JSX 转换为 Preact 能理解的结构，但也可直接使用而无需 JSX：

```jsx
// 使用 JSX
const App = <h1>Hello World!</h1>;

// ...无 JSX 的等效写法
const App = h('h1', null, 'Hello World');
```

仅此不会产生任何效果，我们需要将 Hello World 应用注入 DOM。为此我们使用 `render()` 函数。

```jsx
// --repl
import { render } from 'preact';

const App = <h1>Hello World!</h1>;

// 将应用注入 DOM
render(App, document.getElementById('app'));
```

恭喜！您已构建了第一个 Preact 应用！

## 交互式 Hello World

渲染文本是起点，但我们需要让应用更具交互性。目标是实现用户输入名称并提交表单时显示该名称。[组件](/guide/v10/components)在此发挥作用。

将现有 App 转换为[组件](/guide/v10/components)：

```jsx
// --repl
import { h, render, Component } from 'preact';

class App extends Component {
  render() {
    return <h1>你好，世界！</h1>;
  }
}

render(<App />, document.getElementById("app"));
```

我们添加了 `<form>` 表单实现交互：

```jsx
// --repl
import { h, render, Component } from 'preact';

class App extends Component {
  render() {
    return (
      <div>
        <h1>你好，世界！</h1>
        <form>
          <input type="text" />
          <button type="submit">更新</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.getElementById("app"));
```

通过 `state` 状态管理实现数据绑定：

```jsx
// --repl
import { h, render, Component } from 'preact';

class App extends Component {
  state = { value: '' }

  onInput = ev => {
    this.setState({ value: ev.currentTarget.value });
  }

  render() {
    return (
      <div>
        <h1>你好，世界！</h1>
        <form>
          <input type="text" value={this.state.value} onInput={this.onInput} />
          <button type="submit">更新</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.getElementById("app"));
```

添加表单提交处理完成交互闭环：

```jsx
// --repl
import { h, render, Component } from 'preact';

class App extends Component {
  state = { value: '', name: '世界' }

  onInput = ev => {
    this.setState({ value: ev.currentTarget.value });
  }

  onSubmit = ev => {
    ev.preventDefault();
    this.setState({ name: this.state.value });
  }

  render() {
    return (
      <div>
        <h1>你好，{this.state.name}！</h1>
        <form onSubmit={this.onSubmit}>
          <input type="text" value={this.state.value} onInput={this.onInput} />
          <button type="submit">更新</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.getElementById("app"));
```

大功告成！现在可以输入自定义名称并实时更新显示。

## 时钟组件

实践构建自动更新的时钟组件：

```jsx
// --repl
import { h, render, Component } from 'preact';

class Clock extends Component {
  render() {
    let time = new Date().toLocaleTimeString();
    return <span>{time}</span>;
  }
}

render(<Clock />, document.getElementById("app"));
```

添加定时器实现动态更新：

```jsx
// --repl
import { h, render, Component } from 'preact';

class Clock extends Component {
  state = { time: Date.now() };

  componentDidMount() {
    this.timer = setInterval(() => {
      this.setState({ time: Date.now() });
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timer);
  }

  render() {
    let time = new Date(this.state.time).toLocaleTimeString();
    return <span>{time}</span>;
  }
}

render(<Clock />, document.getElementById("app"));
```

[动态时钟](http://jsfiddle.net/developit/u9m5x0L7/embedded/result,js/)就此完成！通过组件生命周期方法实现定时器管理，确保资源及时释放。
