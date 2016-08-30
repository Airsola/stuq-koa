## 使用nodemon

livereolad

- nodemon
- supervisor

```
npm i -D nodemon
```

修改package.json

```
  "scripts": {
    "start": "./node_modules/.bin/nodemon bin/www"
  },
```

然后

```
npm start
```

改一下routes/index.js里的代码试试，你再也不要重启了

演示效果