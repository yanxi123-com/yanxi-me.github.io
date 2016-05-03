---
layout: post
title: 尝试移动应用开发(React Native)
---

从第一次尝试网站开发到现在，马上就15个年头了。还记得第一次上网是在新世纪的第一年，当时互联网远没有现在火。
但当我走进机房打开第一个网页就感觉太神奇了，全世界的人都可以通过一个网址找到你，从那时起就开始了十几年的互联网征程。

十几年来做了无数的网站，PC端和移动端，前端和后端都有涉及，但唯独没有亲自做过 APP，错过了 APP 最火的几年。
比起十几年前对于互联网的嗅觉，对于移动互联网的嗅觉可谓迟钝。
但在目前的移动互联网时代，网站正在接受手机 App 的挑战，人们花在网站上的时间越来越少，甚至很多人根本不知道网址的概念。

---

### 本地应用 VS 混合应用

最近公司在开发一个新的移动应用，于是参与的比较多，开始了移动应用的学习和尝试。App 开发分为纯 Native 本地开发和 Hybrid 混合开发。
性能上 Native 胜出，但需要做 ios 和 android 两个版本，开发效率低不少。Hybrid App 以 web view 为主，通过 Bridge 连接来用 Javascript
调用本地 API。由于对 web 比较熟悉，所以决定先采用 Hybrid 方案来做一个 demo。经过对比，最后选定了 IONIC，它底层集成 Cordova
框架来和手机通信，UI 层通过 Angularjs 实现了大多数本地 UI 组件的效果。完成 Demo 后，发现在 iPhone 上运行基本能够接受，但相对本地应用还是有较大差距。
在安卓手机上卡顿比较严重，作为一个产品基本很难接受。于是在正式开发中，依旧选择两拨人分别开发 iphone 和安卓应用。

---

#### 尝试 React Native (以下简称 RN)

基用 IONIC 的混合方案被否定后，正好赶上 facebook 开源 React Native. 于是花了些时间看了看 ObjectC 和 RN，并做了些对比。
其实之前对 React 有一些了解，但实在对 JSX 的写法（将 xml 置于 js中）不感冒，感觉是对 Javascript 的玷污。
这次多花了一点时间详细看了看 React，觉得其模块化思想其实相当好。RN 是对 App 写法上的一次革命，将群众基础广泛的 Javascript
语言带到 App 开发中。开源一个月左右，github 上已经有了大量的相关模块，可以大大加速 App 开发时间。Facebook 计划在10月份开源 Android
版本，对于 RN 的 app 项目，到时移植 React iOS 到 React Android 应该会比较容易。

目前正在一边学习 RN, 一边在业余时间用他开发一个小应用，相信用不了多久可以开发完毕和大家见面了。

---

### 尝试 Flux

Flux 是 facebook 推出的一个应用程序架构，用来解决前端数据和 UI 的绑定和更新。今天在我做的 RN 小应用上尝试了一下，感觉还不错，
在这个项目会继续使用 Flux 来解决数据流问题。相对于 Angular 的数据双向绑定，Flux 的单向流动在性能上会有不少提升。
下面是 Flux 程序的数据流图:

{% include image.html img="https://cloud.githubusercontent.com/assets/4622233/7428533/b362c9ac-f024-11e4-862b-16ed78134eb7.png" caption="Flux 程序的数据流" %}
