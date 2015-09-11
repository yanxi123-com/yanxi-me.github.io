---
layout: post
title: 开通新的个人网站（yanxi.me）
---

今天注册了新域名 [yanxi.me](http://yanxi.me) ，顺便用 [Github Pages](https://pages.github.com/) 和
[Jekyll](http://jekyllrb.com) 搭了这个网站。从此可以直接通过编辑器更新博客了，省略了在 web 页面点来点去的麻烦。

简单记录下开通过程：

1. 在 Github 创建自己网站项目，项目名为 [yanxi-com.github.io](https://github.com/yanxi-com/yanxi-com.github.io) <br/>
注：项目名必须为 `your-username.github.io`
1. 注册域名，选择阿里云旗下的万网 [www.net.cn](http://www.net.cn)，增加 CNAME 记录到 `yanxi-com.github.io`
1. 使用 Jekyll 的 [Lanyon 模板](https://github.com/poole/lanyon)，直接克隆即可
1. 修改 config 和 CNAME, 并 push 到 Github 即可， [改动如下](https://github.com/yanxi-com/yanxi-com.github.io/commit/c97d9199dd9d2120de2aa70d22f97d7f89ee766a)
1. 搞定，可以愉快的访问了：[yanxi.me](http://yanxi.me)
