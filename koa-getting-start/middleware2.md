# 中间件原理

## 你好奇为什么是`1342`么？

### 从generator说起

koa-getting-start/middleware/core/a.js

```
function * a(){
  console.log('第1个中间件before 1')
  yield *b()
  console.log('第1个中间件after 2')
}

function * b(){
  console.log('  业务逻辑处理')
}

function *hello() {
  yield *a()
}

var it1 = hello();
console.log(it1.next());             // { value: 1, done: false }
```

### 用co简化一下代码

koa-getting-start/middleware/core/b.js

```
var co = require('co')

function * a(){
  console.log('第1个中间件before 1')
  yield *b()
  console.log('第1个中间件after 2')
}

function * b(){
  console.log('  业务逻辑处理')
}

co(function* () {
  yield *a()
})

```

### 看一下具体的1342

koa-getting-start/middleware/core/c.js

```
var co = require('co')

function * a(){
  console.log('第1个中间件before 1')
  yield *b()
  console.log('第1个中间件after 2')
}

function * b(){
  console.log('  第2个中间件before 3')
  yield *c()
  console.log('  第2个中间件after 4')
}

function * c(){
  console.log('    业务逻辑处理')
}

co(function* () {
  yield *a()
})

```

这看起来不太像中间件啊，next呢？

### 中间件写法

koa-getting-start/middleware/core/d.js

```
var co = require('co');
var compose = require('koa-compose')

function * a(next){
  console.log('第1个中间件before 1')
  yield next
  console.log('第1个中间件after 2')
}

function * b(next){
  console.log('  第2个中间件before 3')
  yield next;
  console.log('  第2个中间件after 4')
}

function * c(next){
  console.log('    业务逻辑处理')
}

var stack = [a, b, c];

co(compose(stack))

```

### 总结

通过上面的3个demo，相信大家能够理解generator的yield转让处理权的。通过generator嵌套next，实现这种回溯功能。

## 探索中间件原理

之前讲了中间件各种用法，原理，以至于我们还挖出了1342问题，那么你好奇Koa里到底是怎么实现中间件机制的吗？

本小节将给出精简Koa中间件机制，以便读者更容易读懂Koa源码

### v1

koa-getting-start/middleware/v1/app.js

```
var co = require('co');
var debug = require('debug')('v0')

module.exports = {
  middleware :[],
  use: function (fn) {
    debug('use:' + fn);

    this.middleware.push(fn);
    return this;
  },
  callback: function (cb) {
    const fn = this.compose(this.middleware);
    debug('callback compose fn = ' + fn)

    co(fn).then(cb, function (err) {
      console.error(err.stack);
    });
  },
  compose: function (middleware) {
    debug('compose=' + middleware)
    return function *(next){
      if (!next) {
        next = function *(){}
      }
      
      var i = middleware.length;

      while (i--) {
        debug('compose middleware[' + i + ']=: ' + middleware[i])
        // next = co.wrap(middleware[i]).call(this);
        next = middleware[i].call(this, next);

        debug(next)
      }

      return yield *next;
    }
  } 
}

```

测试代码

```
var app = require('./app')

app.use(function *(next){
  console.log(1)
  yield next;
  console.log(2)
})

app.use(function *(next){
  console.log(3)
  yield next;
  console.log(4)
})

app.callback();
// 1
// 3
// 4
// 2
```

v1的代码里只依赖co，整体来说是非常精简和好理解的，下面我们一一解读


1）中间件是由数组保存的

```
middleware :[],
```

2）app.use使用中间件

```
  use: function (fn) {
    debug('use:' + fn);

    this.middleware.push(fn);
    return this;
  },
```

- 通过push方法给数组增加中间件
- 返回this，是简单的链式写法，以便可以无限的`app.use(fn).use(fn2)`

3）核心入口

先看测试里代码

```
app.callback();
// 1
// 3
// 4
// 2
```

