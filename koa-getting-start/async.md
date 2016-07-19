# 异步流程控制

主要讲3个

- Promise
- Generator/yield
- Async/await

# Promise/a+规范

顺序执行的代码和错误有限的回调方式都是js引擎默认支持的，这部分大家会调用接口，无太多变化，而Promise是对callback的思考，或者说改良方案，目前使用非常普遍，这里详细讲解一下。

## node里的Promise

promise最早是在commonjs社区提出来的，当时提出了很多规范。比较接受的是promise/A规范。后来人们在这个基础上。提出了promise/A+规范，也就是实际上的业内推行的规范。es6也是采用的这种规范。

> The Promise object is used for asynchronous computations. A Promise represents an operation that hasn't completed yet, but is expected in the future.

Promise对象用于异步技术中。Promise意味着一个还没有完成的操作（许愿），但在未来会完成的（实现）。

![](img/promise-impl.png)

在Node.js 0.12里实现9/11，在6.2和7实现100%，中间版本实现了10/11。所以Node.js对Promise的支持是非常好的，0.12之后的绝大部分版本都支持的不错。

Promise 的最大优势是标准，各类异步工具库都认同，未来的 async/await 也基于它，用它封装 API 通用性强，用起来简单。

要想知道node.js有哪些比较好的promise实现，最好的办法就是看一下最知名的bluebird库的[benchmark](http://bluebirdjs.com/docs/benchmarks.html)里比较里哪些。

- async@1.5.0
- babel@5.8.29
- davy@1.0.1
- deferred@0.7.3
- kew@0.7.0
- lie@3.0.1
- neo-async@1.6.0
- optimist@0.6.1
- promise@7.0.4
- q@1.4.1
- rsvp@3.1.0
- streamline@1.0.7
- text-table@0.2.0
- vow@0.4.11
- when@3.7.4


## Promise是什么？

> A promise is an abstraction for asynchronous programming. It’s an object that proxies for the return value or the exception thrown by a function that has to do some asynchronous processing. — Kris Kowal on JSJ

Promise表示一个异步操作的最终结果。与Promise最主要的交互方法是通过将函数传入它的then方法从而获取得Promise最终的值或Promise最终最拒绝（reject）的原因。

- 递归，每个异步操作返回的都是promise对象
- 状态机：三种状态转换，只在promise对象内部可以控制，外部不能改变状态
- 全局异常处理

定义

```
var promise = new Promise(function(resolve, reject) {
  // do a thing, possibly async, then…

  if (/* everything turned out fine */) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
});
```

## 术语

- Promises	Promise规范自身
- promise对象	promise对象指的是 Promise 实例对象
- ES6 Promises	如果想明确表示使用 ECMAScript 6th Edition 的话，可以使用ES6作为前缀（prefix）
- Promises/A+	Promises/A+。 这是ES6 Promises的前身，是一个社区规范，它和 ES6 Promises 有很多共通的内容。
- Thenable	类Promise对象。 拥有名为.then方法的对象。

## hello promise

给出一个最简单的读写文件的api实例，它是error-first风格的典型api

async/promise/hello.js

```
// callbacks
var fs = require("fs");

fs.readFile('./package.json', (err, data) => {
  if (err) throw err;
  console.log(data.toString());
});

```

下面，我们把它变成promise的简单示例

async/promise/hellopromise.js

```
// callbacks to promise
var fs = require("fs");

function hello (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}

hello('./package.json').then(function(data){
  console.log('promise result = ' + data)
}).catch(function(err) {
  console.log(err)
})

```

这二段代码执行效果是一模一样的，唯一的差别是前一种写法是Node.js默认api写法，以回调为主，而后一种写法，通过返回promise对象，在fs.readFile的回调函数，将结果延后处理。

这就是最简单的promise实现


形式

```
new Promise(function(resolve, reject){
  
})
```

参数

- resolve 解决，进入到下一个流程
- reject  拒绝，跳转到捕获异常流程

调用

```
hello('./package.json').then(function(data){

})
```

全局处理异常

```
hello('./package.json').then(function(data){

}).catch(function(err) {

})
```

结论

> Promise核心：将callback里的结果延后到then函数里处理或交给全局异常处理


## 封装api的过程

还是以上面的fs.readFile为例

```
fs.readFile('./package.json', (err, data) => {
  if (err) throw err;
  console.log(data.toString());
});
```

参数处理：除了callback外，其他东西都放到新的函数的参数里

```
function hello (file) {
  ...
}
```

返回值处理：返回Promise实例对象

```
function hello (file) {
  return new Promise(function(resolve, reject){
    ...
  });
}
```

结果处理：通过resolve和reject重塑流程

```
function hello (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}
```

我们知道所有的Node.js都是error-first的callback形式，通过上面的例子，我们可以肯定是所有的Node.js的API都可以这样来处理，只要它们遵守Promise规范即可。

## 每个函数的返回值都是Promise对象

为了简化编程复杂性，每个函数的返回值都是Promise对象，这样的约定可以大大的简化编程的复杂。

它可以理解为是递归的变种思想应用，只要是Promise对象，就可以控制状态，就可以支持then方法，参数还是Promise对象，这样就可以无限个Promise对象链接在一起。

```
// callbacks to promise
var fs = require("fs");

function hello (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}

function world (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}

function log(data){
  return new Promise(function(resolve, reject){
    console.log('promise result = ' + data)
    resolve(data)
  });
}

hello('./package.json').then(log).then(function(){
  return hello('./each.js').then(log)
}).catch(function(err) {
  console.log(err)
})

```

这里可以看出

- `hello`、`world`、`log` 返回单个Promise对象
- `hello('./each.js').then(log)` 返回流程链

无论是单个，还是流程链的返回值都是Promise对象，那么它就是一样的。

## 链式的thenable

每个promose对象都有then方法，也就是说，then方法是定义在原型对象Promise.prototype上的。它的作用是为Promise实例添加状态改变时的回调函数。

一般实现，类似于

```
Promise.prototype.then = function(sucess, fail) {
    this.done(sucess);
    this.fail(fail);
    return this;
};
```

它的返回值是this，这就是为什么then可以链式操作的原因。

then的2个参数

- sucess是fulfilled状态的回调函数
- fail是rejected状态的回调函数

一般都是穿sucess回调函数即可。

## 状态转换

一个Promise必须处在其中之一的状态：pending, fulfilled 或 rejected.

- pending: 初始状态, 非 fulfilled 或 rejected.
- fulfilled: 完成（成功）的操作.
- rejected: 拒绝（失败）的操作.

这里从pending状态可以切换到fulfill状态，也可以从pengding切换到reject状态，这个状态切换不可逆，且fulfilled和reject两个状态之间是不能互相切换的。

一定要注意的是，只有异步操作的结果，才可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。

Promise对象可以理解为一个乐高积木，它对下一个流程，传送状态和具体结果。


![](img/promise.png)

如果是pending状态,则promise：

  - 可以转换到fulfilled或rejected状态。
  
如果是fulfilled状态,则promise：

  - 不能转换成任何其它状态。
  - 必须有一个值，且这个值不能被改变。

如果是rejected状态,则promise可以：

  - 不能转换成任何其它状态。
  - 必须有一个原因，且这个值不能被改变。
  
”值不能被改变”指的是其identity不能被改变，而不是指其成员内容不能被改变。

## reject和resove流程再造

前面讲了，每个函数的返回值都是Promise对象，每个Promise对象都有then方法，这是它可以递归思路的解决办法。

那么问题来了，如何在连续的操作步骤里，完成流程再造呢？这其实才是异步流程控制最核心的问题。

我们知道Promise的使用形式如下：

```
new Promise(function(resolve, reject){
  
})
```

下面仍然使用fs的例子，见reflow.js

`way 1`：简单模式

```
hello('./package.json').then(function(data){
  console.log('way 1:\n')
  return new Promise(function(resolve, reject){
    console.log('promise result = ' + data)
    resolve(data)
  });
}).then(function(data){
  return new Promise(function(resolve, reject){
    resolve('1')
  });
}).then(function(data){
  console.log(data)
  
  return new Promise(function(resolve, reject){
    reject(new Error('reject with custom err'))
  });
}).catch(function(err) {
  console.log(err)
})

```

这是一个常规的例子，就是在then里面的promise对象里，通过resolve将流程进行到下一步，在reject的时候抛出异常。这里面的每一个promise对象里都可以这样做，那么是不是这个操作流程就是可控的了？

`way 2`：嵌套模式

```
hello('./package.json').then(function(data){
  console.log('\n\nway 2:\n')
  return new Promise(function(resolve, reject){
    console.log('promise result = ' + data)
    resolve(data)
  }).then(function(data){
    return new Promise(function(resolve, reject){
      resolve('1')
    });
  }).catch(function(err) {
    console.log(err)
  })
}).then(function(data){
  console.log(data)
  
  return new Promise(function(resolve, reject){
    reject(new Error('reject with custom err'))
  });
}).catch(function(err) {
  console.log(err)
})
```

这里的做法是，把第一个then和第二个then合并到一个流程里。这样做的好处是，这个流程也可以考虑单独处理异常。为了某些粒度更新的异步处理，是非常有好处的。


`way 3`：嵌套模式的refact清晰版

```
var step1 = function(data){
  console.log('\n\nway 3:\n')
  return new Promise(function(resolve, reject){
    console.log('promise result = ' + data)
    resolve(data)
  }).then(function(data){
    return new Promise(function(resolve, reject){
      resolve('1')
    });
  }).catch(function(err) {
    console.log(err)
  })
}

var step2 = function(data){
  console.log(data)
  
  return new Promise(function(resolve, reject){
    reject(new Error('reject with custom err'))
  });
}

hello('./package.json').then(step1).then(step2).catch(function(err) {
  console.log(err)
})
```

把每个独立的操作抽成函数，然后函数的返回值是Promise对象，这样就可以在真正的流程链里随意组织了。

它们就好比是积木一样，可以让逻辑更清楚，让代码更具可读性和可维护性。如果再极端点，每个操作都放到独立文件里，变成模块，是不是更爽呢？  


`way 4`：final版，把每个独立的操作放到独立文件里，变成模块

原理: 使用`require-directory`

根据commonjs规范，require只能引用某一个文件，当一个文件夹里有很多文件，每一个都去require是很麻烦的，`require-directory`就是一个便捷模块，可以把某个文件夹内的多个文件挂载到一个对象。

原理，递归遍历文件，读取具体文件，如果是遵循commonjs规范的模块，就挂载在它的返回值对象上。

比如reflow/tasks/index.js

```
var requireDirectory = require('require-directory');
module.exports = requireDirectory(module);
```

这样`reflow/tasks`下的所有遵循commonjs规范的模块都可以挂载

reflow/tasks/hello.js

```
var fs = require("fs");

module.exports = function hello (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}
```

这其实和之前的定义是一模一样的，唯一差别就是变成了模块，使用了module.exports来导出。

其他的step1和step2以此类推，下面我们卡一下具体调用的代码

```
var tasks = require('./tasks')

tasks.hello('./package.json').then(tasks.step1).then(tasks.step2).catch(function(err) {
  console.log(err)
})
```

给出具体的流程图

![](img/promise-flow.png)


首先`require('./tasks')`获得tasks目录下的所有操作任务定义，然后在下面的Promise流程里处理，可以看出定义和实现分离，让代码有更好的可读性。

如果，这时我们恰好需要调整step1和step2的顺序，是不是非常的简单？

```
var tasks = require('./tasks')

tasks.hello('./package.json').then(tasks.step2).then(tasks.step1).catch(function(err) {
  console.log(err)
})
```

更多好处，自行体会吧，这里就不做更多解释了。

## 错误处理

常用的处理方式是全局处理，即所有的异步操作都由一个catch来处理

```
promise.then(function(result) {
  console.log('Got data!', result);
}).catch(function(error) {
  console.log('Error occurred!', error);
});
```

当然，then方法的第二个参数也是可以的

```
promise.then(function(result) {
  console.log('Got data!', result);
}).then(undefined, function(error) {
  console.log('Error occurred!', error);
});
```

如果有多个then配对的reject函数呢？是不是可以更加灵活？这其实就要取决于你的业务复杂程度里。

错误处理最简单的办法是在promise里使用try/catch的语句。在try/catch块中，它可能去捕获异常，并显示处理它：（TODO: 重写个更简单例子）

```
try {
  throw new Error('never will know this happened')
} catch (e) {}
```

在promises里可以这样写

```
readFile()
  .then(function (data) {
    throw new Error('never will know this happened')
  })
```

为了打印errors，这里以简单的.then(null, onRejected)语句为例

```
readFile()
  .then(function (data) {
    throw new Error('now I know this happened')
  })
  .then(null, console.error)
```

类库包括一些暴露error的其他选项。比如Q就提供了done方法，可以再次跑出error异常的。


链式写法很方便，可以随意组合，

api/catch.js

```
var p1 = new Promise(function(resolve, reject) {
  resolve('Success');
});

p1.then(function(value) {
  console.log(value); // "Success!"
  return Promise.reject('oh, no!');
}).catch(function(e) {
  console.log(e); // "oh, no!"
  // return Promise.reject('oh, no! 2');
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});
```

执行

```
$ node api/catch.js
Success
oh, no!
after a catch the chain is restored
```

api/catch2.js
 
```
var p1 = new Promise(function(resolve, reject) {
  resolve('Success');
});

p1.then(function(value) {
  console.log(value); // "Success!"
  return Promise.reject('oh, no!');
}).catch(function(e) {
  console.log(e); // "oh, no!"
  return Promise.reject('oh, no! 2');
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});
```

执行

```
$ node api/catch2.js
Success
oh, no!
Not fired due to the catch
```

## Node.js的promise库

Promise扩展类库除了实现了Promise中定义的规范之外，还增加了自己独自定义的功能。

按字母排序

| package | repo | alias
| ------- | ---- | -----
| [bluebird](https://www.npmjs.com/package/bluebird) | [petkaantonov/bluebird](https://github.com/petkaantonov/bluebird) | bb
| [es6-promise](https://www.npmjs.com/package/es6-promise) | [jakearchibald/es6-promise](https://github.com/jakearchibald/es6-promise) |
| [es6-promise-polyfill](https://www.npmjs.com/package/es6-promise-polyfill) [^1] | [lahmatiy/es6-promise-polyfill](https://github.com/lahmatiy/es6-promise-polyfill) |
| [es6-promises](https://www.npmjs.com/package/es6-promises) | [Octane/Promise](https://github.com/Octane/Promise) |
| [lie](https://www.npmjs.com/package/lie) | [calvinmetcalf/lie](https://github.com/calvinmetcalf/lie) |
| [native-promise-only](https://www.npmjs.com/package/native-promise-only) | [getify/native-promise-only](https://github.com/getify/native-promise-only) | npo
| [promiscuous](https://www.npmjs.com/package/promiscuous) | [RubenVerborgh/promiscuous](https://github.com/RubenVerborgh/promiscuous) |
| [promise](https://www.npmjs.com/package/promise) | [then/promise](https://github.com/then/promise) | then
| [promiz](https://www.npmjs.com/package/promiz) | [Zolmeister/promiz](https://github.com/Zolmeister/promiz) |
| [q](https://www.npmjs.com/package/q) | [kriskowal/q](https://github.com/kriskowal/q) |
| [rsvp](https://www.npmjs.com/package/rsvp) | [tildeio/rsvp.js](https://github.com/tildeio/rsvp.js) |
| [vow](https://www.npmjs.com/package/vow) | [dfilatov/vow](https://github.com/dfilatov/vow) |
| [when](https://www.npmjs.com/package/when) | [cujojs/when](https://github.com/cujojs/when) | w

[^1]: based on es6-promise, so excluded from the registery by default


Promise扩展类库数量非常的多，我们只介绍其中两个比较有名的类库。

- [kriskowal/q](https://github.com/kriskowal/q)

类库 Q 实现了 Promises 和 Deferreds 等规范。 它自2009年开始开发，还提供了面向Node.js的文件IO API Q-IO 等， 是一个在很多场景下都能用得到的类库。

- [petkaantonov/bluebird](https://github.com/petkaantonov/bluebird)

这个类库除了兼容 Promise 规范之外，还扩展了取消promise对象的运行，取得promise的运行进度，以及错误处理的扩展检测等非常丰富的功能，此外它在实现上还在性能问题下了很大的功夫。

Q 和 Bluebird 这两个类库除了都能在浏览器里运行之外，充实的API reference也是其特征。由于Bluebird的性能比较好，所以我们一般用Bluebird的时候会比较多。

Q等文档里详细介绍了Q的Deferred和jQuery里的Deferred有哪些异同，以及要怎么进行迁移 Coming from jQuery 等都进行了详细的说明。

Bluebird的文档除了提供了使用Promise丰富的实现方式之外，还涉及到了在出现错误时的对应方法以及 Promise中的反模式 等内容。

这两个类库的文档写得都很友好，即使我们不使用这两个类库，阅读一下它们的文档也具有一定的参考价值。

## 替换bluebird

> tj: bluebird is MASSIVE, why not use v8's?

bluebird是Node.js世界里性能最好的模块，api非常齐全，功能强大，是原生Promise外的不二选择。


安装bluebird模块

```
$ npm i -S bluebird
```

见代码`hellopromise-bb.js`

```
// callbacks to promise
var fs = require("fs");
var Promise = require("bluebird");


function hello (file) {
  return new Promise(function(resolve, reject){
    fs.readFile(file, (err, data) => {
    	if (err) {
    		reject(err);
    	} else {
    		resolve(data.toString())
    	}
    });
  });
}

hello('./package.json').then(function(data){
  console.log('promise result = ' + data)
}).catch(function(err) {
  console.log(err)
})

```

它和之前的`hellopromise.js`执行结果是一模一样的，只差一行代码，即

```
var Promise = require("bluebird");
```

由此可以看出，Node.js原生的Promise和bluebird的实现是兼容的。只要掌握其中任何一个，几乎是0成本代价就可以学会。

这里用的是`var`来声明`Promise`，主要目的是为了当前文件使用，如果是koa或express这样的web项目里，使用全局替换呢？

其实也很简单，使用global全局替换就好，在应用的入口文件app.js里

```
global.Promise = require("bluebird");
```

## Promisification

> Promisification means converting an existing promise-unaware API to a promise-returning API.

这里主要介绍一下bluebird的promisefy和promisifyAll

promisifyAll更彻底，对类方法或者对象方法都可以进行promisify处理，是最简单的包裹promisify的常用手段，比如

```
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));

fs.readFileAsync("./package.json", "utf8").then(function(contents) {
    console.log(contents);
}).catch(function(e) {
    console.error(e.stack);
});
```

再来个稍微复杂一些的，下面这个例子有abc 3个方法，每个都是普通函数，通过bluebird的promisifyAll让他变成promise对象，继而完成流程控制。

```
var Promise = require("bluebird");

var obj  = { 
  a: function(){
    console.log('a')
  },
  
  b: function(){
    console.log('b')
  },
  
  c: function(){
    console.log('c')
  }
}

Promise.promisifyAll(obj);

obj.aAsync().then(obj.bAsync()).then(obj.cAsync()).catch(function(err){
  console.log(err)
})
```

是不是非常简单？

危险常常来自便利处，大量的这样promisifyAll，会不会有性能问题呢？error被bluebird包裹了，我们自己想定制呢？

## Promise的5个api

![](img/promise-methods.png)

1）构造方法

语法

> new Promise( /* executor */ function(resolve, reject) { ... } );

所有Promise只能这样创建，它的2个参数resolve和reject是唯一可以改变对象状态的方法。

- resolve会让状态从pending切换到fulfilled
- reject会让状态从pending切换到rejected（可选，不写也不算错）
  - Promise.prototype.then()可以当前操作的reject异常
  - Promise.prototype.catch()可以捕获全局的reject异常

备注：这里的resolve相当于Promise.resolve的别名，reject相当于Promise.reject的别名。

promise/api/a.js

```
new Promise(function(resolve){
    resolve(1);
}).then(function(value){
    console.log('new Promise ' + value);
});

Promise.resolve(1).then(function(value){
    console.log('Promise.resolve ' + value);
});

```

这2个示例resolve效果是一样的，可以看出Promise.resolve是便捷用法

promise/api/b.js

```
var error = new Error('this is a error')

new Promise(function(resolve, reject){
    reject(error);
}).catch(function(err){
    console.log('new Promise ' + err);
});

Promise.reject(error).catch(function(err){
    console.log('Promise.resolve ' + err);
});

```

这2个示例reject效果是一样的，可以看出Promise.reject是便捷用法


既然resolve和reject都有别名，那么我们能不能不适用构造函数，直接使用便捷用法呢？答案是不可以的，具体如下，见promise/api/c.js

```
// 以下做法是错误的
new Promise(function(){
    return Promise.resolve(1)
}).then(function(value){
    console.log('Promise.resolve 1 ' + value);
});

```

可能有的库会实现，但Node.js的原生Promise是不支持这样的写法的。

想便捷的话，一般采用下面这样的方法

promise/api/d.js

```
// 以下做法是正确的的
function hello(i){
    return Promise.resolve(i)
}

hello(1).then(function(value){
    console.log('Promise.resolve 1 ' + value);
});
```

这种写法可行原因是，Promise.resolve返回的是Promise对象，相当于`new Promise(resolve, reject)`

但是一定要注意，一旦的函数确定要返回Promise对象，就一定要全部可能分支都要返回Promise对象，不然出了问题非常难定位。

举个简单的例子，i是奇数或偶数做不一样的处理，一定要严谨。

promise/api/e.js

```
// 奇数和偶数
function hello(i){
  if (i % 2 == 0) {
    return Promise.resolve(i)
  } else {
    return Promise.reject(i)
  }
}

hello(1).then(function(value){
    console.log('Promise.reject 1 ' + value);
});

hello(2).then(function(value){
    console.log('Promise.resolve 1 ' + value);
});
```

其实按照规范Promise.resolve和Promise.reject还有更多用法，其他的给出语法定义，了解一下即可，没有特别需要说明的。

> Promise.resolve(value);
> Promise.resolve(promise);
> Promise.resolve(thenable);
> 
> Promise.reject(reason);

2）核心方法Promise.prototype.then()

语法

> p.then(onFulfilled, onRejected);
> 
> p.then(function(value) {
>    // fulfillment
>   }, function(reason) {
>   // rejection
> });

3）次核心方法Promise.prototype.catch()

> p.catch(onRejected);
> 
> p.catch(function(reason) {
>    // rejection
> });



4）工具方法

- Promise.all(iterable)
- Promise.race(iterable)

Promise.all 在接收到的所有的对象promise都变为 FulFilled 或者 Rejected 状态之后才会继续进行后面的处理， 与之相对的是 Promise.race 只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

简单点就说，all是所有都执行完成，再执行then，而race语义上相当于once，有个执行完成后就会执行then。一定要注意，它们是并发的，只是结果处理的点不一样而已。

它们的使用方法是一样，接收一个promise对象数组为参数。

all.js

```
'use strict'

let sleep = (time, info) => {
  return new Promise(function (resolve) {
    setTimeout(function () {
        console.log(info)
        resolve('this is ' + info)
    }, time)
  })
}

let loser = sleep(1000, 'loser')
let winner = sleep(4, 'winner')

// main
Promise.all([winner, loser]).then(value => {
    console.log("所有都完成后会执行then，它们是并行的哦: " + value)    // => 'this is winner'
})

```

执行结果

```
$ node api/all.js 
winner
loser
所有都完成后会执行then，它们是并行的哦: this is winner,this is loser
```

race.js

```
'use strict'

let sleep = (time, info) => {
  return new Promise(function (resolve) {
    setTimeout(function () {
        console.log(info)
        resolve('this is ' + info)
    }, time)
  })
}

let loser = sleep(1000, 'loser')
let winner = sleep(4, 'winner')

// main
Promise.race([winner, loser]).then(value => {
    console.log("只要有一个成功，就会执行then，和顺序无关，只看执行速度: " + value)    // => 'this is winner'
})
```

执行结果

```
$ node api/race.js
winner
只要有一个成功，就会执行then，和顺序无关，只看执行速度: this is winner
loser
```
## 参考阅读

1. [Promises/A]( http://wiki.commonjs.org/wiki/Promises/A)
2. [Promises/B]( http://wiki.commonjs.org/wiki/Promises/B)
3. [Promises/D]( http://wiki.commonjs.org/wiki/Promises/D)
4. [Promisejs]( https://www.promisejs.org/)
5. [Promises/A+]( https://promisesaplus.com/)
6. [As soon as possible]( https://github.com/kriskowal/asap)
7. [A minimalist implementation of a javascript promise]( https://gist.github.com/unscriptable/814052)
8. [Lightweight implementation of promises]( http://stackoverflow.com/questions/12923533/lightweight-implementation-of-promises)
9. [How is a promise/defer library implemented?]( http://stackoverflow.com/questions/17718673/how-is-a-promise-defer-library-implemented)
10. [Basic Javascript promise implementation attempt]( http://stackoverflow.com/questions/23772801/basic-javascript-promise-implementation-attempt/23785244#23785244)
11. [You're Missing the Point of Promises]( https://blog.domenic.me/youre-missing-the-point-of-promises/)
12. [Boom! Promises/A+ Was Born]( http://www.slideshare.net/domenicdenicola/boom-promisesa-was-born)
13. [Futures and promises]( http://en.wikipedia.org/wiki/Futures_and_promises)
14. [JavaScript Promises - There and back again]( http://www.html5rocks.com/zh/tutorials/es6/promises/)
15. [Promise 迷你书](https://github.com/liubin/promises-book)
16. https://blog.domenic.me/youre-missing-the-point-of-promises/
17. https://strongloop.com/strongblog/promises-in-node-js-with-q-an-alternative-to-callbacks/
18. https://github.com/kriskowal/q/wiki/General-Promise-Resources
19. https://www.w3.org/2001/tag/doc/promises-guide
10. https://github.com/bevacqua/promisees


源码 https://github.com/calvinmetcalf/lie/blob/master/lib/index.js


# 生成器Generators/yield

Generator Function（生成器函数）和Generator（生成器）是ES6引入的新特性，该特性早就出现在了Python、C#等其他语言中。生成器本质上是一种特殊的<FONT COLOR=#336DB7>迭代器https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/The_Iterator_protocol</FONT>。

Generator函数本意是iterator生成器，函数运行到yield时退出，并保留上下文，在下次进入时可以继续运行。

生成器函数也是一种函数，语法上仅比普通function多了个星号* ，即function* ，在其函数体内部可以使用yield和yield* 关键字。

简单理解，这是ES6的新特性，function 后面带 * 的叫做Generator，如以下代码所示。

```
function* doSomething() {
  ....
}
```

先看一下Generator如何执行：

```
function* doSomething() {
    console.log('1');
    yield; // Line (A)
    console.log('2');
}

var gen1 = doSomething();

gen1.next(); // Prints 1 then pauses at line (A)
gen1.next(); // resumes execution at line (A), then prints 2
```

**说明**

- gen1是产生出来的Generator对象
- 第一个next，会打印出1，之后悬停在 yield所在行，即Line (A)
- 第二个next，恢复line (A)点的执行，之后打印出2

如果有多个yield呢？那么会有无穷无尽的next。

于是tj就写[co](https://github.com/tj/co)这个著名的Generator执行器，co目前已经是v4了，彻底的面向Promise了，个中曲折也是够八卦的了。

# Async函数/Await（以前说是ES7 stage-3）

Generator的弊病是没有执行器，它本身就不是为流程控制而生的，所以co的出现只是解决了这个问题。

可是，你不觉得奇怪么？为什么非要加个co，才能好好的玩耍？为什么不能是直接就可以执行，并且效果和Yieldable一样的呢?

Async/Await 就是这样被搞出来的，很多人认为它是异步操作的终极解决方案。

<FONT COLOR=#26B4FD>**Await**</FONT>

Await的3种可能情况

- Await + Async函数
- Await + Promise
- await + co（co会返回Promise，这样可以Yieldable，但难度较大，适合老手）

头2种是比较常用的，第三种co作为promise生成器，是一种hack的办法。

下面给出第一种和第二种的示例：

```
async function a2() {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, 1000);
  })
}

async function a1() {
  console.log("hello a1 and start a2");
  await a2();
  console.log("hello end a2");
}

async function a0() {
  console.log("hello a0 and start a1");
  await a1();
  console.log("hello end a1");
}

a0()
```

**执行**

需要使用babel或者其他支持async函数的编译工具，这里使用runkoa（是为koa支持async函数做的基于babel的简单封装）

```
$ runkoa async.js
async.js
3babel presets path = /Users/sang/.nvm/versions/node/v4.4.5/lib/node_modules/runkoa/node_modules/
hello a0 and start a1
hello a1 and start a2
hello end a2
hello end a1
```

**异常处理**

Node.js里关于异常处理有一个约定，即同步代码采用try/catch，非同步代码采用error-first方式。对于Async函数俩说，它的Await语句是同步执行的，所以最正常的流程处理是采用try/catch语句捕获，和generator/yield是一样的。

下面的代码所展示的是通用性的做法：

```
try {
  console.log(await asyncFn());
} catch (err) {
  console.error(err);
}
```

很多时候，我们需要把异常做得粒度更细致一些，这时只要把Promise的异常处理好就好了。

Promise里有2种处理异常的方法

- then(onFulfilled, onRejected)里的onRejected，处理当前Promise里的异常
- catch处理全局异常


**Async函数总结**

- Async函数语义上非常好
- Async不需要执行器，它本身具备执行能力，不像Generator
- Async函数的异常处理采用try/catch和Promise的错误处理，非常强大
- Await接Promise，Promise自身就足够应对所有流程了
- Await释放Promise的组合能力，外加Promise的then，基本无敌


# co引出的“血案”

ES6的Generator本意是为了计算而设计的迭代器，但tj觉得它可以用于流程控制，于是就有了co，co的历史可以说经历了目前所有的流程控制方案，而且由于支持Generator和yield就导致yieldable。

实际上co和Generator是把双刃剑，给了我们强大便利的同时，也增加了非常多的概念，可能是过渡性的，也可能是过时的。

可是，你真的需要了解这么多么？从学习的角度，当然是多多意义，如果从实用的角度看，你可能不需要。

存在即合理，那么我们就看看这“血案”吧:

- 学习ES6的Generator
- 了解ES6的迭代器和迭代器相关的2种协议，了解for-of
- 了解co和co的2种用法，源码
- 了解yieldable 5种（包括不常用Thunk）
- 如果是koa，还需要了解convert和compose

# 5种 yieldable

yieldable本来是没有这个词的，因为在Generator里可以是yield关键词，而yield后面接的有5种可能，故而把这些可以yield接的方式成为yieldable，即可以yield接的。

- Promises
- Thunks (functions)
- array (parallel execution)
- objects (parallel execution)
- Generators and GeneratorFunctions

这里我把co和promise做了简单的关键，同时区分Yieldable里的并行和顺序执行处理方式，以便大家能够更好的理解co和Yieldable。

![Co](img/co.png)

- 顺序执行
  - Promises
  - Thunks
- 并行
  - array
  - objects

无论是哪种，它们其实都可以是Promise，而既然是Thunk对象，它们就可以thenable，而co v4.6版本的执行的返回值就是Promise，至此完成了左侧闭环。

至于Generator和GeneratorFunction就要从yield和yield*讲起，在koa 1.x和2.x里有明显的应用。

最关键的是，Generator是用来计算的迭代器，它是过渡性的产物。yiedable足够强大，只是学习成本稍高，理解起来也有些难度。

# 推导出学习重点

综上所述

- Async函数是趋势，如果Chrome 52. v8 5.1已经支持Async函数(https://github.com/nodejs/CTC/issues/7)了，Node.js支持还会远么？
- Async和Generator函数里都支持promise，所以promise是必须会的。
- Generator和yield异常强大，不过不会成为主流，所以学会基本用法和promise就好了，没必要所有的都必须会。
- co作为Generator执行器是不错的，它更好的是当做Promise 包装器，通过Generator支持yieldable，最后返回Promise，是不是有点无耻？

我整理了一张图，更直观一些。

![All](img/all.png)

- 红色代表Promise，是使用最多的，无论async还是generator都可用
- 蓝色是Generator，过度货
- 绿色是Async函数，趋势

**结论**：Promise是必须会的，那你为什么不顺势而为呢？

**推荐**：使用Async函数 + Promise组合，如下图所示。

![Suggest](img/suggest.png)

**实践**

合理的结合Promise和Async函数是可以非常高效的，但也要因场景而异

- Promise更容易做promisefyAll（比如使用bluebird）
- Async函数无法批量操作

那么，在常见的Web应用里，我们总结的实践是，dao层使用Promise比较好，而service层，使用Async/Await更好。

dao层使用Promise：

- crud
- 单一模型的方法多
- 库自身支持Promise

这种用promisefyAll基本几行代码就够了，一般单一模型的操作，不会特别复杂，应变的需求基本不大。

而service层一般是多个Model组合操作，多模型操作就可以拆分成多个小的操作，然后使用Await来组合，看起来会更加清晰，另外对需求应变也是非常容易的。
