# Nodejs Newbie

- linux操作系统和bash编程要会（以后要有一定的服务器部署、运维工作）
- vi是编辑器，需要会使用
- git必须会，目前最火的版本控制工具
- 常用命令行工具（ack，autojump等）

## 前端入门的4本书

- 精通CSS+DIV 网页样式与布局 http://product.china-pub.com/35553
- 精通CSS：高级Web标准解决方案（第2版）http://product.china-pub.com/196593
- 锋利的jQuery(第2版) http://product.china-pub.com/3661548
- GitHub入门与实践 http://product.china-pub.com/4727673

第一本书是傻瓜式的入门的书，老点，但简单，符合国人思维，入门html和css比较合适

第二本书是css领域不错的书，加深理解css，努力成为一个合格的前端

第三本书是jquery的书，也是很简单，为啥没有直接javascript的原因是，jq足够简单，先实习效果，以后再慢慢补js基础即可，如果上来就js，很多人是搞不定的

第四本书是git和github的用法，是版本控制里比较简单的，比较适合入门

## 要求

- ubuntu
- sublime text3（或者vsc https://github.com/i5ting/vsc）
- 编码风格 https://github.com/dead-horse/node-style-guide
- 常用命令行工具

## Tips

### 编辑器

只允许文本编辑器，不准使用任何IDE

使用sublime的快速打开文件

    ctrl + p（mac是command + T）

在终端里使用subl命令打开文件，（如果是mac，需要安装https://github.com/i5ting/subl）

    subl app.js

快速定位到某一行

    ctrl + g （mac是command + L）

- [网上找的](http://my.oschina.net/nodeonly/blog/489463)

关于tab配置

```
{
    "default_encoding": "UTF-8",
    "default_line_ending": "unix",
    "font_size": 10,
    "rulers":
    [
        80
    ],
    "tab_size": 2,
    "translate_tabs_to_spaces": true,
    "word_wrap": "false"
}
```
### 使用oh-my-zsh

- 官网 http://ohmyz.sh/
- 代码 https://github.com/robbyrussell/oh-my-zsh

安装步骤

- 先安装zsh
- 安装oh-my-zsh

以后环境变量在~/.zshrc里

### 安装ack，命令行查找代码

http://beyondgrep.com/install/


Ubuntu

- Package "ack-grep"

sudo apt-get install ack-grep

Mac

- brew install ack

### 使用autojump跳转目录

https://github.com/wting/autojump

Linux

    sudo apt-get install autojump

Mac os

    brew install autojump

需要修改~/.zshrc里的plugin,修改为

    plugins=(git autojump)

然后

    source ~/.zshrc

至此，已经完成了安装。

此后cd到任意目录，以后就可以使用j这个直达到某个目录了，下面是示例：

    ➜  nodejs-newbie git:(master) ✗ cd ~/workspace/github/nodejs-newbie
    ➜  nodejs-newbie git:(master) ✗ cd ~
    ➜  ~  j nodejs-n
    /Users/sang/workspace/github/nodejs-newbie
    ➜  nodejs-newbie git:(master) ✗

如果想玩的更high，可以参见https://github.com/clvv/fasd



### 查询文档

- http://zealdocs.org/ (推荐，离线下载)

有很多doc在dash（mac）里默认是没有的；

see here ： http://kapeli.com/docset_links

如果是下载到本地的docset，放到zealdocs目录下面，需要重启zeal

### 学习git用法

常用

	alias gs='git status'
	alias gp='git push'

使用alias来简化命令输入


- 重磅推荐peter wang写的 [搬进 Github](http://gitbeijing.com/)

下面给出一些git学习资料

- [git-guide](http://www.bootcss.com/p/git-guide/)
- [git入门gif演示](https://git.oschina.net/wzw/git-quick-start)
- [写出好的 commit message](http://ruby-china.org/topics/15737)
- [github-cheat-sheet](https://github.com/tiimgreen/github-cheat-sheet/blob/master/README.zh-cn.md)
- [分支管理](http://www.juvenxu.com/2010/11/28/a-successful-git-branching-model/)
- [Git-it Challenges is a terminal based app for learning Git and GitHub](http://jlord.github.io/git-it/)
- [高富帅们的Git技巧（译）](http://blog.csdn.net/zmlcool/article/details/8682382)
- [Git 怎样保证fork出来的project和原project（上游项目）同步更新](http://www.tuicool.com/articles/Mnmmqyi)
- [10.Git之本地忽略](http://blog.csdn.net/kangear/article/details/13169395)
- [git-flow 备忘清单](http://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)
- [Git flow 開發流程 ihower](http://ihower.tw/blog/archives/5140)
- [git bisect](http://www.oschina.net/translate/letting-git-bisect-help-you)

	$ git update-index --assume-unchanged /path/to/file       #忽略跟踪
	$ git update-index --no-assume-unchanged /path/to/file  #恢复跟踪
