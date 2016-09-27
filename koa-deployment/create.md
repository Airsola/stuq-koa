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

## 总结

区分以下命令，是学习linux的基础

- ssh
- scp
- su
- sudo

## 附：阿里云服务器挂载数据硬盘（可选）

  购买普通阿里云服务器的时候，本机默认自带的系统盘大小为20G，但是这样的大小是不满足部署产品服务器的需求，
  所以可以购买阿里云数据盘，一半大小为200G

- 首先使用root用户查看系统版本，本文是在centos中部署使用

  在终端中使用下面命令查看系统版本

```
$ lsb_release -a

LSB Version:  :core-4.1-amd64:core-4.1-noarch
Distributor ID: CentOS
Description:  CentOS Linux release 7.2.1511 (Core)
Release:  7.2.1511
Codename: Core
```

确定系统版本之后在终端中确认系统盘情况(注：使用root用户):

```
$ fdisk -l

Disk /dev/xvda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009e68a

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048    41943039    20970496   83  Linux

Disk /dev/xvdb: 214.7 GB, 214748364800 bytes, 419430400 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xd0c73cf7

    Device Boot      Start         End      Blocks   Id  System
/dev/xvdb1            2048   419430399   209714176   83  Linux
```

首先确认在阿里云中购买了数据盘，上面的Disk /dev/xvda是自带的系统盘，/dev/xvda1表示已经挂载并且在使用中，Disk /dev/xvdb是数据盘，
上面的情况是已经挂载好的，如果没有挂载情况，只显示Disk /dev/xvdb: 214.7 GB, 214748364800 bytes, 419430400 sectors，表示未被
使用

- 将未被分区挂载的数据盘进行分区挂载

```
$ fdisk  /dev/xvdb
```
根据提示，输入"n","p","1",两次回车,"wq",分区开始，很快就会结束

- 查看新的分区：

```
$ fdisk -l
```
此时应该显示/dev/xvdb已被分区, like this:

```
Device                Boot      Start         End      Blocks   Id  System
/dev/xvdb1            2048   419430399   209714176              83  Linux
```
- 格式化新的分区

以ext3为例：使用“mkfs.ext3 /dev/xvdb1”命令对新分区进行格式化，格式化的时间根据硬盘大小有所不同。
(也可自主决定选用其它文件格式，如ext4等)

```
$ mkfs.ext3 /dev/xvdb1
```
需要等一段时间等待格式化完毕

- 添加新的分区信息

```
$ echo '/dev/xvdb1  /mnt ext3    defaults    0  0' >> /etc/fstab
```
- 查看分区

```
$ cat /etc/fstab
```
出现/dev/xvdb1  /mnt ext3    defaults    0  0 说明成功

- 挂载新的分区

```
$ mount -a
```

- 查看分区的情况

```
$ df -h

Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/xvda1      20510332 2031552  17413872  11% /
devtmpfs          934320       0    934320   0% /dev
tmpfs             942004       0    942004   0% /dev/shm
tmpfs             942004   98724    843280  11% /run
tmpfs             942004       0    942004   0% /sys/fs/cgroup
/dev/xvdb1     206292664 1065720 194724852   1% /mnt
```
/dev/xvdb1已经成功启用，挂载成功
