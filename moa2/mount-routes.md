## 自动挂载路由


```
npm i -S mount-routes
```

app.js里修改如下

修改1

```
var routes = require('./routes/index');
var users = require('./routes/users');
```

改为
```
var mount = require('mount-routes');
```

修改2

```
app.use('/', routes);
app.use('/users', users);
```

改为

```
mount_routes(app,  __dirname + '/routes', true);
```

然后

```
npm start
```

在路由里复制users.js为test.js

测试

http://127.0.0.1:3000/test

妈妈再也不用担心我写路由了

这样做是有好处也有坏处的。但均衡来讲，好处大于坏处，感谢express的路由机制，让我们可以足够定制的里面的逻辑

如果你觉得自动路由不够用，你自己手动挂载也是可以的，只要你不嫌麻烦
