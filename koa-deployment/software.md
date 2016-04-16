## 安装必备软件

### 安装git

如果上面没有复制给sang账户sudo权限，请切换到root账户操作

```
sudo apt-get update
sudo apt-get install git
```

### 安装nginx

```
sudo apt-get install nginx
```

### 开机启动
（http://www.jianshu.com/p/2e03255cfabb）

```
sudo apt-get install sysv-rc-conf
sudo sysv-rc-conf nginx on
```

注意：Ubuntu系统中服务的运行级别

- 0        系统停机状态
- 1        单用户或系统维护状态
- 2~5      多用户状态
- 6        重新启动

### 准备工作目录

```
mkdir -p workspace/github
cd workspace/github
```
