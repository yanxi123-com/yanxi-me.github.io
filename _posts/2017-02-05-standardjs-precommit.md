---
layout: post
title: standard JS 代码规范和提交代码时检查
author: 阎曦
category: 技术
tags: [Javascript]
---

公司的 Javascript 项目最开始有一个代码规范，也用 jsHint 写了检查脚本。但是随着团队人员的逐渐增加和项目的时间紧迫，有一段时间没有特别强调要去做代码规范的检查。导致后来每次运行 jsHint 脚本都是错误无数，于是更没有人做相关的检查了。由于缺乏代码规范，不同的人有不同的偏好，代码可读性逐渐降低。也出现过几次由于不小心带来的线上 bug，这些完全是可以通过代码检查避免的。于是我决定是时候对代码规范和检查做强制性的要求了。

之前的 JS 代码规范比较简单，也没有涉及到 jsx 部分的规范。不同的人对于 JS 的写法有不同的习惯和看法，其实本没有对错之分。但作为一个开发团队，应该采用相同的规范，不同的人写的代码应该看起来是出自同一个人之手。这样能显著提高代码的可读性，有利于团队合作，避免出现不必要的错误。而且规范一旦制定，一定要有可行的办法强制执行，不然规则再完美再有效，也没有实际的意义。关于代码规范，非常认同下面的几句话：


> "对风格的挑刺毫无意义可言。它们必须是指导原则，且你必须遵循。"
>
> --Rebecca Murphey


> "成为一个优秀的成功项目管理者的一个条件是，明白按自己的偏好风格写代码是非常不好的做法。如果成千上万的人都在使用你的代码，那么请尽可能通俗易懂地写出你的代码，而非在规范之下自作聪明地使用自己偏好的风格。"
>
> --Idan Gazit

<!-- more -->

在网上比较了 [airbnb](https://github.com/airbnb/javascript)，[Idiomatic.js](https://github.com/rwaldron/idiomatic.js/tree/master/translations/zh_CN)，[jQuery](https://contribute.jquery.org/style-guide/js/)，[Standard](https://github.com/feross/standard)，[等更多 JS 代码规范后](http://noeticforce.com/best-javascript-style-guide-for-maintainable-code) ，确定把 [standardJS](https://github.com/feross/standard) 作为团队的 JS 代码标准，主要因为：

1. 简单，无需配置文件
1. 符合规则的代码相对比较简洁（比如语句不需要分号）
1. 支持自动修改代码样式（通过 `--fix` 参数）

主要规则如下:

- 用2个空格缩进
- 字符串用单引号，除非字符串里有单引号
- 不能有未使用的变量，这是很多bug的来源
- 语句结束不需要分号，可以少些不少字符 [It's][1] [fine.][2] [Really!][3]
- 不要以 `(`, `[`, or `` ` `` 开头，这是仅有的缺少分号可能带来问题的地方，standard js 会帮你自动检查
- 关键字后面加空格 `if (condition) { ... }`
- 函数名后面加空格 `function name (arg) { ... }`
- 永远使用 `===` 代替 `==`，但 `obj == null` 是允许的，用来检查 `null || undefined`.
- 永远处理错误 nodejs 类型的错误 `function(err) {}`
- 浏览器全局变量永远加上 `window`，`document` 和 `navigator` 除外
- 查看[更多规则][5]

[1]: http://blog.izs.me/post/2353458699/an-open-letter-to-javascript-leaders-regarding
[2]: http://inimino.org/~inimino/blog/javascript_semicolons
[3]: https://www.youtube.com/watch?v=gsfbh17Ax9I
[4]: https://github.com/feross/standard/blob/master/RULES.md#semicolons
[5]: https://github.com/feross/standard/blob/master/RULES.md

## 强制在提交代码时检查代码是否符合规范

正如上面提到的，执行规范是比规范本身更加重要，再好的规范但没有很好的执行也是没有意义的。只靠强调是很难确保所有人都能完全按照规范，必须有自动化的检查工具才行。利用 Git 提供的 pre-commit hook，可以很好的完成提交时检查的工作，下面是 pre-commit 脚本：

```shell
#!/bin/sh
# Ensure all javascript files staged for commit pass standard code style
git diff --name-only --cached --relative | grep "\.jsx\?$" | xargs standard --verbose | snazzy
if [ $? -ne 0 ]; then exit 1; fi
```

注意：

 - 文件位于`.git/hooks/pre-commit`，需要可执行
 - `standard` 和 `snazzy` 需要通过 npm 全局安装 `npm install -g standard snazzy`

另：pre-commit 文件可以通过执行[这段代码](https://gist.github.com/yanxi-me/4ac3cc98869bcc5ce3effe9d9005e84e)自动创建。

## 使用 overcommit

除了通过上面的方法直接修改 pre-commit 文件，还可以使用 [overcommit](https://github.com/brigade/overcommit) 工具来管理 Git hoops，步骤大致如下：

- 安装 rvm: `\curl -sSL https://get.rvm.io | bash -s stable`
- 下载 ruby 2.4.0: `rvm install 2.4.0`
- 更新 gem 镜像：[http://gems.ruby-china.org/](http://gems.ruby-china.org/)
- 安装 overcommit: `gem install overcommit`
- 在 git 项目中初始化 overcommit

  ```
  overcommit --install
  overcommit --sign
  ```

- 编辑 `.overcommit.yml` 文件：

  ```yml
  PreCommit:
    Standard:
      enabled: true
      include:
        - '**/*.js'
        - '**/*.jsx'
  ```