知道到callback函数，打印出1342，即callback函数是入口，处理了所有中间件调用。

代码如下

```
  callback: function (cb) {
    const fn = this.compose(this.middleware);
    debug('callback compose fn = ' + fn)

    co(fn).then(cb, function (err) {
      console.error(err.stack);
    });
  },
```

解读

- 通过this.compose把this.middleware转变一个名为fn的generator函数
- 通过co来执行fn
- co的返回值是Promise对象，所以后面的then接了2个参数，cb是成功的回调，后面的匿名函数是用于处理发生异常的。

这里的逻辑比较简单，就是co去执行fn获得最终结果。但问题是this.middleware是基于generator函数的数组，怎么把它转成generator呢？这其实就是this.compose做的事儿。

4）核心变化compose

```
  compose: function (middleware) {
    return function *(next){
      if (!next) {
        next = function *(){}
      }
      
      var i = middleware.length;

      while (i--) {
        next = middleware[i].call(this, next);
      }

      return yield *next;
    }
  } 
```

其实就是compose([f1, f2, ..., fn])转化为fn(...f2(f1(noop())))，最终的返回值是一个generator function。

4.0）高阶函数

> 高阶函数只是将函数作为参数或返回值的函数

4.1）i--

i--之后的i = i - 1，也就是，当前的next和

- middleware[i]是前一个中间件。

4.2）call

先来看一下call

```
function add(a,b){  
  console.log(a+b)
}

function sub(a,b){  
  console.log(a-b)
}

add.call(sub, 3, 1)


// 这个例子中的意思就是用 add 来替换 sub，
// add.call(sub,3,1) == add(3,1) ，
// 所以运行结果为：console.log(4); 
// 注意：js 中的函数其实是对象，函数名是对 Function对象的引用。
```

那么

```
next = middleware[i].call(this, next);
```

4.3) yield *


还记得

koa-getting-start/middleware/core/b.js

```
var co = require('co')

function * a(){
  console.log('第1个中间件before 1')
  yield *b()
  console.log('第1个中间件after 2')
}

function * b(){
  console.log('  业务逻辑处理')
}

co(function* () {
  yield *a()
})

```

这段代码相当于

```
function * a(){
  console.log('第1个中间件before 1')
    // yield *b()
    console.log('  业务逻辑处理')
  console.log('第1个中间件after 2')
}

```

亦即

```
a(b())
```

通过yield*递归执行generator，是非常有用的手段。


4.4) 总结

- 如果next为空，`next = function *(){}`
- i--之后，middleware[i]就是之前的中间件，即f(n-1)
- 通过call，实际转成之前中间件为next
- 通过while i--规约所有中间件为一个generator
- 通过yield *递归执行generator

compose返回的是generator，那么它就需要一个generator执行器，也就是上文出现的co，与之搭配是非常合理的。

### v2

koa-getting-start/middleware/v2/app.js

```
var co = require('co');
var debug = require('debug')('v1')
const convert = require('koa-convert')

module.exports = {
  middleware :[],
  use: function (fn) {
    this.middleware.push(fn);
    return this;
  },
  callback: function () {
    const fn = convert.compose(this.middleware);
    debug('callback compose fn = ' + fn)
    
    var ctx = {
      
    }

    fn(ctx).then(function(){

    })
  }
}

```

测试

koa-getting-start/middleware/v2/test.js

```
var app = require('./app')

app.use((ctx, next) =>{
  console.log(1)
  // before
  return next().then(() => {
    // after
    console.log(2)
  })
})

app.use(function *(next){
  console.log(3)
  yield next;
  console.log(4)
})

// console.log(app.middleware)
app.callback();
// 1
// 3
// 4
// 2

```

### v2补充

https://github.com/koajs/convert/blob/master/test.js

