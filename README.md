# StuQ Koa在线课程

这是和StuQ合作的一门关于Node.js下一代web框架Koajs的在线课程的文档

## 缘起（雷蒙德）

桑老师，打扰一下！[呲牙]

最近 Koa 很火啊，Fundun 老师上次分享的效果也挺好，他跟我说你已经做了很多东西，也准备做 Koa 分享，哈，我赶紧来找你了！

期待 Koa 众筹课上线 StuQ 啊！如果您时间紧的话，也建议考虑跟 Fundon 老师联袂分享！最近上线的移动测试众筹课程就是这样，4个老师一起分享，对老师来说可能更轻松一点！

当然，只是建议，主要看您的时间安排！如果您定了计划，我希望能提前宣传招生，这样会让招生运营压力小一点；

## 合作

前几天心脏有点小问题，已经缓过来，哈哈

目前《更了不起的 Node 4：将下一代 Web 框架 Koa 进行到底》一书目录已经出来了，准备连载，建立支付宝答疑群，博文视点那边也正在申请书号

我感觉目前有2种方式合作

1）按照《更了不起的 Node 4：将下一代 Web 框架 Koa 进行到底》来，怕和stuq的协议有冲突，这个之前有做的，估计会比较麻烦
2）我改写一下，仍然是这些东西，但不和书产生关联

课程和stuq合作

- 采用在线视频直播方式
- 以实战为主
- 在线解答疑问
- 课后作业review

## 讲师介绍

```
桑世龙，StuQ明星讲师，空弦科技CTO，开源项目 Moajs 作者，Node.js 技术传道者。

曾就职在新浪、网秦，曾做过前端、后端、数据分析、移动端负责人、做过首席架构师、技术总监，全栈技术实践者。

目前主要关注技术架构和团队梯队建设方向。
```

### 关于StuQ

![](img/1.png)

软件公司招聘需要巨大，但入门难，技术发展过快（指数），而人的曲线成长较慢，现在的慕客形式又过于老旧，呆板，少互动，所以社群时代的在线教育，一定是专业的、互动的、深入浅出、共同成长，这些正是StuQ最擅长的方面，我个人特别看好StuQ这个品牌，真心推荐，如果不是股份绑定，我一定会加入StuQ

## 整体思路

![Cover](cover.png)

先讲Node.js基础，然后Koa框架（从Koa-generator开始），理解一些基础概念，调试，测试等

![Three Tier Architecture](three-tier-architecture.png)

然后讲解http相关知识，比如get、post、上传如何实现，如果使用form实现，ajax实现，如何koa实现，如果使用cli curl命令测试，如果使用chrome的postman插件测试，如果使用supertest来测试api

然后讲解异步流程控制，从co开始讲，讲解co源码（包括convert、compose、中间件如何实现），说明generator和promise原理，讲解yieldable有哪5种，讲解async函数，讲异常处理和各种fy，并最终总结推导出哪些是必须学的以及未来的趋势

![All](all.png)

然后讲数据库操作，以mongodb为主，讲解crud单一操作以及mongoose各种特性，讲解组合操作（此时需要结合【异步流程控制】），讲解如何通过测试模型和dao接口，讲解如何封装一个dao库，以及mongoosedao的用法

然后把 koa（http + db + 异步流程控制） = 项目实战，之前是打散讲，现在整合一起，希望大家能够真正的理解它的各种机制。此时会设计到session等，完善注册登录等流程，逐渐加深对http的理解。

之后讲解阿里云上linux部署，从0开始，讲解pm2以及日常运维

之后讲解如何从头开始写一个moa2一样的框架，讲解scaffold脚手架原理，以及架构相关知识，前后端分离、cdn、缓存、mq、pub/sub，负载，rpc相关的内容不太多，看时间安排，我会尽力的讲清楚。

## 课程目录

- Node.js入门（4.0+）
  - es6
  - vscode
  - ava
  - npm
- Koa 1.x 和 2.x入门
- HTTP with Koa
- 异步流程控制(co, promise/a+, thunk, generator, aysnc/await)
- 数据库：mongodb
- 项目实战：实现值乎、分答类的系统
- 部署实战：阿里云部署
- Koa实战：moa2框架、脚手架、架构相关知识讲解

具体目录和正文见 [目录](SUMMARY.md)

## 为什么你能学会？

- 最小化问题：跟我学过的，大概都知道，我喜欢把问题最小化，让点足够小，这样才能让大家更容易学习。就像摆拼图一样，其他的都拼好，只留一个空，让你去填，是不是比很多空要容易？前端和node越来越复杂，最小化问题就变得更加重要
- 直戳重点：你迷惑的，可能正是我清楚的，那么多知识，我到底该怎么学？抓厚厚的书，还是？站在实战的角度看，你真的不必每个点都掌握，入门，然后再摸爬滚打，不要指望一下就全学会，编程没有捷径。
- 由浅入深：能人非常多，但能讲出来的不多，很多知识点都非常好，但怎样编排能够让人更容易学习呢？比如es 6有很多好特性，真的要用么？到底哪些可以用呢？比如为啥不上来就讲demo，而是先将http，然后讲db，然后在整合demo呢？由浅入深，若能恍然大悟，那便值得了。
- 实战：测试、例子，项目，课后作业都会有
- 在线答疑：有QQ群和微信群，有不懂的地方讲师也可以随时录制小视频

## 课程学习环境使用说明

- https://github.com/i5ting/nodejs-newbie
- vscode

## 报名

具体开课时间

- 第一期7月，已开课，30多人


如关注，请各位加[Stuq]公众号或Stuq小助手的私人微信StuQxiaozhushou

## Koa的预习资料  

- 中文文档 https://github.com/17koa/koa-guide
- 生成器 https://github.com/17koa/koa-generator
- co 4.6源码解析 https://cnodejs.org/topic/576bdffa889605241796f7d9
- AVA实践：面向未来的测试运行器 https://cnodejs.org/topic/57464cd8da0dea851e308101
- 模板引擎 https://cnodejs.org/topic/57353cc040b2969853981250
- 压测性能 https://cnodejs.org/topic/5728267f3f27a7c841bcb88e
- 使用vscode调试koa2-example https://cnodejs.org/topic/572209ea35af8a704195f552
- vscode使用手册 https://github.com/i5ting/vsc
- Koa 2 实例demo https://cnodejs.org/topic/570ae6c012def0933c43abc9
- Koa 2实用入门 https://cnodejs.org/topic/5709959abc564eaf3c6a48c8
- https://github.com/moajs/moa2
