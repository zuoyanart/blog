title: 翻译nightwatchjs guid
date: 2016-05-04 14:32:19
tags:
---

## 综述

### 什么是NightWatch?

--------
NightWatch.js是一个web应用和web网站的自动化测试框架，使用nodejs编写，并使用第三方[http://code.google.com/p/selenium/wiki/JsonWireProtocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol "Selenium WebDriver API.")。

他是一个完整的浏览器自动化（端到端）解决方案，旨在简化CI的构建和书写自动化测试的过程。

### Selenium介绍

-----

Selenium 是一个非常受欢迎的浏览器自动化的全面工具集，最初是为java研发的，但是也支持其他大多数的编程语言。

Selenium主项目包含：

* Selenium IDE
* Selenium Remote Control
* Selenium WebDriver
* Selenium Grid

NightWatch 使用 Selenium WebDriver，特别是WebDriver Wire Protocol用来执行浏览器自动化相关的任务。

##现实原理

NightWatch运行通过发送带有正确参数的HTTP请求到Selenium服务器然后解析响应。restful api协议的定义是通过 Selenium JsonWireProtocol,看下面的演示流程图。
![](/mdimg/operation.png)

大部分情况下， NightWatch 需要发送至少两次请求到Selenium 服务去执行命令或断言，第一次请求通过一个css选择器或者xpath表达式来定位元素，然后在获取到的元素上执行命令或断言。

## 安装

### install nodejs

----
这步省略，不翻译，网上的教程一大把。

### 安装 nightWatch

------
安装最新的版本通过npm命令，

```js
npm install nightwatch

```
国内用户还是建议使用cnpm
```js
cnpm install nightwatch
```

