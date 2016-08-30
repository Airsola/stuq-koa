## 自动挂载路由

注意是next版本，我们用的是koa2

```
$ npm install --save mount-koa-routes@next
```

app.js里修改如下

修改1

```
var routes = require('./routes/index');
var users = require('./routes/users');
```

改为
```
const mount = require('mount-koa-routes');
```

修改2

```
app.use('/', routes);
app.use('/users', users);
```

改为

```
mount(app,  __dirname + '/routes', true);
```

然后

```
$ npm start
[nodemon] app crashed - waiting for file changes before starting...
[nodemon] restarting due to changes...
[nodemon] starting `node bin/www`
koa deprecated Support for generators will been removed in v3. See the documentation for examples of how to convert old middleware https://github.com/koajs/koa/tree/v2.x#old-signature-middleware-v1x app.js:18:5
mount route /index.js 
mount route /users.js 

******************************************************
		MoaJS Apis Dump
******************************************************

┌───────────────────────────────────────────────────────┬────────┬─────────┐
│ File                                                  │ Method │ Path    │
├───────────────────────────────────────────────────────┼────────┼─────────┤
│ /Users/sang/workspace/github/your-koa/routes/index.js │ GET    │ /       │
├───────────────────────────────────────────────────────┼────────┼─────────┤
│ /Users/sang/workspace/github/your-koa/routes/users.js │ GET    │ /users/ │
└───────────────────────────────────────────────────────┴────────┴─────────┘
        
```

在路由里复制users.js为test.js

测试

http://127.0.0.1:3000/test

妈妈再也不用担心我写路由了

这样做是有好处也有坏处的。但均衡来讲，好处大于坏处，感谢koa-router的路由机制，让我们可以足够定制的里面的逻辑

如果你觉得自动路由不够用，你自己手动挂载也是可以的，只要你不嫌麻烦
