# 随堂练习：写一个基于koa的静态http模块并发布

重点在于理解koa中间件，扩展koa用法（不只是web框架，它还是Node.js模块，它可以做更多），并复习巩固上一节学的Node.js模块写法

## 练习koa用法，集成以下中间件

```
    "koa": "^2.0.0",
    "koa-compress": "^2.0.0",
    "koa-conditional-get": "^2.0.0",
    "koa-etag": "^3.0.0",
    "koa-favicon": "^2.0.0",
    "koa-static": "^3.0.0",
```

app.js

```
var serve = require('koa-static');
var Koa = require('koa');
var app = new Koa();

var favicon = require('koa-favicon');
var compress = require('koa-compress')
var conditional = require('koa-conditional-get');
var etag = require('koa-etag');

app.use(compress({
  filter: function (content_type) {
    return /text/i.test(content_type)
  },
  threshold: 2048,
  flush: require('zlib').Z_SYNC_FLUSH
}))


app.use(favicon(__dirname + '/public/favicon.ico'));

// etag works together with conditional-get
app.use(conditional());
app.use(etag());


// or use absolute paths
app.use(serve(__dirname + '/dist'));

app.listen(9090);

console.log('listening on port 9090');
```

## 将上面代码改成bin模块并发布

static http server模块核心是koa-static模块

```
// or use absolute paths
app.use(serve(__dirname + '/dist'));
```

这里的目录即要作为static http server的目录，也就是我们需要动态的把它通过命令行配置

假设模块名称为stuq-koa-static-server

```
$ npm i -g stuq-koa-static-server
```

通过内置命令`stuq-koa-static-server启动服务，

```
$ stuq-koa-static-server .
```

如果没有指定目录，即当前目录

```
$ stuq-koa-static-server
```

## 知识点

- 获取当前目录 
- 命令行参数解析，推荐[commander](https://github.com/tj/commander.js)

## 更多

参考[http-server](https://github.com/indexzero/http-server)模块，增加更多cli配置项

```
$ http-server -h
usage: http-server [path] [options]

options:
  -p           Port to use [8080]
  -a           Address to use [0.0.0.0]
  -d           Show directory listings [true]
  -i           Display autoIndex [true]
  -e --ext     Default file extension if none supplied [none]
  -s --silent  Suppress log messages from output
  --cors[=headers]   Enable CORS via the "Access-Control-Allow-Origin" header
                     Optionally provide CORS headers list separated by commas
  -o [path]    Open browser window after starting the server
  -c           Cache time (max-age) in seconds [3600], e.g. -c10 for 10 seconds.
               To disable caching, use -c-1.
  -U --utc     Use UTC time format in log messages.

  -P --proxy   Fallback proxy if the request cannot be resolved. e.g.: http://someurl.com

  -S --ssl     Enable https.
  -C --cert    Path to ssl cert file (default: cert.pem).
  -K --key     Path to ssl key file (default: key.pem).

  -r --robots  Respond to /robots.txt [User-agent: *\nDisallow: /]
  -h --help    Print this list and exit.
```
