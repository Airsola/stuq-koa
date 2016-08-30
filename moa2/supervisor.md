## 使用supervisor

livereolad

- nodemon
- supervisor

```
npm i -S supervisor
```

修改package.json

```
  "scripts": {
    "start": "./node_modules/.bin/supervisor bin/www"
  },
```

然后

```
npm start
```

改一下routes里的代码试试，你再也不要重启了
