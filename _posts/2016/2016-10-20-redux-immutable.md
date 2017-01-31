---
layout: post
title: Redux + immutable.js 学习实践
category: 技术
tags: [Javascript]
author: 阎曦
---

用 react + redux 有一段时间了。之前都是自己写的 reducer，因为每次修改 state 时需要返回新的引用，所以需要特别注意，用起来总觉得不够方便。这两天学习了一下 Immutable.js , 准备把它引入到项目中和 redux 配合使用。先来设计 action 数据存取方法，最主要的几个方法如下：

    // 根据 keyPath 设置 value，可以更新任意深位置的数据
    // value 是简单值类型或者 Immutable 类型，也可以是函数 (oldValue) => newValue
    dispatch(action.set(...keyPath, value)) 
    
    // 删除 keyPath 对应的 value
    dispatch(action.delete(...keyPath))

    // 根据 keyPath 获取 value
    state.getIn(keyPath)

<!-- more -->

#### Object 类型数据存取示例:

```
dispatch(action.set('page', 'form', 'name', 'yanxi'));
dispatch(action.set('page', 'form', 'city', 'beijing'));

state.getIn(['page', 'form']);
// 结果: Map {name: 'yanxi', city: 'beijing'}

state.getIn(['page', 'form', 'name']);
state.getIn(['page', 'form']).get('name');
// 结果: yanxi

state.get('page').toJS()
// 结果: {form: {name: 'yanxi', city: 'beijing'}}

state.get('page').toObject()
// 结果: {form: Map {name: 'yanxi', city: 'beijing'}}

dispatch(action.delete('page', 'form', 'city'));

state.get('page').toObject()
// 结果: {form: Map {name: 'yanxi'}}
```

#### 数组类型数据存取示例:

```
dispatch(action.set('page', 'persons', Immutable.fromJS([{name:'yanxi'}]))

state.getIn(['page', 'persons'])
// 结果: List [ Map { "name": "yanxi" } ]

state.getIn(['page', 'persons', 0, 'name'])
// 结果: yanxi

dispatch(action.set('page', 'persons', s => s.push(Map({name: 'zhangsan'})))

state.getIn(['page', 'persons', 1])
// 结果: Map { "name": "zhangsan" }
```

这么设计有两大好处，action 操作会非常方便：

1. 可以直接根据keyPath更新任意深的数据
1. 更新的时候如果最后一个参数指是函数，函数签名中第一个参数就是其更新前的值

**注意事项**：设置的 value 应该是一个值类型，即 Immutable 类型或者其他简单类型，而不能是一个 JS 对象或者数组。

### 主要实现部分如下:

actions

```
function set(...keyPath) {
  let value = keyPath.pop();
  return {
    type: 'MAP_SET',
    keyPath,
    value
  }
}
```

reducer

```
function (state = Map({}), action = {}) {
  if (action.type === 'MAP_SET') {
    if (typeof action.value === 'function') {
      return state.updateIn(action.keyPath, action.value);
    } else {
      return state.setIn(action.keyPath, action.value);
    }
  }

  return state;
}
```
