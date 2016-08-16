# pub/sub

发布订阅模式

核心点

- Pub/Sub 模型定义了如何向一个内容节点发布和订阅消息，这些节点被称作主题(topic)。
- 主题可以被认为是消息的传输中介，发布者(publisher)发布消息到主题，订阅者(subscriber) 从主题订阅消息。
- 主题使得消息订阅者和消息发布者保持互相独立，不需要接触即可保证消息的传送。

## pub/sub解决了什么样的问题？

- 耗时的问题，比如上传，格式转换、计算等其他耗时任务，消耗的时间大于http服务器的超时时间
- 类似于im，一对一，一对多、群组等

## 技术选型

[faye](https://github.com/faye/faye)，最早用的时候是写ruby的时候，那时候为了用faye而了解node，因为node版本的faye效率比ruby的高非常多，也算是“因ruby得node”吧。

> Simple pub/sub messaging for the web http://faye.jcoglan.com

## 集成

安装

```
$ npm i -S faye
```

最简单例子

```
var http = require('http'),
    faye = require('faye');

var server = http.createServer(),
    bayeux = new faye.NodeAdapter({mount: '/'});

bayeux.attach(server);
server.listen(8000);
```

很明显，它是attach到http的server实例上的，所以我们也需要把koa改成对应的

使用类似于express的www写法

```
#!/usr/bin/env node

var faye = require('faye'); 

var bayeux = new faye.NodeAdapter({
	mount		: '/faye', 
	timeout : 45
});

/**
 * Module dependencies.
 */

var app = require('./app');
// console.dir(app)

var debug = require('debug')('demo:server');
var http = require('http');

/**
 * Get port from environment and store in Express.
 */

var port = normalizePort(process.env.PORT || '3000');
// app.set('port', port);

/**
 * Create HTTP server.
 */

var server = http.createServer(app.callback());

bayeux.attach(server);

/**
 * Listen on provided port, on all network interfaces.
 */

server.listen(port);
server.on('error', onError);
server.on('listening', onListening);

/**
 * Normalize a port into a number, string, or false.
 */

function normalizePort(val) {
  var port = parseInt(val, 10);

  if (isNaN(port)) {
    // named pipe
    return val;
  }

  if (port >= 0) {
    // port number
    return port;
  }

  return false;
}

/**
 * Event listener for HTTP server "error" event.
 */

function onError(error) {
  if (error.syscall !== 'listen') {
    throw error;
  }

  var bind = typeof port === 'string'
    ? 'Pipe ' + port
    : 'Port ' + port;

  // handle specific listen errors with friendly messages
  switch (error.code) {
    case 'EACCES':
      console.error(bind + ' requires elevated privileges');
      process.exit(1);
      break;
    case 'EADDRINUSE':
      console.error(bind + ' is already in use');
      process.exit(1);
      break;
    default:
      throw error;
  }
}

/**
 * Event listener for HTTP server "listening" event.
 */

function onListening() {
  var addr = server.address();
  var bind = typeof addr === 'string'
    ? 'pipe ' + addr
    : 'port ' + addr.port;
  debug('Listening on ' + bind);
}

```

其实核心很简单

```
var server = http.createServer(app.callback());
```

这是koa和http的集成，所以有了server实例就可以加faye了。

```
bayeux.attach(server);
```

至此，一个建的服务就构建好。

## 模式1：客户端订阅，服务器端发布

构建前端

```
extends layouts/layout

block content
  h1= title
  p Welcome to StuQ Koa小班课！
  div
    a(href='/users/register') 注册 
    span |
    a(href='/users/login') 登录 
    span |
    a(href='/users/logout') 销毁
  script(src="http://127.0.0.1:3000/faye/client.js")
  script.
    var client = new Faye.Client('http://127.0.0.1:3000/faye', {
      timeout : 120,
          retry       : 5
      });

      Logger = {
        incoming: function(message, callback) {
          console.log('incoming', message);
          callback(message);
        },
        outgoing: function(message, callback) {
          console.log('outgoing', message);
          callback(message);
        }
      };

      client.addExtension(Logger);

      client.on('transport:down', function() {
        // the client is offline
      });

      client.on('transport:up', function() {
        // the client is online
      });

      var subscription = client.subscribe('/messages', function(message) {
        // handle message
          alert(message.text);
      });

```

服务器端发布

```
"use strict"

var Faye = require('faye'); 

const router = require('koa-router')()


var client = new Faye.Client('http://127.0.0.1:3000/faye');

console.log(client)

// 首页
router.get('/', ctx => {
  var session = ctx.session;
  
  setTimeout(function(){
    console.log('publish')
    client.publish('/messages', {
      text: 'Hello world'
    });
  },3000)
  
  
  if (session.current_user) {
    return ctx.render('index', {
      title: '演示session用法: 当前状态为已登录或注册成功'
    });
  } else {
    return ctx.render('index', {
      title: '演示session用法: 当前状态为游客'
    });
  }
});

module.exports = router
```

核心

```
    client.publish('/messages', {
      text: 'Hello world'
    });
```

## 模式2：客户端订阅，客户端发布

```
    <script type="text/javascript">
      var client = new Faye.Client('http://127.0.0.1:3000/faye', {
        timeout : 120,
        retry       : 5
    });

    Logger = {
      incoming: function(message, callback) {
        console.log('incoming', message);
        callback(message);
      },
      outgoing: function(message, callback) {
        console.log('outgoing', message);
        callback(message);
      }
    };

    client.addExtension(Logger);

    client.on('transport:down', function() {
      // the client is offline
    });

    client.on('transport:up', function() {
      // the client is online
    });


    var subscription = client.subscribe('/foo', function(message) {
      // handle message
        console.log(message);
    });

    setTimeout(function(){
        var publication = client.publish('/foo', {text: 'Hi there, foo test'});

        publication.then(function() {
          alert('Message received by server!');
        }, function(error) {
          alert('There was a problem: ' + error.message);
        });

    },2000);
</script>
```

核心

```
var publication = client.publish('/foo', {text: 'Hi there, foo test'});
```

是不是有点像im？

## 效率问题

它有engine的概念，即存储引擎

https://faye.jcoglan.com/node/engines.html

- faye-redis
- faye-redis-sharded
- faye-couchbase 

```
var faye      = require('faye'),
    fayeRedis = require('faye-redis');

var server = new faye.NodeAdapter({
  mount:    '/faye',
  timeout:  45,
  engine:   {
    type:   fayeRedis,
    host:   'localhost',
    port:   6379
  }
});
```

## 服务化

把这样的pub/sub丢出去，变成http接口，会不会更简单易用

https://github.com/i5ting/fayeserver
