---
title: cookies,sessionStorage,localStorage的区别
date: '2018-03-22 21:00:00'
categories: [
前端两三事
]
layout: post
author: xy
---

### cookies

Cookie技术是客户端的解决方案。

Cookie是用于服务器发给客户端的特殊信息，这些信息是以文件的形式存在客户端本地客户端中，然后客户端每次向服务器发送请求的时候会带上这些cookie信息。

举个栗子，用户第一次登录网站A时候，客户端会把用户的登录信息提交至服务器，然后服务器向服务器相应返回的文本中同时会返回这些个人信息，存在http response的header中，之后客户端再向服务器发送请求的时候都会带上相应的cookie，服务器就会根据请求头cookie知道客户端特有的信息，识别该用户。下次访问该网站A的时候，就不需要重复登录。

![cookie](/images/js/cookie.jpg)

特点：

1、Cookie功能需要浏览器支持。浏览器会根据不同的方式把不同的浏览器为文件夹，以文本方式储存。

2、cookie在客户端是由浏览器来管理的，具有不可跨域名性，Google只能操作Google的cookie，百度只能操作百度的cookie，两者却不能互相操作彼此的cookie。

3、cookie具有时效性。maxAge秒后自动失效。maxAge为0则表示该删除Cookie。如果maxAge为负数，则表示该Cookie仅为本浏览器窗口和子窗口内有效，关闭窗口后该Cookie失效

除了用Cookie，还经常使用session来记录客户端的状态。

### Session

Session技术是服务端的解决方案。

在用户第一次访问服务器的时候就自动创建了Session，每个用户都有一个独立的sessionID。这个sessionID在随后的请求中会被用来重新获取已经创建的session，也可以给session增加内容，这些内容也只会存在服务器中，发给客户端的只有sessionID。session是依赖cookie实现的。因为session id是发送给客户端，储存在cookie文件中的，如果浏览器禁用掉cookie的话，相应的session是无法使用的。

### Storage

H5提供了两种客户端储存数据的方式

sessionStorage:针对一个session的储存。仅在当前浏览器会话期间有效，不可持久保持

localStorage:针对本地储存。即使关闭了浏览器和窗口仍能长久保存。

1、对比cookie，储存的数据不能超过4k，storage的能达到5M。

2、Storage可以减少网络流量，一旦保存在本地就可以避免再向服务器请求数据。


| NAME | 时效  | 储存大小 | 安全性 | 储存方式 | 作用域 |
| :---- | :---- | :---- | :---- |:---- |:---- |
| cookies | maxAge | 最大4k  | 低 | 本地 | 跨浏览器共享 |
| Session | 由服务器管理<br/>用户最后一次操作时间开始记时 | 存sessionID,过多会导致内存溢出  |高 |服务器 | 跨浏览器共享 |
| Storage | localStorage无时效<br/>sessionStorage：窗口或标签页关闭时 |  最大5M |中 | 浏览器端 | 不可跨浏览器 |
