# hello world

<!-- toc -->

## Koa 1.x

```shell
$ koa helloworld
```


## Koa 2.x

```shell
$ koa2 helloworld
```

# 路由

写法说明

## Koa 1.x

只要是koa-router写的路由都可以加载的，加载方式和express里一样

```javascript
var router = require('koa-router')();

router.get('/', function *(next) {
  this.body = 'this /1!';
});


router.get('2', function *(next) {
  this.body = 'this /2!';
});

module.exports = router;
```

一定要区分

```javascript
url = /2
router.get('2', function *(next) {
  this.body = 'this /2!';
});
```

```javascript
url = //2
router.get('/2', function *(next) {
  this.body = 'this /2!';
});
```

这个是koa-router的一个问题，和express里的路由稍有不一样，注意一些即可

## Koa 2.x

由于Koa 2.x支持async，故写法稍有差异

```javascript
var router = require('koa-router')();

router.get('/', async function (ctx, next) {
  await ctx.render('index', {
    title: 'Hello World Koa!'
  });
});
module.exports = router;
```


# 切换视图模板引擎

视图默认使用的是 `jade` 。如果想使用其他的视图

```shell
$ koa 1.x/views-ejs -e
```

说明

- `-e, --ejs           add ejs engine support (defaults to jade)`

koa-generator使用的是[koa-views](https://github.com/queckezz/koa-views)，支持[所有consolidate.js支持的模板引擎](https://github.com/tj/consolidate.js#supported-template-engines)
