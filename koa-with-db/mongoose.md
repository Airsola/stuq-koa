# mongoose

mongoose是Node.js操作mongodb的半orm库

MongoDB object modeling designed to work in an asynchronous environment. 

- http://mongoosejs.com
- https://github.com/Automattic/mongoose

## mongoose用法

```
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

var Cat = mongoose.model('Cat', { name: String });

var kitty = new Cat({ name: 'Zildjian' });
kitty.save(function (err) {
  if (err) // ...
  console.log('meow');
});
```

mongooseconfig

## CRUD（增删改查）

- save
- find | findOne
- update
- remove

```
Kitten.find({ name: /^Fluff/ }, callback)
Comment.remove({ title: 'baby born from alien father' }, callback);
MyModel.update({ age: { $gt: 18 } }, { oldEnough: true }, fn);
```

## MONGOOSEDAO

模型

```
var TopModel = mongoose.model('TopModel', TopSchema); 
var TopModelDao = new MongooseDao(TopModel);
module.exports = TopModelDao;
```


用法

```
require('./db');

var User = require('./User');

User.create({"username":"sss","password":"password"},function(err, user){
  console.log(user);
});

User.delete({"username":"sss"},function(err, user){
  console.log(user);
});
```


https://github.com/moajs/mongoosedao