```
describe('convert.compose()', () => {
  it('should work', () => {
    let call = []
    let context = {}
    let _context
    let mw = convert.compose([
      function * name (next) {
        call.push(1)
        yield next
        call.push(11)
      },
      (ctx, next) => {
        call.push(2)
        return next().then(() => {
          call.push(10)
        })
      },
      function * (next) {
        call.push(3)
        yield* next
        call.push(9)
      },
      co.wrap(function * (ctx, next) {
        call.push(4)
        yield next()
        call.push(8)
      }),
      function * (next) {
        try {
          call.push(5)
          yield next
        } catch (e) {
          call.push(7)
        }
      },
      (ctx, next) => {
        _context = ctx
        call.push(6)
        throw new Error()
      }
    ])

    return mw(context).then(() => {
      assert.equal(context, _context)
      assert.deepEqual(call, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11])
    })
  })
  ```
  
## v3

koa-getting-start/middleware/v3/app.js

```
const co = require('co');
const debug = require('debug')('v1')
const compose = require('koa-compose')

module.exports = {
  middleware :[],
  use: function (fn) {
    this.middleware.push(fn);
    return this;
  },
  callback: function () {
    const fn = compose(this.middleware);
    debug('callback compose fn = ' + fn)
    
    var ctx = {
      
    }

    fn(ctx).then(function(){

    })
  }
}

```

koa-getting-start/middleware/v3/test.js

```
'use strict'

const co = require('co');
const debug = require('debug')('v1')

module.exports = {
  middleware :[],
  use: function (fn) {
    this.middleware.push(fn);
    return this;
  },
  compose: function (middleware) {
    return function (context, next) {
      // last called middleware #
      let index = -1
      return dispatch(0)
      function dispatch (i) {
        if (i <= index) return Promise.reject(new Error('next() called multiple times'))
        index = i
        const fn = middleware[i] || next
        if (!fn) return Promise.resolve()
        try {
          return Promise.resolve(fn(context, function next () {
            return dispatch(i + 1)
          }))
        } catch (err) {
          return Promise.reject(err)
        }
      }
    }
  },
  callback: function () {
    const fn = this.compose(this.middleware);
    debug('callback compose fn = ' + fn)
    
    var ctx = {
      
    }

    fn(ctx).then(function(){

    })
  }
}

```


如果此时把其中的一个中间件变成koa 1.x支持的v3/test2.js

```
var app = require('./app')
var co = require('co')

app.use((ctx, next) => {
  console.log(1)
  // before
  return next().then(() => {
    // after
    console.log(2)
  })
})

app.use(function *(next) {
  console.log(3)
  yield next;
  console.log(4)
})


// app.use(function *(next) {
//   console.log(3)
//   yield next;
//   console.log(4)
// })

// console.log(app.middleware)
app.callback();
//1
//2

```

## 回形针的实现 koa compose

```
 compose: function (middleware) {
  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      const fn = middleware[i] || next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, function next () {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

其实就是compose([f1, f2, ..., fn])转化为f1(...f(n-1)(fn(noop())))，最终的返回值是一个function (context, next) {}。


1）最终的返回值是function (context, next) {}，递归函数。


2）递归，从dispatch(0)开始，

i = 0

```
fn == middleware[i]
```

然后

```
fn(context, function next () {
  return dispatch(i + 1)
})
```

此处是执行第一个中间件，如果成功的化，就继续执行第二个中间件。其他依次类推，直至执行完所有的中间件。


结合这个中间件

```
app.use((ctx, next) => {
  console.log(1)
  // before
  return next().then(() => {
    // after
    console.log(2)
  })
})
```

实际上，

``` 
var value = fn(context, function next () {
  return dispatch(i + 1)
})

return Promise.resolve(value)
```

我们知道Promise.resolve()返回的是Promise对象，它可以thenable的。

举个多个中间件的例子

```
app.use((ctx, next) => {
  console.log(1)
  // before
  return next().then(() => {
    // after
    console.log(2)
  })
})

