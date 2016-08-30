# 更多实践
## gulp

如果我们的框架里没有gulp，会显得有点low，那我们也使用gulp作为构建工具吧

一般作用

- 构建，打包
- watch

先实现一个简单的watch吧

```
npm i -g gulp
npm i -S gulp
npm i -S gulp-watch
npm i -S gulp-mocha
```

create gulpfile.js

```
touch gulpfile.js
```

in gulpfile.js

```
var gulp = require('gulp');
var watch = require('gulp-watch');
var mocha = require('gulp-mocha');

var source_path = ['test/**/*.js', 'lib/*.js'];

gulp.task('watch', function() {
  gulp.watch(source_path, ['mocha']);
});

gulp.task('mocha', function () {
    return gulp.src(source_path , {read: false})
        // gulp-mocha needs filepaths so you can't have any plugins before it 
        .pipe(mocha({reporter: 'spec'}));
});
```

测试

执行一次测试
```
gulp mocha
```

如果source_path目录下的文件变动，会触发一次测试（作业依赖）

```
gulp watch
```

增加默认gulp命令

```
gulp.task('default',['mocha', 'watch']);
```

rails里的rake routes挺爽，不妨实现一些

```
var mount = require('mount-routes');

gulp.task('routes', function() {
  var express       = require('express');
  var app           = express();
  
  // mount routes
  mount(app, __dirname + '/app/routes', true);
});
```

于是

```
gulp routes
```

效果基本一样，有木有？

更多之前曾在Stuq微课堂里分享过【Gulp实战和原理解析（以weui作为项目实例）】

http://i5ting.github.io/stuq-gulp/

还有一篇更基础的教程

https://github.com/streakq/js-tools-best-practice/blob/master/doc/Gulp.md

## browser-sync

代码变动，浏览器也可以不刷新的

- browser-sync

```
gulp.task('less_server',['build_less'] ,function () {
    browserSync.init({
      proxy: "127.0.0.1:3005"
    })
    gulp.watch('./public/css/main.less', ['build_less']);
    gulp.watch('./public/*.html',function () {
      browserSync.reload();
    });
});
```

具体参见【Gulp实战和原理解析（以weui作为项目实例）】

http://i5ting.github.io/stuq-gulp/


## open

都是mac闹的，人都变懒了

```
var express  = require('express');
var app      = express();
var path     = require('path');
var open     = require("open");

app.get('/', function (req, res) {
  res.send('Hello World')
})

// 随机端口3000 - 10000 之间
app.listen(4001)

open("http://127.0.0.1:4001");
```
