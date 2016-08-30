## 如何发布npm模块

那么我们来发布一个吧，顺便学习一下如何发布npm模块

- 登陆npm
- 发布

```
➜  require-dictory git:(master) ✗ nrm use npm

   Registry has been set to: https://registry.npmjs.org/

➜  require-dictory git:(master) ✗ npm login
Username: i5ting
Password: 
Email: (this IS public) shiren1118@126.com
```

一定要注意`nrm use npm`，源一定要是npmjs，不然是无法登陆成功的。

然后在package.json所在目录里

```
npm publish .
```

即可

可以在package.json里增加start，每次发布模块的时候能简单点