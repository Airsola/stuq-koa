## 添加测试

```
npm install --save-dev ava
npm install --save-dev sinon
npm install --save-dev superkoa
npm install --save-dev zombie
```

```
mkdir test
touch test/test.js
```

将下面代码copy进去

```
import test from 'ava'
import superkoa from 'superkoa'

test.cb("first test", t => {
  superkoa('./app.js')
    .get("/")
    .expect(200, function (err, res) {
      t.ifError(err)
      var userId = res.body.id;
      t.is(res.text, 'Hello Koa', 'res.text == Hello Koa')
      t.end()
    });
});
```


执行测试

```
your-koa git:(master) ava -v

  ✔ first test (272ms)

  1 test passed [16:54:55]
```

就这样完成了简单的测试


测试生命周期是非常重要的，给出模板test2.js

- test.before([title], implementation)
- test.after([title], implementation)
- test.beforeEach([title], implementation)
- test.afterEach([title], implementation)

举例

```
test.before.cb((t) => {
  setTimeout(() => {
    t.end();
  }, 2000);
});

test('#save()', t => {
  let user = new User({
    username: 'i5ting',
    password: '0123456789'
  });

  user.save((err, u) => {
    if (err) log(err)
    t.is(u.username, 'i5ting');
  });
});
```

更多参考https://github.com/i5ting/ava-practice

至此已有功能

- livereload
- mount-routes
- test
