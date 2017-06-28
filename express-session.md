# Express中的 Session 是怎样工作的？

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[郑 farmer](http://www.zcfy.cc/@undefined)
> 链接：[http://www.zcfy.cc/article/3342](http://www.zcfy.cc/article/3342)
> 原文：[http://nodewebapps.com/2017/06/18/how-do-nodejs-sessions-work/](http://nodewebapps.com/2017/06/18/how-do-nodejs-sessions-work/)

每个 Web 应用都会维护 Session，作为开发人员，我们需要知道怎么合理的使用它们。

本文将会介绍以下几个方面：

*   什么是一个会话（Session）？

*   Session 是怎样存储数据的？

*   如何选择存储 Session 数据的方式？

*   使用 Session 时需要避免哪些安全隐患？

想获取更多代码示例，可以参考 [session npm module](https://github.com/expressjs/session)，这可能是最通用的 Session 库。

## 什么是会话（Session）

**Session 是可以跨请求存储数据的地方。每个访问你网站的用户都会有一个唯一的Session。当他们访问你网站的时候，你可以从各自的 Session 中获取用户数据**

Session 是 Web 应用的必要组成部分，因为它们允许应用程序存储状态。根据用户对A页面的操作，我们可以显示不同的页面B。如果没有Session，应用程序将是无状态的，应用会变得不完整。

下面你可以在 Express 中设置简单会话的方法：

    import express from 'express';import session from 'express-session';var app = express();app.use(session());

Session 可以有多种方式存储信息。最常用的方式有以下几点：

*   应用程序内存

*   cookie

*   内存缓存

*   数据库

## 将 Session 存在应用程序内存中

存储 Session 数据的一种方法是在应用程序内存中。这种是很常用也很简单的一种方式，但不经常用在正式环境中。

将 Session 数据存储在应用程序内存中意味着数据只会在应用程序运行时保留，如果你的应用崩溃或者停止，所有的 Session 数据都会被清空。

而且将 Session 数据存储在内存中也很容易会导致内存泄漏。随着您的应用程序运行，使用越来越多的内存，直到您的应用程序内存不足。

如果在开发过程中，将会话存储在应用程序内存中通常很有用。除此之外，更好的存储 Session 的方式应该用以下几种。

## 将 Session 数据存储在Cookie中

**cookie 通常很小，在 Web 服务器和浏览器之间传递。它允许服务器存储与特定用户相关的信息。**

Cookie 的一个常见用途就是存储 Session 数据。工作方式如下：

1.  服务器发送一个cookie，发送到浏览器并存储一段时间（称为到期时间）。

2.  当用户后续向 Web 服务器发出请求时，该cookie将与请求一起发送，并且服务器可以读取其中的信息。

3.  如果需要，服务器可以操作cookie，然后将其发送回浏览器。

在 cookie 到期之前，每次你提出请求时，你的浏览器都会将 Cookie 发送回服务器。

express-session模块将为你提供一个很好的API来处理会话（让你获取和设置数据到会话），它将使用一个cookie保存和检索这些数据。

![](http://p0.qhimg.com/t0125da0777cfdc9e84.png)

[Express-session](https://github.com/expressjs/session)也提供了一些关于 cookie 的安全策略。我们不会在这里讲安全细节，但我建议您阅读一下这些文档。以确保您的应用程序 cookie 中的信息不被暴露，这是非常重要的。

下面介绍一下 Cookie 的一些问题：

1.  他们只能存储大约 4KB 的小数据。

2.  它们在每个请求中发送，如果您将一大堆数据存储在cookie中，则会增加请求的大小，这将降低您网站的性能。

3.  如果攻击者知道您的Cookie是如何加密的（您的秘密密钥），那么您的 cookie 将被盗用。攻击者就可以读取存储在 cookies 中的数据，这些数据可能是敏感的用户数据。

* * *

## 将 Session 数据存储在内存缓存中

内存缓存是可以存储小块键值数据的地方。比较常用的用于存储会话信息比如 Redis 和 Memcached。

将 Session 数据存储在内存缓存中时，服务器仍然需要使用cookie，但是 cookie 只包含一个唯一的SessionId这个SessionId 将被服务器用来对数据库进行查找。

![](http://p0.qhimg.com/t01aca21053b047ed70.png)

**使用内存缓存时，您的cookie只包含一个 SessionID。这消除了在Cookie 中暴露私人用户信息的风险。**

使用内存缓存来存储 Session 还有很多其他好处。

1.  它们通常是基于键值的，所以执行查找非常快。

2.  它们通常与应用程序服务器分离。这样降低耦合性。

3.  一个内存缓存可以为许多应用程序提供服务。

4.  它们可以通过删除旧会话数据来自动管理内存。

同时，他们也有一些缺点：

1.  他们是通过另一台服务器来管理。

2.  对于小型应用，它们可能会过度使用。通常数据库存储（我们将在下面介绍），或 cookies 也将完成此工作。

3.  没有办法来重置缓存，除非删除其中存储的所有会话。

    以下是通过connect-memcached模块设置内存缓存（如Memcached with express-session）的示例。var express= require('express');var session= require('express-session');var cookieParser = require('cookie-parser');var app= express();var MemcachedStore = require('connect-memcached')(session); app.use(cookieParser());app.use(session({secret: 'some-private-key',key : 'test',proxy : 'true',store : new MemcachedStore({hosts: ['127.0.0.1:11211'], //this should be where your Memcached server is runningsecret: 'memcached-secret-key' // Optionally use transparent encryption for memcache session data })}));

## 将 Session 数据存储在数据库中

最后，我们来讨论将 Session 数据存储在传统数据库中，如MySQL或PostgreSQL。在大多数情况下，这可以以非常相似的方式将会话数据存储在内存存储中。

会话 cookie 仍然要包含一个sessionId。在这种情况下，它将映射到数据库上的 Session 表的主键。

一般来说，我不建议将会话数据存储在数据库中，唯一的原因相比Memcached或Redis太费力了。如果您有理由说为什么数据库更好地工作，请在评论中通知我。

从数据库检索数据比内存缓存慢，因为数据存储在磁盘上，而不是内存。当你用数据库存储 Session 的时候，你需要更多的访问数据库。

此外，您必须自己完全管理旧会话。如果你没有处理好旧会话，您的数据库将填充数千条无用的数据。

有许多数据库存储可以与express-session一起使用。查看[README](https://github.com/expressjs/session#compatible-session-stores)上的完整列表。

## 你应该在哪里存储会话数据？

我们已经谈到了存储会话数据的三个常见的地方。那么你应该在哪里存储会话数据呢？

一般来说，我遵循规则，**“缓存先，然后是cookie，最后再数据库”**。

如果您可以访问 Memcached 或 Redis ，我会首选它。如果没有，请将您的数据存储在cookie中，但请确保保护您的密钥。最后，您可以将数据存储在数据库中，但要计划删除旧会话。