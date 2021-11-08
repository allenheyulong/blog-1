## 起因

因业务需要，项目中的部门接口需要替换，在理逻辑的时候，就发现有些接口是在asyncData中调用的。

## 调试接口

在替换接口之后，页面并没有呈现数据，浏览器network中并没有调用asyncData中的一些接口，这时候我就去排查查，该怎么去定位问题呢？

## 理解（网络上的解释）

**asyncData是一个两用方法：**

1、当强制刷新页面时，asyncData在服务端运行，在运行结束后拼装成页面返给你，类似jsp。所以，在刷新下，浏览器的console.log是不会打印任何东西的，但是在node的运行终端你可以看到console.log的结果；


2、当VUE项目内部用nuxt-link跳转时，它运行浏览器中。类似在普通vue页面中的mouted或者哪个钩子函数里写请求一样；这个时候，在asyncData里的consolelog会打印在浏览器端；

* asyncData在服务器端运行是为了满足SEO和减少HTTP请求；
* asyncData在浏览器端运行是为了满足正常的异步请求

## 调试

**1、先检查接口是否成功**

* 如果页面都无法呈现，你可以在axios中打印请求值和返回值

![axios调试.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f9a60261a0b42eeacdf12407c611e6b~tplv-k3u1fbpfcp-watermark.image)
* 如果接口报错，那么在vscode node服务中应该会有错误的输出信息

![nuxt调试.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1d3a446b900946b4b8a0be6942597c03~tplv-k3u1fbpfcp-watermark.image)
* 如果接口参数没有匹配预期，可以在vue DevTools小工具中查看接口返回的数据，因为我这里把请求后的数据在asyncData中return了，所以可以在实例的data中可以看得到

![vue.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fd38f10f7e494163b39b81eaccc4e8c8~tplv-k3u1fbpfcp-watermark.image)

**2、通过package.json文件进行debugger调试**

你可在你需要的地方打上断点，在package.json中点击调试按钮就可以进行调试

![企业微信20210624-102343.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7635341fdd144fb286e33d0e66e691ee~tplv-k3u1fbpfcp-watermark.image)

**3、可以尝试把接口换个地方调用**

由于asyncData的特殊性，开发者在浏览器看不到请求，你可以把请求放到created中输出一下返回数据，对比下，就能发现问题

## 发现问题

最后我的问题定位到了，是由于接口返回的数据格式，跟我理解的不一样，导致页面报错了