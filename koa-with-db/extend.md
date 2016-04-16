# 扩展mongoose模型

## 扩展mongoose模型：statics类方法

```
UserSchema.statics.find_by_openid = function(openid, cb) {
  return this.findOne({
    openid: openid
  }, cb);
};
```

调用

```
User.find_by_openid(openid, cb)
```

## 扩展mongoose模型：methods对象方法

```
UserSchema.methods.is_exist = function(cb) {
  var query;
  query = {
    username: this.username,
    password: this.password
  };
  return this.model('UserModel').findOne(query, cb);
};
```

调用

```
var user = new User({});
user.is_exist(cb)
```

