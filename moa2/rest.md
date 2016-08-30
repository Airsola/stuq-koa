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

既然它是最佳实践，我们也没必要再自己搞一套,只需要因express/koa制宜即可

 *  GET    /locations[/]        => location.list()
 *  GET    /locations/new       => location.new()
 *  GET    /locations/:id       => location.show()
 *  GET    /locations/:id/edit  => location.edit()
 *  POST   /locations[/]        => location.create()
 *  PATCH  /locations/:id       => location.update()
 *  DELETE /locations/:id       => location.destroy()


在app/routes/locations.js里

```
"use strict";

var router = require('koa-router')();
const co = require('co');

var $middlewares  = require('mount-middlewares')(__dirname);

// core controller
var $ = require('mount-controllers')(__dirname).locations_controller;

/**
 * Auto generate RESTful url routes.
 *
 * URL routes:
 *
 *  GET    /locations[/]        => location.list()
 *  GET    /locations/new       => location.new()
 *  GET    /locations/:id       => location.show()
 *  GET    /locations/:id/edit  => location.edit()
 *  POST   /locations[/]        => location.create()
 *  PATCH  /locations/:id       => location.update()
 *  DELETE /locations/:id       => location.destroy()
 *
 */

router.get('/new', $.new); 
 
router.get('/:id/edit', $.edit);

router.get('/', $.list);

router.post('/', $.create);

router.get('/:id', $.show);

router.patch('/:id', $.update);

router.delete('/:id', $.destroy);

// -- custom routes




module.exports = router;

```

于是所有东西就都丢到controller里去。

整个世界顿时就清净了

有了Rest，格调也有了。。。。
