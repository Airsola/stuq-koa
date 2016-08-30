## 添加mvc目录

很多人是这样的做法：

```
$ tree . -L 1 -d
.
├── actions
├── config
├── cron_later
├── doc
├── middleware
├── migrate
├── models
├── node_modules
├── public
├── queues
├── routes
├── test
├── tmp
├── uploads
└── views
```
说明

- 继承express-generator的结构（routes和views）
- actions即controller控制器层
- models即模型层
- config是配置项目录
- middleware中间件层
- migrate是我们处理数据的，跟rails的migrate不完全一样
- test是测试目录
- queues是队列
- cron_later是调度
- doc文档
- uploads是上传自动创建的目录

这阶段仅仅是用，算是基于express抽象了一点业务、配置相关的东西而已，目录多了依然蛋疼

所以还是rails的更清晰一些

```
rails new blog --skip-bundle


git:(master) ✗ tree blog -L 2 -d
blog
├── app
│   ├── assets
│   ├── controllers
│   ├── helpers
│   ├── mailers
│   ├── models
│   └── views
├── bin
├── config
│   ├── environments
│   ├── initializers
│   └── locales
├── db
├── lib
│   ├── assets
│   └── tasks
├── log
├── public
├── test
│   ├── controllers
│   ├── fixtures
│   ├── helpers
│   ├── integration
│   ├── mailers
│   └── models
├── tmp
│   └── cache
└── vendor
    └── assets

29 directories
```

so，我们也来建一个吧


这些就是基本的mvc目录，但是express有其自己的特点，比如路由和中间件等，想想还是放app项目更好一些

```
mkdir -p app/controllers
mkdir -p app/models
mkdir -p app/middlewares
```

而views和routes是已有目录，只需要移动到app下即可

```
mv views app/views
mv routes app/routes
```

我们记得，mount-routes里修改了app里的路由路径，改一下就好

```
mount_routes(app,  __dirname + '/routes', true);
```

改为

```
mount_routes(app,  __dirname + '/app/routes', true);
```

```
app.set('views', path.join(__dirname, 'views'));
```

改为

```
app.set('views', path.join(__dirname, 'app/views'));
```

至此我们的基本目录是有了的下面

下面我们要考虑职责的事儿了

SOLID（藏头命名）

- 单一职责原则 (Single Responsibility Principle，SRP)
- 开放/封闭原则(Open/Close Principle，OCP)
- 里氏替换原则(Liskov Substitution Principle，LSP)
- 接口分离原则(Interface Segregation Principle，ISP)
- 依赖倒置原则(Dependency Inversion Principle，DIP)

此时的路由里完成各种逻辑，实际上是不太合理，没有遵循mvc原则

理想的做法是我们创建一个model叫Order，它有

- routes/order.js
- controllers/OrderController.js
- models/Order.js
- views/order/*.jade

这是正确的做法

最佳目录结构应该如下

```
 git:(master) ✗ tree app -L 3
app
├── controllers
│   └── orders_controller.js
├── models
│   └── order.js
├── routes
│   ├── api
│   │   └── orders.js
│   └── orders.js
└── views
    └── orders
        ├── edit.jade
        ├── index.jade
        ├── new.jade
        ├── order.jade
        └── show.jade

6 directories, 9 files
```

调用级别是

- 我们在路由里不写任何逻辑代表，只是让他转到controller
- 然后controller调用mode里方法完成业务逻辑，并根据返回时，处理视图渲染
- 视图只根据controller给的内容进行渲染即可
