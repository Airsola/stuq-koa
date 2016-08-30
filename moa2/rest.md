## 没rest怎么行？

曾经rest被吹的玄之又玄，唯恐不谈rest就没有x格，其实rest理解起来还是蛮简单的

最简单的入门是理解crud方法，参考rails的scaffold即可

```
rails g scaffold user name:string password:string
```

打印路由

```
blog git:(master) ✗ rake routes
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
```

是不是代码非常清晰？

既然它是最佳实践，我们也没必要再自己搞一套,只需要因express制宜即可

*  GET    /orders[/]        => order.list()
*  GET    /orders/new       => order.new()
*  GET    /orders/:id       => order.show()
*  GET    /orders/:id/edit  => order.edit()
*  POST   /orders[/]        => order.create()
*  PATCH  /orders/:id       => order.update()
*  DELETE /orders/:id       => order.destroy()


在app/routes/order.js里

```
var express = require('express');
var router = express.Router();

// mount all middlewares in app/middlewares, examples:
// 
// router.route('/')
//  .get($middlewares.check_session_is_expired, $.list)
//  .post($.create);
// 
var $middlewares  = require('mount-middlewares')(__dirname);

// core controller
var $ = require('mount-controllers')(__dirname).orders_controller;


/**
 * Auto generate RESTful url routes.
 *
 * URL routes:
 *
 *  GET    /orders[/]        => order.list()
 *  GET    /orders/new       => order.new()
 *  GET    /orders/:id       => order.show()
 *  GET    /orders/:id/edit  => order.edit()
 *  POST   /orders[/]        => order.create()
 *  PATCH  /orders/:id       => order.update()
 *  DELETE /orders/:id       => order.destroy()
 *
 */

router.get('/new', $.new);  
router.get('/:id/edit', $.edit);

router.route('/')
  .get($.list)
  .post($.create);

router.route('/:id')
  .patch($.update)
  .get($.show)
  .delete($.destroy);

module.exports = router;
```

于是所有东西就都丢到controller里去。

整个世界顿时就清净了

有了Rest，格调也有了。。。。
