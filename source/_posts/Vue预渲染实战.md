---
title: Vue预渲染实战-prerender-spa-plugin
date: 2020-05-27 11:17:16
tags: [ Vue, 浏览器 ]
---



## 前言

官网需要 SEO，看了下方案觉得预渲染比较合适

* 界面少
* 弱交互



## [服务器端渲染 vs 预渲染 (SSR vs Prerendering)]([https://ssr.vuejs.org/zh/#%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%B8%B2%E6%9F%93-vs-%E9%A2%84%E6%B8%B2%E6%9F%93-ssr-vs-prerendering](https://ssr.vuejs.org/zh/#服务器端渲染-vs-预渲染-ssr-vs-prerendering))

> 如果你调研服务器端渲染 (SSR) 只是用来改善少数营销页面（例如 `/`, `/about`, `/contact` 等）的 SEO，那么你可能需要**预渲染**。无需使用 web 服务器实时动态编译 HTML，而是使用预渲染方式，在构建时 (build time) 简单地生成针对特定路由的静态 HTML 文件。优点是设置预渲染更简单，并可以将你的前端作为一个完全静态的站点。
>
> 如果你使用 webpack，你可以使用 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin) 轻松地添加预渲染。它已经被 Vue 应用程序广泛测试 - 事实上，[作者](https://github.com/chrisvfritz)是 Vue 核心团队的成员。



## [What is Prerendering?](https://github.com/chrisvfritz/prerender-spa-plugin#what-is-prerendering)

> Recently, SSR (Server Side Rendering) has taken the JavaScript front-end world by storm. The fact that you can now render your sites and apps on the server before sending them to your clients is an absolutely *revolutionary* idea (and totally not what everyone was doing before JS client-side apps got popular in the first place...)
>
> However, the same criticisms that were valid for PHP, ASP, JSP, (and such) sites are valid for server-side rendering today. It's slow, breaks fairly easily, and is difficult to implement properly.
>
> Thing is, despite what everyone might be telling you, you probably don't *need* SSR. You can get almost all the advantages of it (without the disadvantages) by using **prerendering.** Prerendering is basically firing up a headless browser, loading your app's routes, and saving the results to a static HTML file. You can then serve it with whatever static-file-serving solution you were using previously. It *just works* with HTML5 navigation and the likes. No need to change your code or add server-side rendering workarounds.
>
> In the interest of transparency, there are some use-cases where prerendering might not be a great idea.
>
> - **Tons of routes** - If your site has hundreds or thousands of routes, prerendering will be really slow. Sure you only have to do it once per update, but it could take ages. Most people don't end up with thousands of static routes, but just in-case...
> - **Dynamic Content** - If your render routes that have content that's specific to the user viewing it or other dynamic sources, you should make sure you have placeholder components that can display until the dynamic content loads on the client-side. Otherwise it might be a tad weird.



## 三种不同渲染方式的区别

* 客户端渲染：用户访问 url -> 请求 html 文件 -> 加载 css/js -> 前端动态渲染页面内容
	* 性能消耗在客户端
* 服务端渲染：用户访问 url -> 服务端根据访问路径请求所需数据 -> 拼接成 html -> 返回给前端
	* 网络传输数据量大
* 预渲染：构建阶段生成匹配预渲染路径的 html 文件 -> 用户访问 url -> 返回 html 给前端
	* 构建出来的 html 文件已经有静态数据，需要ajax数据的部分未构建



### 原理

> puppeteer是谷歌出品的一个插件，可以完成很多的操作，广泛使用在爬虫、测试自动化等浏览器自动化方向的应用，而prerender-spa-plugin这个插件正是依赖puppeteer操作chromium这个真正的浏览器内核对SPA跑了一遍，生成一个静态的HTML，里面是已经填好的dom节点和数据，就是这么简单粗暴，为了给用户直接返回有内容的xhtml文档，提前在一个浏览器里跑了一遍，生成了跑过js和css之后index.html(跟路由)和其他文档。这样的话有两个缺陷，第一个：无法展示用户自身的内容，第二个：不适合动态路由多的大型项目。
>
> 作者：mguy_1
> 链接：https://juejin.im/post/5c125787f265da611d668807
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



### 安装

```shell
yarn install prerender-spa-plugin --save
```

### 配置

```js
// router.js
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})
```

```js
// vue.config.js
const PrerenderSPAPlugin = require('prerender-spa-plugin');
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;

module.exports = {
  configureWebpack: {
    ……
    plugins: [
      new PrerenderSPAPlugin({
    		// 输出路径
        staticDir: path.join(__dirname, 'dist'),
  			// 需要预渲染的路由
        routes: ['/']
      })
    ]
  }
}

```



## 后记

配置还是比较简单，坑点是 prerender-spa-plugin 安装超级慢。官方源和淘宝源换来换去 / 科学上网开开关关，折腾了半天才下好。

都说大量路由不适合用预渲染，但实际上主要是构建过程比较慢，似乎对用户来说没什么区别？