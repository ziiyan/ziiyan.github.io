---
title: Webpakc 从原理到实战
date: 2020-07-01 22:48:02
tags: Webpack
---



# Webpakc 从原理到实战

* 理解前端模块化
	* 发展历程
	* 不同模块化方案的实现
* 理解 webpack 打包核心思路
* 理解 loader & plugin



## 简介

* webpack 是一个现代 **JavaScript** 应用程序的静态模块 **打包器**
	* 默认只处理 Javascript
	* 逻辑多、文件多，项目的复杂度提高，所以需要打包
* 打包之外，还有“翻译”功能 —— loader
* 加上一些小动作 —— plugin
* 可插拔



## 原理与背景

### 理解前端模块化

#### 作用域

运行时 - 变量 函数 对象 可访问性

作用域决定了代码中变量和其它资源的可见性

#### 命名空间

#### 模块化的优点

* 作用域封装
* 重用性
* 解除耦合



### 模块化方案进化史

#### AMD

* Asynchronous Module Definition

```js
define('getSum', ['math'], function (math) {
  return function (a, b) {
    console.log('sum: ' + math.sum(a, b));
  }
});
```

#### COMMONJS

* 服务端模块标准，不是用于浏览器环境
* nodejs 采用并实现部分规范

```js
const math = require('./math');
exports.getSum = function (a, b) {
  return a + b;
}
```

#### ES6 MOUDLE

* Javascript 原生支持

```js
import math from './math';
export function sum(a, b) {
  return a + b;
}
```

#### Gulp、Grunt —— 自动化构建工具

* 打包只是自动化构建中的一部分
* webpack 专门针对打包，代表着模块化的流行



### webpack 的打包过程

* 从入口文件开始，分析整个应用的依赖树
* 将每个依赖模块包装起来，放到一个数组中等待调用
* 实现模块加载的方法，并把它放到模块执行的环境中，确保模块间可以互相调用
* 把执行入口文件的逻辑放在一个函数表达式中，并立即执行这个函数



## 实战

### 配置开发环境

#### npm 与包管理器

* 依赖
	* --save
	* --dev
* 版本号
	* ^version 中版本和小版本
		* ^1.0.1 -> 1.x.x 中取最新
	* ~version 小版本
		* ~1.0.1 -> 1.0.x 中取最新
	* version: 特定版本
* scripts

##### npm install 过程

- 检查 `.npmrc` 文件：优先级为：项目级的 `.npmrc` 文件 > 用户级的 `.npmrc` 文件> 全局级的 `.npmrc` 文件 > npm 内置的 `.npmrc` 文件
- 检查项目中有无 `lock` 文件。
- 无 `lock` 文件：
	- 从 `npm` 远程仓库获取包信息
	- 根据 `package.json` 构建依赖树，构建过程：
		- 构建依赖树时，不管其是直接依赖还是子依赖的依赖，优先将其放置在 `node_modules` 根目录。
		- 当遇到相同模块时，判断已放置在依赖树的模块版本是否符合新模块的版本范围，如果符合则跳过，不符合则在当前模块的 `node_modules` 下放置该模块。
		- 注意这一步只是确定逻辑上的依赖树，并非真正的安装，后面会根据这个依赖结构去下载或拿到缓存中的依赖包
	- 在缓存中依次查找依赖树中的每个包
		- 不存在缓存：
			- 从 `npm` 远程仓库下载包
			- 校验包的完整性
			- 校验不通过：
				- 重新下载
			- 校验通过：
				- 将下载的包复制到 `npm` 缓存目录
				- 将下载的包按照依赖结构解压到 `node_modules`
		- 存在缓存：将缓存按照依赖结构解压到 `node_modules`
	- 将包解压到 `node_modules`
	- 生成 `lock` 文件
- 有 `lock` 文件：
	- 检查 `package.json` 中的依赖版本是否和 `package-lock.json` 中的依赖有冲突。
	- 如果没有冲突，直接跳过获取包信息、构建依赖树过程，开始在缓存中查找包信息，后续过程相同

### 构建工程

### 核心特性

### loader 和 plugin



## webpack 与前端性能

### webpack 性能调优

#### 打包结果优化

* BundleAnalyZerPlugin

#### 构建过程优化

* 利用多线程

#### 预编译

#### 缓存

#### Tree-Shaking

## 总结

### 不止 "pack"

