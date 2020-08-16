---
title: SpringBoot
date: 2020-04-02 14:11:47
tags: Java
---

# Spring Boot 入门

## Srping Boot 简介

> 简化 Spring 应用开发的一个框架；
>
> 整个 Spring 技术栈的一个大集合；
>
> J2EE 开发的一站式解决方案；

## 微服务

微服务：架构风格（微服务化）

一个应用应该是一组小型服务；可以通过 HTTP 的方式进行互通；

每一个功能元素最终都是一个可独立替换和独立升级的软件单元；

## Hello World 探究

### POM文件

#### 父项目

```js
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
// 它的父项目是（cmd+鼠标左键点击 spring-boot-starter-parent 查看）
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>
// 真正管理 Spring Boot 应用里所有依赖版本
```

#### 启动器

##### spring-boot-starter-web

* spring-boot-starter：SpringBoot 场景启动器

Spring Boot 将所有的功能场景都抽取出来，做成一个个启动器，只需要在项目引入这些启动器相关场景都所有依赖都会导入进来，要什么功能就导入什么启动器

* spring-boot-starter-web：帮我们导入了 web 模块正常运行所依赖的组件

### 主程序类，主入口类

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

* @SpringBootApplication：说明这个类是主配置类，运行该类的 main 方法启动 SpringBoot 应用

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

* @SpringBootConfiguration：SpringBoot 的配置类
	* 标注在某个类上，表示这是一个 SpringBoot 的配置类
	* @Configuration：配置类上标注这个注解
	* @Component：说明这也是一个spring的组件

* @EnableAutoConfiguration：自动配置
	* @AutoConfigurationPackage：自动配置包
		* @Import({Registrar.class})
			* Spring 的底层注解，给容器中导入一个组件：导入的组件由 Registrar.class 指定
			* 将主配置类（@SpringBootApplication标注的类）的所在包以及下面所有子包里面的所有组件扫描到 Spring 容器
	* @Import({AutoConfigurationImportSelector.class})：自动配置导入选择
		* 导入哪些组件的选择器
		* 将所有需要导入的组件以全类名的方式返回；这些组件就会被添加到容器中；
		* 会给容器导入非常多的自动配置类（xxxAutoConfiguration）；就是给容器中导入这个场景需要的所有组件，并配置好这些组件；
* META-INF/spring.factories：自动配置的核心文件

结论：Springboot 所有自动配置都是在启动的时候扫描并加载 `spring.factories ` 所有的自动配置类都在这里面，但不一定生效，要判断条件是否成立，只要导入了对应的 start，就有对应的启动器了，有了启动器，我们自动装配就会生效，然后就配置成功

1. springboot 在启动的时候，从类路径下 `/META-INF/spring.factories` 获取指定的值
2. 将这些自动配置的类导入容器，自动配置就会生效，帮我们进行自动配置
3. 整合 javaEE，解决方案和自动配置的东西都在 spring-boot-autoconfigure-2.2.0.RELEASE.jar 这个包下
4. 它会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加到容器
5. 容器中也会存在非常多的 xxxAutoConfiguration 的文件（@Bean），就是这些类给容器中导入了这个场景需要的所有组件；并自动配置，@Configuration，JavaConfig
6. 有了自动配置类，就免去了手动配置

## 使用 Spring Initialize 快速创建 Spring Boot 项目

默认生成的 Spring Boot 项目

* 主程序已经生成好了，只需要编写自己的逻辑
* resources 文件夹中目录结构
	* static：保存所有的静态资源；js css images
	* templates：保存所有的模版页面；（Spring Boot 默认 jar 包使用嵌入式的 Tomcat，默认不支持 JSP 页面）；可以使用模版引擎
	* application.properties：SpringBoot 应用的配置文件：可以修改端口等



#配置文件

* application.properties
	* 语法结构：key=value
* application.yml（推荐）
	* 语法结构：key:空格value
	* Yaml 可以直接给实体类赋值
		* @ConfigurationProperties(prefix = "xxx")

## JSR-303 校验

* @validated 数据校验

## 配置文件位置及优先级

1. file: ./config/
2. file: ./
3. classpath: /config/
4. classpath:/

## 自动装配原理

1. SpringBoot 启动会加载大量的自动配置类
2. 看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中
3. 在看这个自动配置类中到底配置了哪些组件（需要的组件有的话就不用手动配置了）
4. 给容器中添加组件的时候，会从 properties 类中回去某些属性，只需要在配置文件中指定这些属性的值即可

* xxxAutoConfiguration 自动配置类：给容器中添加组件

* xxxProperties：封装配置文件中相关属性



# SpringBoot Web 开发

## 静态资源

* 在 springboot 中，可以使用以下方式处理静态资源
	* webjars
	* public、static、/**、resources
* 优先级：resources > static > public

## 模版引擎

```xml
		<dependency>
			<groupId>org.thymeleaf</groupId>
			<artifactId>thymeleaf-spring5</artifactId>
		</dependency>
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-java8time</artifactId>
		</dependency>
```

* 前后端分离还有用吗这个？



## SpringMVC 配置原理

* 在 Srpingboot 中，有非常多的 xxxConfiguration 帮助我们进行扩展配置



