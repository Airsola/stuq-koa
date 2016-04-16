# Koa入门


## 什么是Koa？

Koa是Node.js下一代web框架

## Koa 1 和 Koa 2



## 技术栈变更历史

http://stackshare.io/nodejs/in-stacks


## Why Node.js 4.x？

- es6语法特性支持支持，如generator
- koa是基于node 4+开发的，使用generator、箭头函数等特性

## 安装koa-generator

这里的generator是生成器的意思，用于生成项目骨架，[express-generator](https://github.com/expressjs/generator)就是一个比较好的例子，虽然比较精简，但结构清晰，足矣满足一般性的开发需求

鉴于很多人非常熟悉expressjs，这里假定大家也熟悉express-generator

express-generator提供的功能

- 生成项目骨架
- 约定目录结构（经典，精简，结构清晰）
- 支持css预处理器

koa-generator提供的功能

- 生成项目骨架
- 约定目录结构（和express-generator的结构一模一样）
- 支持css预处理器（暂未实行）

2个生成器共同的项目骨架结构

- app.js为入口
- bin/www为启动入口
- 支持static server，即public目录
- 支持routes路由目录
- 支持views视图目录
- 默认jade为模板引擎

koa-generator支持koa1.x和2.x，安装后，可以分别使用`koa`和`koa2`分别创建。

### 安装koa-generator

```shell
$ npm install -g koa-generator
```

# Getting Start

## 创建项目

[koa-generator](github.com/base-n/koa-generator)支持Koa1.x和2.x，安装后，可以分别使用`koa`和`koa2`分别创建。

### Koa 1.x

```shell
$ koa helloworld
```

### Koa 2.x

```shell
$ koa2 helloworld
```

### 安装依赖模块

```shell
$ cd  helloworld
$ npm install
$ npm start
```
### 启动，并在浏览器中查看

```shell
$ npm start
```

open http://127.0.0.1:3000


# Koa Core

## Context

## Middleware

## Generator

## Router

## Views

## Lifecycle

# 异步流程控制

