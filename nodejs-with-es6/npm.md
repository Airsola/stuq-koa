## 什么是npm

NPM的全称是Node Package Manager[1]  ，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准，通常称为node包管理器。顾名思义，它的主要功能就是管理node包，包括：安装、卸载、更新、查看、搜索、发布等。

NPM作为Node的模块管理和发布工具，作用与Ruby的gem、Python的pypl或setuptools、PHP的pear和.Net的Nuget一样。在当前前端工程化极速狂奔的年代，即使不做nodejs的开发，也需要学习和使用NPM的，谁叫grunt/gulp、bower、yeoman这一堆的工具都通过NPM发布呢？！


## 什么是nodejs模块

Nodejs自身提供了基本的模块，但是开发实际应用过程中仅仅依靠这些基本模块则还需要较多的工作。幸运的是，Nodejs库和框架为我们提供了帮助，让我们减少工作量。但是成百上千的库或者框架管理起来又很麻烦，有了NPM，可以很快的找到特定服务要使用的包，进行下载、安装以及管理已经安装的包。
  
### sdk内置模块

- http
- net
- url
- path

### 自己造的轮子

nodejs以包的形式组织程序模块，而包的定义却十分简单——包含文件内容符合规范package.json文件的目录或归档文件。并通过<package-name>@<version>来唯一标识每个包。

### 如果安装模块？

```
npm install --save xx
```

区分

- --save
- --save-dev

### 如果安装binary模块？

```
npm install --save xx
```