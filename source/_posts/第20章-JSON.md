---
title: 第二十章 JSON
date: 2020-03-12 00:11:48
tags: JavaScript高级程序设计
---



**JSON 是一种数据格式，不是编程语言**



## 语法

* 简单值
	* 字符串
	* 数组
	* 布尔值
	* null
* 复杂数据类型
	* 对象
		* 无序的键值对
	* 数组
		* 有序的值的列表
		* 可以通过数值索引访问



### 简单值

JSON 字符串必须使用双引号，JavaScript 字符串可以单引号

```js
"Hello world!"
```

### 对象

```js
{
  "属性": "值"
}
```

* 属性必须加双引号
* 值可以是简单值/复杂类型

### 数组

```js
[25, "hi", true]
```



}



## 解析与序列化

### JSON 对象

* `stringify()` js 对象序列化为 JSON 字符串
	* 函数及原型成员会被忽略
	* 值为 undefined 的属性也会被跳过
* `parse()` JSON 字符串解析为 js 值



### 序列化选项

* `JSON.stringify()` 参数
	* 过滤器
		* 数组
		* 函数 replacer
	* 选项
		* 是否在 JSON 字符串中保留缩进

##### 过滤结果

* 如果过滤器参数是数组，那么 stringily() 的结果中将只包含数组中列出的属性

* 如果是函数：该函数接收两个参数（属性名，属性值），根据属性名进行自定义序列化

```js
var book = {
  "title": "Professional JavaScript",
  "authors": [
    "Nicholas C. Zakas"
  ],
  edition: 3,
  year: 2011
};	// js 对象

var jsonText = JSON.stringify(book, function(key, value) {
  switch(key){
    case "authors": return value.join(",")
    case "year": return 5000;
    case "edition": return undefined;
    default: return value;
  }
});

// {"title":"Professional JavaScript","authors":"Nicholas C. Zakas","year":5000}
```



##### 字符串缩进

* 数字：空格数，最大缩进空格数 10
* 字符串：缩进字符，不是用空格，最长10

#####  toJSON() 方法

Date 对象自带，也可以自定义

做为函数过滤器的补充

>  stringify() 执行顺序
>
> 1. 如果存在 toJSON() 
> 	* 能取得有效值就调用
> 	* 否则返回对象本身
> 2. 如果提供了过滤器（第2个参数）
> 	* 应用过滤器，传入的值是 1 返回的值
> 3. 对 2 返回的每个值进行相应的序列化
> 4. 如果提供第 3 个参数，执行相应的格式化



### 解析选项

* `JSON.parse()` 参数
	* 还原函数 reviver
		* 在每个键值对上调用
		* 接收两个参数（属性即键，值）
		* 返回 undefined 表示删除相应的键

















