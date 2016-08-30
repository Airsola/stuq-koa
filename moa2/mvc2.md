## 继续优化mvc结构

好吧，上一小节，给出了如何简单的把目录下的文件挂载到某个对象上，并发布npm上

照着这个思路，[我们](https://github.com/moajs)造了几个简单的库，用于挂载某个目录里的文件

- mount-controllers
- mount-models
- mount-middlewares


### 1)mount-controllers

```
var $ = require('mount-controllers')(__dirname).orders_controller;

router.get('/new', $.new); 
```


### 2)mount-models

```
var $models = require('mount-models')(__dirname);

var Order = $models.order;

exports.list = function (req, res, next) {
  console.log(req.method + ' /orders => list, query: ' + JSON.stringify(req.query));
  
  Order.getAll(function(err, orders){
    console.log(orders);
    res.render('orders/index', {
      orders : orders
    })
  });
};
```

### 3)mount-middlewares

一般会在路由或路由下面的api里使用

```
var $ = require('mount-controllers')(__dirname).orders_controller;

var $middlewares  = require('mount-middlewares')(__dirname);

// route define
router.get('/', $middlewares.check_api_token, $.api.list);
```

做这些的目的是为了让大家更好的去分离代码，其实还有一个目的，结构化的代码才能用于写生成器的。下面会讲

### 集成

简单的集成一下

```
npm i -S mount-controllers
npm i -S mount-models
npm i -S mount-middlewares
```


```
moag order name:string password:string
```

然后npm start


发现缺少中间件

- jsonwebtoken
- msgpack5

```
npm i -S jsonwebtoken
npm i -S msgpack5
```

此时访问http://127.0.0.1:3000/orders/

正常是应该有数据的

可是没有，这是为什么呢？找代码

把views下面的error和layout放进去就好了

发现没有连接数据，于是在app.js里

```
require('./db')
```

经测试delete不好使，原因是少东西
