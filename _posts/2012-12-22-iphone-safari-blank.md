---
layout: post
title: iPhone safari 刷新后出现空白页解决方案
---

这几天在做一个新网站，发现用iphone的safari浏览页面时，初次浏览页面都没有问题，但当手动点击刷新按钮，整个页面就变成白页。

先百度和google了一下，发现相关的结果不够解决问题。于是自己研究了一下，空白页出现有下面几个特征：

* 不刷新页面，点击链接页面都正常
* 刷新页面后出现空白页
* 修改页面代码，比如加一个空格，再刷新恢复正常

页面没有修改返回的是304，加一个空格后返回200，看来问题就在这里。Safari在手动刷新时没有正确处理304的结果（非手动刷新正常），chrome 和 IE 都没有此问题，看来应该是safari的bug。解决此问题，临时解决方案只能是让服务器不返回304，于是想到重置 Last-Modified 和 If-None-Match 。

我正在开发的网站用的是nodejs &amp; Express，针对我遇到的情况，简单的解决方案如下：

```
app.get('*', function(req, res, next) {
    var ua = req.header('user-agent');
    if (ua &amp;&amp; ua.match(/safari/i)) {
        req.headers['if-none-match'] = undefined;
    }
    next();
});
```
