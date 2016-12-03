---
layout: post
title: ES6 中 import/export 和 require/exports 对比 
author: 阎曦
---

Javascript 最早用于浏览器，处理的逻辑比较简单，没有语言层面的模块化处理方法。 随着 JS 应用的逐渐复杂，人们提出多重模块化规范，其中最著名的当属 CommonJS 和 AMD 了。随着 nodejs 的推出和流行，CommonJS 已经被大家所熟知。最早实践前端模块化的框架 requirejs 使用的则是 AMD 规范。相对 AMD 规范来说，commonjs 在定义和使用上都要简单不少。所以后来推出的 browserify 就是用 commonjs 规范来组织前端 JS 代码。而这两年流行的 webpack 则兼容了以上两个规范。先来回顾一下这两个规范：

### AMD (Asynchromous Module Definition)

```javascript
// 定义模块
define('sum', function() {
    return function(a, b) {a + b}
});

// 使用模块
define(['sum'], function(sum) {});
```

### CommonJS

```javascript
// 定义模块 math.js
exports.sum = function(a, b) {a + b}

// 使用模块
var sum = require('math').sum
```

随着 es6/es2015 规范的推出，JS 模块化终于有了语言层面上的模块化支持。在开发实践中，对于 `export/import` 和 CommonJS 中的 `exports/require` 的差异，我和同事都有过迷惑，今天抽时间彻底的整理一下。

### ES6/es2015

按名字导出和导入

```javascript
// 定义模块 math.js
export function sum(a, b) {a + b}

// 使用模块
import {sum} from 'math'
```

全部导出和导入，注意关键字 `default`

```javascript
// 定义模块 sum.js
export default function sum(a, b) {a + b}

// 使用模块
import sum from 'sum'
```

### es6:exports 和 commonjs:export 的关系

**按名字导出**

```javascript
// es6 使用 export 语法
export function sum(a, b) {
  return a + b;
}

// 等价于 commonjs 写法如下 (Babel 6.18.2 生成)
Object.defineProperty(exports, "__esModule", {
  value: true
});
exports.sum = sum;
function sum(a, b) {
  return a + b;
}
```

**导出整体**

```javascript
// es6 使用 export 语法
export default function sum(a, b) {
  return a + b;
}

// 等价于 commonjs 写法如下 (Babel 6.18.2 生成)
Object.defineProperty(exports, "__esModule", {
  value: true
});
exports.default = sum;
function sum(a, b) {
  return a + b;
}
```

可以看出 es6 中 `export default sum` 相当于 CommonJS 中 `exports.default = sum`

### es6:import 和 commonjs:require 的关系

**按名字导入**

```javascript
// es6 使用 import 语法
import {sum} from 'math';
console.log(sum)
sum(1, 2);

// 等价于 commonjs 写法如下  (Babel 6.18.2 生成)
var _math = require('math');
console.log(_math.sum);
(0, _math.sum)(1, 2);   // （0, _math.sum) 相当于一个赋值语句，改变了 sum 函数 this 指向
```

**整体导入**

```javascript
// es6 使用 import 语法
import sum from 'sum';
console.log(sum)
sum(1, 2);

// 等价于 commonjs 写法如下  (Babel 6.18.2 生成)
var _sum = require('sum');
var _sum2 = _interopRequireDefault(_sum);
function _interopRequireDefault(obj) {
    return obj && obj.__esModule ? obj : { default: obj }; 
}
console.log(_sum2.default);
(0, _sum2.default)(1, 2);
```

**导入所有**

```javascript
// es6 使用 import 语法
import * as math from 'math';
math.sum(1, 2);

// 等价于 commonjs 写法如下  (Babel 6.18.2 生成)
var _math = require('math');
var math = _interopRequireWildcard(_math);
function _interopRequireWildcard(obj) { 
  if (obj && obj.__esModule) { 
    return obj; 
  } 
  else { 
    var newObj = {}; 
    if (obj != null) { 
      for (var key in obj) { 
        if (Object.prototype.hasOwnProperty.call(obj, key)) 
          newObj[key] = obj[key]; 
      } 
    } 
    newObj.default = obj; 
    return newObj; 
  } 
}

math.sum(1, 2);
```

结论：

* `export function sum() {}` 相当于 `exports.sum = function() {}`
* `export default sum` 相当于 `exports.default = sum`
* `import {sum} from 'math'` 相当于 `var sum = require('math').sum`
* `import sum from 'sum'` 相当于 `var sum = require('math').default`
* `import * as math from 'math'` 相当于 `var math = require('math')`
