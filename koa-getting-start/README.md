# Koa入门

在学习完Node.js基础之后，就到了学习下一代web框架Koa的时间了。

![Three Tier Architecture](three-tier-architecture.png)

从这个经典的三层架构图，我们可以知道，Koa位于web server层的微信web框架，它的上游是http协议相关内容，它的下游是db相关内容，也就是说Koa框架所处的位置是连接http和db的桥梁，是非常重要的，在后面讲http和db的时候都会结合着Koa进行讲解。

## 核心内容

- 什么是Koa？
- 入门
- koa-generator
- koa core
  - Context
  - Middleware
  - Generator
  - Router
  - Views
  - Lifecycle
- 异步流程控制

## 下节内容预告

结合koa框架，学习更多http相关的内容，从get、post、文件上传，到更复杂的session等，结合用户管理相关的例子，让大家对Koa的理解更加深入。
