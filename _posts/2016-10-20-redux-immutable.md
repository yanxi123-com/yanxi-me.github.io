---
layout: post
title: Redux + immutable.js 学习实践
author: 阎曦
---

用 react + redux 有一段时间了, 之前都是自己写的 reducer, 因为每次修改 state 时需要返回新的引用, 所以需要特别注意, 用起来总觉得不够方便. 这两天学习了一下 Immutable.js , 准备把它引入到项目中. 数据存取方法设计如下:

    // 根据 keyPath 设置 value
    dispatch(action.set(...keyPath, value)) 
    
    // 删除 keyPath 对应的 value
    dispatch(action.delete(...keyPath))

    // 根据 keyPath 获取 value
    state.get(...keyPath)

#### Object 类型数据存取示例:

```
dispatch(action.set('page', 'form', 'name', 'yanxi'));
dispatch(action.set('page', 'form', 'city', 'beijing'));

state.get('page', 'form');
// 结果: Map {name: 'yanxi', city: 'beijing'}

state.get('page', 'form', 'name');
state.get('page', 'form').get('name');
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

var persons = state.get('page', 'persons')
// 结果: List [ Map { "name": "yanxi" } ]

persons = persons.push(Map({name: 'zhangsan'}))
// 结果: List [ Map { "name": "yanxi" }, Map { "name": "zhangsan" }]

dispatch(action.set('page', 'persons', persons))

state.get('page', 'persons', 1, 'name')
// 结果: zhangsan
```

