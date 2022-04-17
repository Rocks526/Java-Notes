# 一：node.js介绍

### 1.1 node.js简介

- Node.js是一个能够在服务器端运行JavaScript的开放源代码、 跨平台JavaScript运行环境。
- Node采用Google开发的V8引擎运行js代码，使用事件驱动、 非阻塞和异步I/O模型等技术来提高性能。
- Node大部分基本模块都用JavaScript编写。在Node出现之前， JS通常作为客户端程序设计语言使用，以JS写出的程序常在用户的浏览器上运行。
- Node.js允许通过JS和一系列模块来编写服务器端应用和网络相关的应用。
- 核心模块包括文件系统I/O、网络、二进制数据流、加密算法、数据流等等。Node 模块的API形式简单，降低了编程的复杂度。
- 常用的框架有Express.js、Socket.IO 和Connect等。Node.js的程序可以在Microsoft Windows、 Linux、Unix、Mac OS X等服务器上运行。
- Node.js也可以使用CoffeeScript、TypeScript、Dart语言，以及其他能够编译成JavaScript的语言编程。

### 1.2 node.js历史

| 时间       | 事件                                                      |
| ---------- | --------------------------------------------------------- |
| 2009年     | 瑞安·达尔（Ryan Dahl）在GitHub上发布node的最初版本        |
| 2010年1月  | Node的包管理器npm诞生                                     |
| 2010年底   | Joyent公司赞助Node的开发，瑞安·达尔加入旗下，全职负责Node |
| 2011年7月  | Node在微软的帮助下发布了windows版本                       |
| 2011年11月 | Node超越Ruby on Rails，称为GitHub上关注度最高的项目       |
| 2012年1月  | 瑞安·达尔离开Node项目                                     |
| 2014年12月 | Fedor Indutny在2014年12月制作了分支版本，并起名“io.js”    |
| 2015年初   | Node.js基金会成立（IBM、Intel、微软、Joyent）             |
| 2015年9月  | Node.js和io.js合并，Node 4.0发布                          |
| 2016年     | Node 6.0发布                                              |
| 2017年5月  | Node 8.0发布                                              |
| 2017年10月 | Node9.0发布                                               |
| 2018年4月  | Node10发布                                                |
| 2018年10月 | Node11发布                                                |
| 2019年4月  | Node12发布                                                |

> 截止2021年3月，最新版本为Node15。

### 1.3 node的应用

- 实时多人游戏
- 后端的Web服务，例如跨域、服务器端的请求
- 多客户端的通信，如即时通信

### 1.4 node.js安装

- node.js官网：https://nodejs.org/zh-cn/

官网提供windows的msi安装程序，根据提示安装即可。

- node.js环境验证

```javascript
node -v  # 查看node版本
node  # 进入node交互式环境
```

