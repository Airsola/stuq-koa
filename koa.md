
## 为什么要升...

异步代码写烦了，promise也写烦了吧？

koa 1.x通过co，支持generator，你可以yield来写同步代码了

koa 2.x把co移到koa-convert里，把中间件改成的更现代的风格

- common
- generatorFunction
- async/await

## express升级koa，成本高吗？

除了koa自身的550行代码和异步流程外（如果熟悉promise/a+规范，成本更低），其他都可以按照express风格一样弄，路由，view，日志，static，生成器、上传等都是一模一样的

- 熟悉promise/a+规范，通吃
- 按照express风格一样弄，连express-generator都有对应的koa-generator
- 中间件机制一样，只是参数有差异

整体来说，我觉得成本比较低。但难在目前koa 2.x的资料非常少。我在写《更了不起的Node 4： 将下一代web框架Koa进行到底》一书的时候，也是非常痛苦的。

下面给出：express和koa比较

https://github.com/koajs/koa/blob/master/docs/koa-vs-express.md



| Feature           | Koa | Express | Connect |
|------------------:|-----|---------|---------|
| Middleware Kernel | ✓   | ✓       | ✓       |
| Routing           |     | ✓       |         |
| Templating        |     | ✓       |         |
| Sending Files     |     | ✓       |         |
| JSONP             |     | ✓       |         |


这是一份很久以前的文档，目前来看也是对的，但是koa的生态已经很好了

| Feature           | Koa | Express | Connect |
|------------------:|-----|---------|---------|
| Middleware Kernel | ✓   | ✓       | ✓       |
| Routing           | ✓ koa-router    | ✓       |         |
| Templating        | ✓ koa-views   | ✓       |         |
| Sending Files     | ✓ koa-send   | ✓       |         |
| JSONP             | ✓ koa-safe-jsonp   | ✓       |         |


该有的基本都有了,于是我仿着express-generator写了koa-generator

技术栈如下

- https://github.com/alexmingoia/koa-router
- https://github.com/queckezz/koa-views
- https://github.com/koajs/static
- https://github.com/koajs/logger
- https://github.com/koajs/json
- https://github.com/koajs/body-parsers
- cookies已经koa内置了，无需处理

generator是生成器的意思，用于生成项目骨架，express-generator就是一个比较好的例子，虽然比较精简，但结构清晰，足矣满足一帮性需求

鉴于很多人非常熟悉expressjs，我就假定大家也熟悉express-generator

express-generator提供的功能

- 生成项目骨架
- 约定目录结构（经典，精简，结构清晰）
- 支持css预处理器

koa-generator提供的功能

- 生成项目骨架
- 约定目录结构（和express-generator的结构一模一样）
- 支持css预处理器（暂未实行）

那是不是就一样简单了？

如果express还不太熟悉，还是先看express吧，毕竟资料比较多。如果有机会，有人带的话，一定要学Koa的，它是趋势，无论性能、语法、生态，都会有非常明显的优势。

https://cnodejs.org/topic/56650091e7cd33da066d6ee7

## 对于掌握express的同学. 如何学习koa更快😂😂

参见问题【express升级koa，成本高吗？】

学习更快

- 按照express的方式玩koa啊，以自己最擅长的经验，学习新东西是最快的
- koa-generator可以玩玩

这样就可以koa入门了，然后玩一下moa2框架 + moag脚手架，对理解koa的一些好的实践是有好处的。

## koa 比 express 好在哪？ koa优势是啥？

同问、提升有哪几方面？1，2，3？

以koa 2.x为例吧

- 性能，比express只强不弱
- 语义上更清晰，更接近同步，无论是promise还是yield，还是await，都有各种支持
- 更好利用Node 4.x +版本的es6特性，更加现代

再加一点吧，大家都往哪里使劲，大家还是有感觉的，趋势啊。

## 比如说性能提升，主要是哪方面的

- 执行速度：Node 4 比之前版本性能提升，非常大，node 6在模块加载上又有大优化，整体来说越来越强大，执行速度越来越快
- 内存：越来约省
- 并发数：越来越高

具体数据还没有，到4的时候基本是翻倍的，之后的数据没严格比较过，抽空测测

## 在web的框架层面上有什么支持

没啥支持

- 不捆绑任何中间件
- 不带有路由

这算小而美哲学的一种体现，模块职责单一，其他的功能其他模块来做就好了。koa算基础模块，所以未来基于koa的框架会有非常非常多的。目前没有特别好的框架，我的moa2也只是起步，哈哈

## koa 1都没看，koa 2就来了

> 其实，koa2比koa多了什么东西或者说优化了什么东西？


核心api基本无变化，主要是中间件变化，去掉co，移到convert里了

看koa-convert https://github.com/koajs/convert

1.x

```
var koa = require('koa');
var app = koa();
```

- 只支持generator写法的中间件

