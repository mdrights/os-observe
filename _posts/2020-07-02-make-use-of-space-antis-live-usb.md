---
title: 如何利用更多的 antiS USB 空间（在 live USB 创建分区）
date: 2022-02-20
---



## 如何利用更多的 antiS USB 空间（在 live USB 创建分区）

如果你是在一个比较大的 U盘上使用 antiS，那你可能会感兴趣是否能把 U盘剩余的空间都利用上吧！

下面我们来试试：  

## 首先我们先把 antiS 烧录到 U盘：  

方法已写过，在[这里](https://github.com/mdrights/liveslak#installation)。   

以下均在 **Linux 操作系统**上操作（大多数 Linux 发行版都会配有本教程需要用到的工具，请自行安装）。(macOS 因缺乏相应工具暂不支持)  

因此建议用家平时备2个U盘，每次轮流烧录，特别是有自建分区的，也方便把该分区里的东西倒到新U盘分区。  

## 为U盘添加一个分区：  

- 启动登录进 一个 Linux 系统（antiS 也可以的，不过建议最好是在一个 antiS 系统对另一个烧录了 antiS 的U盘进行操作，不要对当前正在运行的U盘操作，虽然可能也能成功）  

**以下所有操作都以 root 用户进行（# 是命令提示符，不必输入）。**  
```  
   先知道 U盘的编号（通常最底部的盘就是刚插入的）：  
   # lsblk  
   创建分区（记得不要挂载它）：  
   # echo "n
   > p
   > 3
   > 
   > 
   > w" | fdisk /dev/sdX  
                       （X 是你U盘编号，比如 sdb 注意这里是不带数字的）  
   再 lsblk 查看新建的分区（比如应可看到 sdb3）   
```  

- （如果不需要建加密分区）格式化分区：  
```
   # mkfs.ext4 -L ANTIS-persist /dev/sdb3
```

- 上面这命令用的是 Linux 默认的 ext4 文件系统格式；如果想该分区也能在 Windows 和 macOS 上，请使用 exfat 格式：  
```
   # mkfs.exfat -L ANTIS-persist /dev/sdb3
```

- 然后挂载它就可以使用了。重启系统，以后每次在文件管理器里就可以挂载了。


### 下面是创建加密分区的步骤：

- 如果需要的话，可以创建加密分区（不需要上述的格式化命令了，只需要创建分区）：  
```
   # cryptsetup --verify-passphrase luksFormat /dev/sdb3  （需要给新分区输入两次自己的密码，记得要够长、够复杂哟！）  
   # cryptsetup luksOpen /dev/sdb3 usb   （这个 usb 是可以自己随意命名的）   
```

- 这时再格式化分区：  
```
   # mkfs.ext4 -L ANTIS-persist /dev/mapper/usb
   # e2label /dev/mapper/usb ANTIS-persist
```

- 暂时关闭新建的加密分区：  
```
   # cryptsetup luksClose /dev/mapper/usb
```

- 重启系统，以后每次用文件管理器就可以解密并挂载这个分区啦！（需要输入两次密码）  

![]({{ site.baseurl }}/images/antis-decrypt.jpg)
	图一：第一次密码：创建加密分区时的密码  

![]({{ site.baseurl }}/images/antis-mount.jpg)
	图二：第二次密码：本系统 root 密码：toor  

