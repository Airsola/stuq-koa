## 部署服务器

阿里云

ubuntu 14.10 LTS  64位

## 登录远端服务器

ssh root@ip

## 创建用户

```
  # sudo useradd -m -d /home/sang -s /bin/bash -c "the sang user" -U sang
  # passwd sang
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
```

- useradd创建登录用户
- passwd设置用户登录密码

## 赋予sudo权限

如果有必要使用sudu权限，请修改

```
  # sudo vi /etc/sudoers
```

复制root行改为sang即可

```
  # User privilege specification
  root	ALL=(ALL:ALL) ALL
  sang	ALL=(ALL:ALL) ALL
```
## 切换用户

```
  # su - sang
  $ ls
  $
  $ pwd
  /home/sang
  $
```