```
app.use(function *(){
  this.body = 'Hello World';
});
```

2.x

```
const Koa = require('koa');
const app = new Koa();
```

注意new

- common

```
app.use((ctx, next) => {
  const start = new Date();
  return next().then(() => {
    const ms = new Date() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
  });
});
```

- generatorFunction

```
app.use(co.wrap(function *(ctx, next) {
  const start = new Date();
  yield next();
  const ms = new Date() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
}));
```

- async/await(Babel required)

```
app.use(async (ctx, next) => {
  const start = new Date();
  await next();
  const ms = new Date() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});
```

这里的中间件差异，可以这样理解，generator里的上下文是this，所以没显式传，而其他的，为了convert方便，以ctx显示传值。

next和express里的next的机制是一样的，就是返回值可以yield或await而已，其实它是promise包裹了而已。


## koa2和koa1之间该选koa2还是koa2还是koa2？

> 现在到底是生产环境用koa2还是koa1，最近正在转战koa，一直有这个困惑？

推荐Koa 2.x版本

1. 性能比1.x要好
1. 是以后koa发展重点，1.x的写法会在3.x里干掉
1. 为以后async/await切换提供学习机会

## 暂时不用async的原因是什么？

babel的实现效率比较低，见koa、koa2、koa2-async和express的压测性能比较http://17koa.com/koa-benchmark/ ,随着中间件的增多，效率直线下降。

先学着，但不要上产品环境。等Node.js原生支持了就用啊，它也是趋势，绝对好东西。估计10月份就能实现

```
 Async/await should arrive – behind an ‘experimental’ flag – in Chrome 53, which is scheduled to be released into stable channel on 6 September, so will hopefully arrive (behind a flag) in Node v7, in October.
```

## 看一下Koa 2 帅帅的代码

### restful api

```
/**
 * Auto generate RESTful url routes.
 *
 * URL routes:
 *
 *  GET    /users[/]        => user.list()
 *  GET    /users/new       => user.new()
 *  GET    /users/:id       => user.show()
 *  GET    /users/:id/edit  => user.edit()
 *  POST   /users[/]        => user.create()
 *  PATCH  /users/:id       => user.update()
 *  DELETE /users/:id       => user.destroy()
 *
 */
```

看起来很爽有木有？

### 中间件

Koa 2.x最常见的modern中间件

```
exports.list = (ctx, next) => {
  console.log(ctx.method + ' /users => list, query: ' + JSON.stringify(ctx.query));

  return User.getAllAsync().then(( users)=>{
    return ctx.render('users/index', {
      users : users
    })
  }).catch((err)=>{
      return ctx.api_error(err);
  });
};
```

只要懂promise/a+规范，就能搞定，所以你以前玩express的经验，还是会非常有用呢~

Koa 2.x中使用generator

```
exports.list = function *(ctx, next) {
  console.log(ctx.method + ' /students => list, query: ' + JSON.stringify(ctx.query));
  
  let students = yield Student.getAllAsync();
  
  yield ctx.render('students/index', {
    students : students
  })
};
```

有木有同步的感觉？

```
let students = yield Student.getAllAsync();
```

本来从数据库里获取所有学生信息是异步的操作，但是yield把它掰弯成同步的，有木有很爽？


实际上，它也是co包过的，这样更便于处理err

```
router.get('/list', (ctx, next) => {
  return co.wrap($.list)(ctx, next).catch(err => {
    return ctx.api_error(err);
  })
}); 
```

代码就这么精简

Koa 2.x中使用async/await

```
exports.list = async (ctx, next) => {
  console.log(ctx.method + ' /students => list, query: ' + JSON.stringify(ctx.query));
  try {
    let students = await Student.getAllAsync();
  
    await ctx.render('students/index', {
      students : students
    })
  } catch (err) {
    return ctx.api_error(err);
  }
};
```

它和generator其实没有太多区别，一样简单、命令，一通百通。

### 性感的ava测试

```
// *  GET    /users[/]        => user.list()
test('GET /' + model, function * (t) {
  var res = yield superkoa('../../app.js')
    .get('/' + model)

  t.is(200, res.status)
  t.regex(res.text, /table/g)
})
```

测试也成了同步的感觉，有木有爽爽哒？

来个更复杂的原子性测试

```
// *  GET    /users/:id       => user.show()
test('GET /' + model + '/:id show', function * (t) {
  var res1 = yield superkoa('../../app.js')
    .post('/api/' + model)
    .send(mockUser)
    .set('Accept', 'application/json')
    .expect('Content-Type', /json/)

  user = res1.body.user

  var res = yield superkoa('../../app.js')
    .get('/' + model + '/' + user._id)

  t.is(200, res.status)
  t.regex(res.text, /Edit/)
})
```

想要测试展示，就要先创建，每一步都是同步的，是不是代码逻辑看起来更清楚？

