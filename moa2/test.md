## 添加测试

```

npm install --save-dev mocha
npm install --save-dev chai
npm install --save-dev sinon
npm install --save-dev supertest
npm install --save-dev zombie
```

```
mkdir test
touch test/test.js
```

将下面代码copy进去

```
var request = require('supertest');
var assert  = require('chai').assert;
var expect  = require('chai').expect;
require('chai').should();

var app = require('../app');

describe('GET /users', function(){
  it('respond with text', function(done){
    request(app)
      .get('/users/')
      .set('Accept', 'application/text')
      .expect('Content-Type', /text/)
      .expect(200, done);
  })
})
```


执行测试

```
➜  mvc git:(master) mocha -u bdd

******************************************************
		MoaJS Apis Dump
******************************************************

┌─────────────────────────────────────────────────────┬────────┬────────┐
│ File                                                │ Method │ Path   │
├─────────────────────────────────────────────────────┼────────┼────────┤
│ /Users/sang/workspace/stuq/aaaa/mvc/routes/index.js │ get    │ /      │
├─────────────────────────────────────────────────────┼────────┼────────┤
│ /Users/sang/workspace/stuq/aaaa/mvc/routes/test.js  │ get    │ /test  │
├─────────────────────────────────────────────────────┼────────┼────────┤
│ /Users/sang/workspace/stuq/aaaa/mvc/routes/users.js │ get    │ /users │
└─────────────────────────────────────────────────────┴────────┴────────┘


  GET /users
GET /users/ 200 10.206 ms - 23
    ✓ respond with text (50ms)


  1 passing (55ms)
```

就这样完成了简单的测试


测试生命周期是非常重要的，给出模板test2.js

```
var request = require('supertest');
var assert  = require('chai').assert;
var expect  = require('chai').expect;
require('chai').should();

// 测试代码基本结构
describe('UserModel', function(){
	before(function() {
    // runs before all tests in this block
  })
  after(function(){
    // runs after all tests in this block
  })
  beforeEach(function(){
    // runs before each test in this block
  })
  afterEach(function(){
    // runs after each test in this block
  })
	
  describe('#save()', function(){
    it('should return sang_test2 when user save', function(done){
      done()
    })
  })
})
```

至此已有功能

- livereload
- mount-routes
- test
