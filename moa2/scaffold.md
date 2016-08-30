## 脚手架scaffold

rails在2005年横空出世，靠的就是10分钟完成一个blog的当时的创举，其实scaffold居功甚伟

scaffold说白了就是生成器

### 模板引擎的原理
我们来回想模板引擎的原理

- 数据
- 模板
- 然后模板+数据编译，生成html页面

那么，如果我要生成文件呢？比如controller.js文件呢？

思路其实也是一样的

举个例子

http://handlebarsjs.com/

步骤1：定义模板

```
<div class="entry">
  <h1>{{title}}</h1>
  <div class="body">
    {{body}}
  </div>
</div>
```

步骤2：编译模板

```
var source   = $("#entry-template").html();
var template = Handlebars.compile(source);

var context = {title: "My New Post", body: "This is my first post!"};
var html    = template(context);
```

步骤3：然后写入到文件

```
fs.writeFile(dest_file_path, content , function (err) {
  if (err) throw err;
  log('It\'s saved!');
});
```

其实也模板引擎就差最后一步，写入文件。

### tpl_apply

但写每次都这样文件读写，还是挺累的，索性就写一个npm吧

```
var tpl = require('tpl_apply');


var source = process.cwd() + '/tpl.js'
var dest = process.cwd() + '/test/tpl.generate.js'


tpl.tpl_apply(source, {
    title: "My New Post", body: "This is my first post!"
}, dest);
```

用法很简单，3个参数

- source是模板
- 中间的对象是数据，要和模板编译的数据
- dest是生成的文件名称

是不是很清晰呢？

### 实现一个scaffold

解决了基本的文件生成问题，下面我们就要分析一下scaffold的实现可能

参考rails的scaffold即可

```
rails g scaffold user name:string password:string
```

说明

- user是模型名称
- name和password是字段
- string是自定对应的类型

是不是很简单？

ok，我们来看一下model

```
var mongoose    = require('mongoose');
var Schema      = mongoose.Schema;
var MongooseDao = require('mongoosedao');

var orderSchema = new Schema(
    {"name":"String","password":"String"}
);

var Order = mongoose.model('Order', orderSchema);
var OrderDao = new MongooseDao(Order);
 
module.exports = OrderDao;
```

这里面可变的就模型名称的字段和类型，是吧？

于是模型模板定义如下

```
/**
 * Created by alfred on {{created_at}}.
 */

var mongoose    = require('mongoose');
var Schema      = mongoose.Schema;
var MongooseDao = require('mongoosedao');

var {{model}}Schema = new Schema(
    {{{mongoose_attrs}}}
);

var {{entity}} = mongoose.model('{{entity}}', {{model}}Schema);
var {{entity}}Dao = new MongooseDao({{entity}});
 
module.exports = {{entity}}Dao;
```

剩下的就是从cli里获得数据，然后编译就好了。

cli里获取参数就是字符串分割，没啥难度

其他依此类推

- controllers
- routes
- views

https://github.com/moajs/moa/tree/dev/tpl


我们来回顾一下，为什么能如此简单的完整生成器呢？

- 我们的代码结构和目录结构已经固定死了
- 模板引擎：数据+模板->生成模板

简单吧？


### 学习资料

- [handlebars玩家指南](https://cnodejs.org/topic/56a2e8b1cd415452622eed2d)
