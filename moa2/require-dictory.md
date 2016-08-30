## 加载某个目录到一个对象上

如果我想加载某个目录到一个对象上，需要如何做呢？

### 准备

```
mkdir require-dictory
cd require-dictory
ls
npm init
npm i -S require-directory
touch index.js
```

### 核心代码

```
var requireDirectory = require('require-directory');
module.exports = requireDirectory(module);
```

### 测试

touch test.js

```
var obj = require('./index')

console.log(obj);
```

然后

```
➜  require-dictory git:(master) ✗ node test.js 
{ node_modules: { 'require-directory': { index: [Object], package: [Object] } },
  package: 
   { name: 'require-dictory',
     version: '1.0.0',
     description: '',
     main: 'index.js',
     scripts: { test: 'echo "Error: no test specified" && exit 1' },
     author: '',
     license: 'ISC',
     dependencies: { 'require-directory': '^2.1.1' } },
  test: {} }
```

此时index对象就返回了index和test和package.json里的内容

测试,创建third.js

```
➜  require-dictory git:(master) ✗ touch third.js
➜  require-dictory git:(master) ✗ node test.js
{ node_modules: { 'require-directory': { index: [Object], package: [Object] } },
  package: 
   { name: 'require-dictory',
     version: '1.0.0',
     description: '',
     main: 'index.js',
     scripts: { test: 'echo "Error: no test specified" && exit 1' },
     author: '',
     license: 'ISC',
     dependencies: { 'require-directory': '^2.1.1' } },
  test: {},
  third: {} }
```

此时，这个模块就写完啦，打印出当前模块下的挂载的所有对象