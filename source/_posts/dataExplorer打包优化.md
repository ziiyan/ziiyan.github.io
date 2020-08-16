---
title: DE打包优化
date: 2020-04-07 19:24:25
tags: [工作]
---

# 成果

### 优化前

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200403192753922.png" alt="image-20200403192753922" style="zoom:50%;" />

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200403192922295.png" alt="image-20200403192922295" style="zoom:50%;" />

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200403194220981.png" alt="image-20200403194220981" style="zoom:50%;" />

### 优化后



# 工具

### 安装

```shell
npm install webpack-bundle-analyzer
```

### 配置

因为是 vue-cli 3.0，没有 webpack 配置文件了，在根目录下自己手动新建 `vue.config.js`

```js
module.exports = {
  chainWebpack: config => {
    config
    .plugin('webpack-bundle-analyzer')
    .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin)
  }
}
```

### 查看

```shell
npm run server
```

重新启动之后访问 http://127.0.0.1:8888 就是可视化的图了



# Webpack 文件分离

Webpack 文件分离包括两个部分，一个是 Bundle 的分离，一个是 Code 代码的分离:

- Bundle splitting: 实际上就是创建多个更小的文件，并行加载，以获得更好的缓存效果；主要的作用就是使浏览器并行下载，提高下载速度。并且运用浏览器缓存，只有代码被修改，文件名中的哈希值改变了才会去再次加载。
- Code splitting: 只加载用户最需要的部分，其余的代码都遵从懒加载的策略；主要的作用就是加快页面加载速度，不加载不必要加载的东西。

## Bundle splitting

### 配置

```js
module.exports = {
  chainWebpack: config => {
    config
    .plugin('webpack-bundle-analyzer')
    .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin)
  },
  configureWebpack: {
    optimization: {
        splitChunks: {
            chunks: 'all',
            maxInitialRequests: Infinity,
            minSize: 0,
            minChunks: 1,
            cacheGroups: {
              commons: {
                test: /[\\/]node_modules[\\/]/,
                name(module) {
                  const packageName = module.context.match(/[\\/]node_modules[\\/](.*?)([\\/]|$)/)[1];
                  return `modules.${packageName.replace('@', '')}`;
                }
              }
            }
        }
    }
  }
}
```

> 首先增加 maxInitialRequests 并设置成 Infinity，指定这个入口文件最大并行请求数
>
> 然后将 minSize 和 minChunks 分别设置成 0 和 1，即使模块非常小也将其提取出来，并且这个模块的引用次数只有 1 也要提取
>
> 最后配置匹配的依赖以及分离出的文件名格式
>
> 另外，我们还将运行时代码分离出来，这块代码还可以配合 **InlineManifestWebpackPlugin** 直接插入到 HTML 文件中。这里我们将这个配置设置成 single，即将所有chunk的运行代码打包到一个文件中

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200407143915100.png" alt="image-20200407143915100" style="zoom:50%;" />

现在可以看到 app.js 被拆解成很多个单独的包，这些包在加载时可以并行下载，提高下载速度。

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200407144235648.png" alt="image-20200407144235648" style="zoom:50%;" />

# 逐个击破

## Echarts

项目里用到的只有 bar 这个图形，所以按需载入即可。

* `/src/plugins`  下新建文件 `echarts.js`：

```js
import echarts from 'echarts/lib/echarts'

import 'echarts/lib/chart/bar'
import 'echarts/lib/component/legend'

export default echarts
```

* 使用到 Echarts 的组件中：

```js
import echarts from '@/plugins/echarts'
```

* 2.52 M -> 830.76 KB

<img src="/Users/wuziyan/Library/Application Support/typora-user-images/image-20200407144602273.png" alt="image-20200407144602273" style="zoom:50%;" />

## monaco

这个是代码编辑器，用到的地方比较多，本来想像 echarts 一样按需引入，但是引入完之后依旧非常大，考虑用 CDN 加速，参考：

https://github.com/microsoft/monaco-editor/blob/master/docs/integrate-amd-cross.md

https://www.cnblogs.com/XHappyness/p/9708102.html

```shell
npm install html-loader --save-dev
```

```js
// index.html

    <script type="text/javascript" src="https://cdn.bootcss.com/monaco-editor/0.20.0/min/vs/loader.js"></script>
    <script>
      require.config({ paths: { 'vs': 'https://cdn.bootcss.com/monaco-editor/0.20.0/min/vs' }});
      window.MonacoEnvironment = {
        getWorkerUrl: function(workerId, label) {
          return `data:text/javascript;charset=utf-8,${encodeURIComponent(`
            self.MonacoEnvironment = {
              baseUrl: 'https://cdn.bootcss.com/monaco-editor/0.20.0/min/'
            };
            importScripts('https://cdn.bootcss.com/monaco-editor/0.20.0/min/vs/base/worker/workerMain.js');`
          )}`;
        }
      };
      require(["vs/editor/editor.main"], function () {});
    </script>
```

```js
// vue.config.js

module.exports = {
    ···
    chainWebpack: config => {
        config.module
            .rule('html')
            .test(/\.html$/)
            .use('html-loader')
            .loader('html-loader')
    }
  ···
  configureWebpack: {
    ...
    externals: {
      'monaco-editor': 'monaco-editor'
    }
  }
}
```

折腾了大半天总算可以，使用的时候需要 `window.monaco.editor.create(...)`