app.use((ctx, next) => {
  console.log(3)
  // before
  return next().then(() => {
    // after
    console.log(4)
  })
})

```


实际上

``` 
var value = fn(context, function next () {
  var dispatch_1_value = fn(context, function next () {
    return dispatch(2)
  })
  return Promise.resolve(dispatch_1_value)
})

return Promise.resolve(value)
```

如果dispatch_1_value有then，那么肯定有些执行，即打印出4，然后执行

```
return Promise.resolve(value)
```

此时，还有个then，即打印出2.


大致可以这样理解

```
function(ctx, next){
  console.log(1)
  reutrn Promise.resolve(
    console.log('3')
    return Promise.resolve(
    
    )
    .then(function(){
      console.log('4')
    })
  )
  .then(function(){
    console.log(2)
  })
}
```

至此，1342有完美的解决了。


## v3 app2

此时需要修改app为app2

```
'use strict'

const co = require('co');
const debug = require('debug')('v1')
const convert = require('koa-convert')
const isGeneratorFunction = require('is-generator-function');

module.exports = {
  middleware :[],
  use: function (fn) {
    if (isGeneratorFunction(fn)) {
      console.log('Support for generators will been removed in v3. ' +
                'See the documentation for examples of how to convert old middleware ' +
                'https://github.com/koajs/koa/tree/v2.x#old-signature-middleware-v1x');
      fn = convert(fn);
    }
    this.middleware.push(fn);
    return this;
  },
  compose: function (middleware) {
    return function (context, next) {
      // last called middleware #
      let index = -1
      return dispatch(0)
      function dispatch (i) {
        if (i <= index) return Promise.reject(new Error('next() called multiple times'))
        index = i
        const fn = middleware[i] || next
        if (!fn) return Promise.resolve()
        try {
          return Promise.resolve(fn(context, function next () {
            return dispatch(i + 1)
          }))
        } catch (err) {
          return Promise.reject(err)
        }
      }
    }
  },
  callback: function () {
    const fn = this.compose(this.middleware);
    debug('callback compose fn = ' + fn)
    
    var ctx = {
      
    }

    fn(ctx).then(function(){

    })
  }
}

```

主要变化是

```
  use: function (fn) {
    if (isGeneratorFunction(fn)) {
      console.log('Support for generators will been removed in v3. ' +
                'See the documentation for examples of how to convert old middleware ' +
                'https://github.com/koajs/koa/tree/v2.x#old-signature-middleware-v1x');
      fn = convert(fn);
    }
    this.middleware.push(fn);
    return this;
  },
```

中的

```
    if (isGeneratorFunction(fn)) {
      console.log('Support for generators will been removed in v3. ' +
                'See the documentation for examples of how to convert old middleware ' +
                'https://github.com/koajs/koa/tree/v2.x#old-signature-middleware-v1x');
      fn = convert(fn);
    }
```

如果fn是GeneratorFunction（koa 1.x中间件），需要通过convert转换一下，转成类似的

```
  const converted = function (ctx, next) {
    return co.call(ctx, mw.call(ctx, createGenerator(next)))
  }
```

这样就保证了，所有的compose里的


## 从koa 1.x到koa 2.x的演变

有人问，为什么你的例子都是koa 1.x的，而你推荐使用的koa 2.x？不要着急，听我慢慢道来。


koa 1.x

> 其实就是compose([f1, f2, ..., fn])转化为fn(...f2(f1(noop())))，最终的返回值是一个generator function。


koa 2.x

> 其实就是compose([f1, f2, ..., fn])转化为f1(...f(n-1)(fn(noop())))，最终的返回值是一个function (context, next) {}。

如果是`function *(next){}`也会被转换成成`function (context, next) {}`，这就是convert做的向后1.x兼容。

- function (context, next) {} 是commonfunction，因为最终返回值也是它，所以称为common也不过分
- co.wrap(function *(ctx, next){})其实是commonfunction一样的，co的wrap就是执行有参数的generator，而参数也是ctx和next，所以也是天生支持的。

那么剩下的就是async函数了。

```
// 定义
async function middleware(context, next) {
  await next()
}

