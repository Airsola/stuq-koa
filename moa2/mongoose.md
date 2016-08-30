## 添加mongoose

```
mkdir models
npm i -S mongoose
npm i -S mongoosedao
```

在路由里增加创建代码

### 配置

配置mongodb链接信息

- config/mongodb.example.js
- db.js

```
cp config/mongodb.example.js config/mongodb.js
```

### 创建models/user.js

```
var mongoose    = require('mongoose');
var Schema      = mongoose.Schema;
var MongooseDao = require('mongoosedao');

var userSchema = new Schema(
    {
      "name":"String",
      "password":"String"
    }
);

var User = mongoose.model('User', userSchema);
<!-- var UserDao = new MongooseDao(User); -->
 
module.exports = User;
```

### 测试user.js

```
import test from 'ava'
import superkoa from 'superkoa'

// 1、引入`mongoose connect`
require('../db');

// 2、引入`User` Model
const User = require('../models/user');

// 3、定义`user` Entity
let user = new User({
  username: 'i5ting',
  password: '0123456789'
});

test.cb('#save()', t => {
  user.save((err, u) => {
    if (err) log(err)
    t.is(u.username, 'i5ting');
    t.end()
  });
});

```

在测试完成后需要在after里删除测试数据，保证测试完整性，自己写吧

### 在路由里增加user创建和api测试

routes/users.js

```
var User = require('../models/user')
var router = require('koa-router')();

router.get('/', function (ctx, next) {
  ctx.body = 'this a users response!';
});

router.post('/register2', function(ctx, next) {
  var name = ctx.request.body.username;
  var password = ctx.request.body.password;
  
  var u = new User({
    "username":name,
    "password":password
  })
  console.log(u)
  return u.save(function (err, user) {
    console.log(err)
    console.log(user)
    if (err) {
      return ctx.body = {'info': 'register failed with err'};
    }
      
    return ctx.body = {'info': 'register sucess'};
  });
});

module.exports = router;

```

test/user_api.js

```
import test from 'ava'
import superkoa from 'superkoa'

var req = superkoa(require('path').resolve(__dirname, '../app'))
  
test.cb("POST /users/register", t => {
  req
    .post('/users/register2')
    .field('username', 'stuq')
    .field('password', '123456')
    .set('Accept', 'application/json')
    .expect('Content-Type', /json/)
    .expect(200, function (err, res) {
      t.ifError(err)

      t.is(res.body.info, 'register sucess', 'res.text == Hello Koa')
      t.end()
    });
});
```

测试

```
ava -v test/user_api.js
```