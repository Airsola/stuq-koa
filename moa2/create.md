## 生成项目

```
$ npm i -g koa-generator
$ koa2 mvc
$ cd mvc
$ nrm use cnpm
$ npm i
```

至此准备完事儿

```
$ npm start
```

然后打开http://127.0.0.1:3000/,测试koa

打开代码，说明一下koa2的代码结构，并点评async，如何替换成其他写法。

修改2处，使得async写法，变为commonfunction形式

app.js

```
// logger
app.use( (ctx, next) => {
  const start = new Date();
  return next().then(function(){
    const ms = new Date() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
  });
});
```

另外routes/index.js

```
var router = require('koa-router')();

router.get('/', function (ctx, next) {
  ctx.state = {
    title: 'koa2 title'
  };

  return ctx.render('index', {
  });
})
module.exports = router;

```

> 为啥要用common function写法么？
