---
title: React Native Context 用法
author: 暮野夕风
top: false
cover: false
date: 2020-05-20 14:32:56
img:
coverImg:
summary:
categories: 前端
tags:
  - React
  - React Native
---

> 参考资料：
>
> React Native 官网文章：[Context](https://reactjs.org/docs/context.html)
> React Native 官网文章：[Context(简体中文文档)](https://zh-hans.reactjs.org/docs/context.html)

_本文示例代码使用 TypeScript 语言。_

## 使用场景

在整个视图树中传递数据，不必每个层级都通过 Props 传。

## 使用步骤

### Context 声明

Context 声明的数据结构最好是所在视图树中通用的。

在 `Contexts.ts` 文件中声明 Context 的数据结构：

```typescript
import Rect from 'react';

export interface PageContextValue {
  theme?: string;
}

/**
 * 页面级上下文。
 */
export const PageContext = Rect.createContext<PageContextValue>({});
```

### 提供 Context 数据源

在页面根视图外包一层 `PageContext.Provider` 节点，通过 `value` 属性提供数据。

```typescript
import { PageContext, PageContextValue } from './Contexts'

const CONTEXT: PageContextValue = {
  theme: 'dark'
}

export class HomePage extends Component<any, any> {

  render() {
    return (
      <PageContext.Provider value={CONTEXT}>
        <Toolbar />
      </PageContext.Provider>
    );
  }
}
```

注意，避免使用以下方式，这样会导致每次执行 `render` 函数都会创建一个新的 `PageContextValue` 对象，造成不必要的刷新：

```typescript
import { PageContext, PageContextValue } from './Contexts'

export class HomePage extends Component<any, any> {

  render() {
    return (
      // 在 render 函数中创建 PageContextValue 可能导致不必要的刷新。
      <PageContext.Provider value={{ theme: 'dark' }}>
        <Toolbar />
      </PageContext.Provider>
    );
  }
}
```

如果 Context 是动态改变的，最好通过 State 来刷新：

```typescript
import { PageContext, PageContextValue } from './Contexts'

interface State {
  context: PageContextValue
}

export class HomePage extends Component<any, State> {

  constructor(props: any, context: any) {
    super(props, context)
    this.state = {
      context: { theme: 'dark' }
    }
  }

  render() {
    return (
      <PageContext.Provider value={this.state.context}>
        <Toolbar />
      </PageContext.Provider>
    );
  }
}
```

### 获取 Context 传递的数据

通过 `this.context` 方式获取数据，可以在任何生命周期中获取。

```typescript
class MyClass extends React.Component {
  // 将 Context 赋值给 contextType 变量
  static contextType = PageContext;
  render() {
    let value = this.context;
    /* 基于这个值进行渲染工作 */
    return value.theme === 'dark' ? (
      <View>{/* dark主题的组件 */}</View>
    ) : (
      <View>{/* 非dark主题的组件 */}</View>
    )
  }
}
```

通过 `Context.Consumer` 方式获取数据

```typescript
class MyClass extends React.Component {
  // 将 Context 赋值给 contextType 变量
  static contextType = PageContext;
  render() {

  <PageContext.Consumer>
    {value => value.theme === 'dark' ? (
      <View>{/* dark主题的组件 */}</View>
    ) : (
      <View>{/* 非dark主题的组件 */}</View>
    )}
  </PageContext.Consumer>
  }
}
```

以上是 Context 的使用步骤，更多用法请参考官网。
