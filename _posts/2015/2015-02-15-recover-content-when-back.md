---
layout: post
title: 页面返回保留上一页信息解决方案
category: 技术
tags: [Web]
---

当用户在网页中点击链接进入下一页，再点击返回按钮，希望重新展示上一页的内容，并且停留在上次离开的地方。这对于传统的服务器端网页技术（利用 php,jsp 生成好 html 然后整体返回给浏览器）没有任何问题。随着前端技术的不断升级换代，dom 越来越多是在前端用 javascript 生成，这时返回按钮通常会导致上一个页面的 dom 重新生成一遍，之前的数据和页面内容都会丢失。

页面 URL 发生变化分两类：

<!-- more -->

1. 不发起新 http 请求（应用内跳转）：只改变 URL 的 hash 部分或者用 html5 的 history.pushState，此时 URL 虽然变化，但是不会重新发起新的 http 请求。
1. 请求了一个新的页面（应用外跳转）：在网页中点击链接，或者 location.href 改变都会发起新的 http 请求。

针对上面两类跳转，对应的返回也分两类，解决方法也略有不同。但基本思路是一样的，就是在发生上述两类跳转时，将当前页面的 state 信息保存到 sessionStorage。当页面返回时，直接从 sesseionStorage 读出 state 信息生成页面。

接下来的问题是如果判断用户是从返回按钮回来的，如果用户不是通过返回按钮来到页面，则先清除相关的 sessionStorage，保证页面数据会重新获取。通过在 html 中设置一个隐藏的 form，里面is_from_back默认位0，当页面跳转时设定其值为1。就可以通过 is_from_back 的值来判断是否来自于后退按钮了。

关键代码实例：(使用 angular 和 ui-route)

html

```html
<form name="ignore_me">
    <input type="hidden" id="is_from_back" value="0" />
</form>
```

angular stateCache service

```javascript
servicesModule.factory('stateCache', function ($state) {
    function initApp() {
        // 应用内返回: 不会调用本方法
        // 应用外返回: 不 clear 数据
        if (document.getElementById('is_from_back').value === '0') {
            // 不是来自返回按钮
            storage.clear();
        }
    }

    function initCtrl($scope, keys, fn) {
        var stateName = $state.$current.name;
        var state = get(stateName);

        if (state && state.keys === keys.join(',')) {
            keys.forEach(function (key) {
                $scope[key] = state.data[key];
            });
        } else {
            fn();
        }

        var onLocationChange = function () {
            var data = {};
            keys.forEach(function (key) {
                data[key] = $scope[key];
            });

            var state = {
                keys: keys.join(','),
                data: data
            };
            set(stateName, state);
            document.getElementById('is_from_back').value = '1';
        };
        window.onbeforeunload = onLocationChange;
        $scope.$on('$stateChangeStart', onLocationChange);

    }

    return {
        initApp: initApp,
        initCtrl: initCtrl
    };
});

```

use the service in angular

```javascript
angular.run(function() {
    stateCache.initApp()
});

function Controller($scope, stateCache) {
    stateCache.initCtrl($scope, keys, initFn);
}
```

