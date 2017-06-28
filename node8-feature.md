# Node.js 8 中的重要新特性及优化

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[郑 farmer](http://www.zcfy.cc/@undefined)
> 链接：[http://www.zcfy.cc/article/3321](http://www.zcfy.cc/article/3321)
> 原文：[https://blog.risingstack.com/important-features-fixes-node-js-version-8/](https://blog.risingstack.com/important-features-fixes-node-js-version-8/)

随着 Node.js 8.0 版本的发布（5月30日下午12点发布），我们得到了最新的 LTS 版本，具有一系列新功能和性能改进。

本文我们将介绍 Node.js 8.0 版本中重要的功能和修复。

> 与以前的 Node.js 版本相比，8.0.0相当强大。虽然这其中有些还正在进行，很多正在商榷。但基本上是稳定和可测试的
> 
> - James M Snell（@jasnell）2017年5月30日

新版本的代号是 Carbon。Node 8 将从2017年10月起成为现行的 LTS 版本，并将保持到2019年12月31日。这也意味着 Node.js 6.x 版本将在 2018 年 4 月进入维护模式，并在 2019 年 4 月废弃。

你可以在这里获取 [8.0 release文档](https://nodejs.org/download/rc/v8.0.0-rc.2/)

## Async Hooks API 简介

Async Hooks（以前称为 AsyncWrap ）API允许您获取有关句柄对象生命周期的结构跟踪信息。

API 可以发送消息通知消费者关于 Node.js 中所有句柄对象的生命周期。它可以解决与[continuation-local-storage npm package](https://www.npmjs.com/package/continuation-local-storage)相同的问题，只不过现在可以在 Node 核心代码中实现。

**如果您曾经使用过 continuation-local-storage，那么现在有了 async hooks ，目前有一个替代方案--[cls-hooked](https://www.npmjs.com/package/cls-hooked) ** ，但目前尚未稳定，因此谨慎使用！

### Async Hooks API 如何在 Node.js 8 中工作的？

`createHooks`函数可以为每一个异步操作的生命周期注册钩子函数。

    const asyncHooks = require('async_hooks')

    asyncHooks.createHooks({  
      init,
      pre,
      post,
      destroy
    })

这些函数将根据处理程序的生命周期事件触发。

这里阅读更多[Async Hooks](https://github.com/nodejs/diagnostics/tree/master/tracing/AsyncWrap)的信息，或者[这里](https://github.com/thlorenz/node/pull/2/files)查看当前的进度。

## N-API 简介

N-API是用于编写原生插件的 API。它独立于底层的 JavaScript 运行环境，但作为 Node.js 本身的一部分进行维护。它的目标是使应用程序二进制接口（ABI）在不同 Node.js 版本之间保持稳定。

N-API的目的是将附加组件与底层JavaScript引擎的更改分开，以便原生组件可以在不同版本的 Node 环境中运行并且不需要重新编译。

查看更多[N-API](https://nodejs.org/dist/latest-v7.x/docs/api/n-api.html)的相关信息。

## Node 8 中 Buffer 安全性的改进

在 Node.js 8之前，用new Buffer(Number)来创建一个Buffer，并未将内存初始化为0。因此，新的缓冲区实例可能包含敏感信息，导致安全问题。

虽然这样可以使 Buffer 的创建更快，但对于大多数情况来看，这并不可行。因为从 Node.js 8 开始，使用`new Buffer(Number)` 或者`Buffer(Number)` 的将会自动将内存置为0.

## 将V8升级到5.8：为 TurboFan 和Ingnition 做准备

使用 Node.js 8，底层的V8 JavaScript引擎也会被更新。

它给 Node.js 用户带来的最大的变化就是可以在 V8 5.9中引入TurboFan 和 Ignition 。Ignition 是 V8 的解释器，而 TurboFan 是优化编译器。

> “ Ignition 和 TurboFan 管道已经开发了近3½年。它代表了 V8 团队通过测量现实 JavaScript 性能并仔细考虑了当前语言中的缺点而获得的最终结果。这为我们能够在未来几年内继续优化 JavaScript 奠定了基础。- Daniel Clifford 和 V8 团队

下面是 Node 8 版本之前的 V8 编译管道的示例图

![Node.js Version 8 - V8 old pipeline](http://p0.qhimg.com/t012094942ee846e841.png)

图片来源:Benedikt Meurer

这个管道的最大问题是新的语言功能必须在管道的不同部分实现，增加了大量额外的开发工作。

**这是简化的管道外观，没有 FullCode Generator 和 Crankshaft：**

![Node.js Version 8 - V8 new pipeline](http://p0.qhimg.com/t01564ae8d5d21ffd9f.png)

图片来源:Benedikt Meurer

这一新管道大大降低了V8团队的技术负担，并且实现了以前不可能实现的大量优化。

阅读更多关于 [TurboFan and Ignition](http://benediktmeurer.de/2016/11/25/v8-behind-the-scenes-november-edition/)和[TurboFan Inlining Heuristics](https://docs.google.com/document/d/1VoYBhpDhJC4VlqMXCKvae-8IGuheBGxy32EOgC2LnT8/edit)

## npm 升级到 5.0.0

新的 Node.js 8 版本还附带了npm 5 - 最新版本的npm CLI。

npm 新版本的亮点：

*   一种新的标准化锁定文件的功能，用于跨套件管理器兼容性（package-lock.json），一种新的格式和 shrinkwrap 语义化。

*   --save 不再需要，默认情况下将保存所有安装

*   node-gyp 现在支持 Windows（node-gyp.cmd）

*   现在将包括sha512和sha1校验。

## Node.js 8中的其他显着变化

#### Buffer

*   Buffer 方法现在接受 Uint8Array 作为输入

#### Child Process

*   优化参数和 kill 信号校验

*   Child Process 方法接受 Uint8Array 作为输入

#### Console

*   使用 console 发出的错误事件现在被限制

#### Domains

*   Native Promise 实例现在是 Domain 敏感的

#### File System

*   实用工具类`fs.SyncWriteStream`已被弃用

*   `fs.read（）`字符串接口已被删除

#### HTTP

*   传出的 Cookie 头连接成一个字符串

*   httpResponse.writeHeader（）方法已被弃用

#### Stream

*   Stream 现在支持`destroy（）`和`_destroy（）`API

#### TLS

*   `rejectUnauthorized`选项现在默认为`true`

#### URL

*   `WHATWG URL`实现现在是完全支持的 Node.js API

## 接下来是 Node.js 8

Node.js 8 为我们带来了非常有趣的改进，包括Async Hooks API，它目前较难掌握，文档还在不断改进状态。**我们将尽快开始播放新版本，并尽快让您对这些功能的更详细的说明。**