// 调用
middleware(context, next);
```

从这段可知，async函数定义和普通函数定义执行都是一样的（除了需要es7-stage-3特性编译），所以无缝集成，唯一要说明的就是next，上文讲next实际是Promise对象，在想await关键字，是不是有点恍然大悟了？

绝配啊。


## koa源码说明

### 核心源码

- koa 1.x用的是compose 2.4+，注意无convert
- koa 2.x用的是compose 3.1+，使用convert把generatorfunction转成commonfunction，以便后续的compose。

### 为啥2.x要多出个ctx？

[@jonathanong](https://github.com/koajs/koa/issues/615)

这样变化的最主要的原因是，在你写koa apps 时使用async箭头函数的时候：

```
app.use(async (ctx, next) => {
  await next()
})
```
  
这种情况下，使用this是万万不可能的。

因为 Arrow Function是 Lexical scoping（定义时绑定）, this指向定义Arrow Function时外围, 而不是运行时的对象。


### 引用koa 1.x和2.x的差异根源

1.x

```
var koa = require('koa');
var app = koa();
```

2.x

```
const Koa = require('koa');
const app = new Koa();

```

源码

1.x

```
/**
 * Application prototype.
 */

var app = Application.prototype;

/**
 * Expose `Application`.
 */

module.exports = Application;

/**
 * Initialize a new `Application`.
 *
 * @api public
 */

function Application() {
  if (!(this instanceof Application)) return new Application;
  this.env = process.env.NODE_ENV || 'development';
  this.subdomainOffset = 2;
  this.middleware = [];
  this.proxy = false;
  this.context = Object.create(context);
  this.request = Object.create(request);
  this.response = Object.create(response);
}
```

2.x

```

/**
 * Expose `Application` class.
 * Inherits from `Emitter.prototype`.
 */

module.exports = class Application extends Emitter {

  /**
   * Initialize a new `Application`.
   *
   * @api public
   */

  constructor() {
    super();

    this.proxy = false;
    this.middleware = [];
    this.subdomainOffset = 2;
    this.env = process.env.NODE_ENV || 'development';
    this.context = Object.create(context);
    this.request = Object.create(request);
    this.response = Object.create(response);
  }
}
```

很明显，1.x是函数，而2.x是类，需要new来实例化。

整个Koa2.x里只有application做了类化，其他的还是保持之前的风格，大概还没有到必须修改的时候吧。


## 常用中间件

根据中间件在整个http处理流程的位置，将中间件大致分为3类：

1. Pre-Request 通常用来改写request的原始数据
2. Request/Response 大部分中间件都在这里，功能各异
3. Post-Response 全局异常处理，改写response数据等

## 练习koa用法，集成以下中间件

```
    "koa": "^2.0.0",
    "koa-compress": "^2.0.0",
    "koa-conditional-get": "^2.0.0",
    "koa-etag": "^3.0.0",
    "koa-favicon": "^2.0.0",
    "koa-static": "^3.0.0",
```

app.js

```
var serve = require('koa-static');
var Koa = require('koa');
var app = new Koa();

var favicon = require('koa-favicon');
var compress = require('koa-compress')
var conditional = require('koa-conditional-get');
var etag = require('koa-etag');

app.use(compress({
  filter: function (content_type) {
    return /text/i.test(content_type)
  },
  threshold: 2048,
  flush: require('zlib').Z_SYNC_FLUSH
}))


app.use(favicon(__dirname + '/public/favicon.ico'));

// etag works together with conditional-get
app.use(conditional());
app.use(etag());


// or use absolute paths
app.use(serve(__dirname + '/dist'));

app.listen(9090);

console.log('listening on port 9090');
```
