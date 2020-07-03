---
title: 如何利用更多的 AntiS USB 空间（并加密）
date: 2020-07-02
---



## 如何利用更多的 AntiS USB 空间（并加密）

如果你正在使用本人制作维护的发行版：[antiS (Liveslak)](https://github.com/mdrights/liveslak) —— 一款隐私加强、翻越长城、反取证的 live 性质的操作系统（基于 Slackware Linux），
并且你是在一个比较大的 U盘上使用 antiS，那你可能会感兴趣是否能把 U盘剩余的空间都利用上吧！

下面我们来试试：  

## 首先我们先把 antiS 烧录到 U盘：  

教程已写过，在[这里](https://github.com/mdrights/liveslak#installation)。   

## 为 U盘添加一个分区：  

- 你需要在另一台 Linux 进行操作（如 Ubuntu/Debian | 即：不能是正在运行 antiS 的系统）   
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

- 然后挂载它就可以用了。重启系统，以后每次在文件管理器里就可以挂载了。


### 下面是创建加密分区的步骤：

- 如果需要的话，可以创建加密分区：  
```
   # cryptsetup --verify-passphrase luksFormat /dev/sdb3  （需要给新分区输入两次自己的密码，记得要够长、够复杂哟！）  
   # cryptsetup luksOpen /dev/sdb3 usb   （这个 usb 是可以自己随意命名的）   
```

- 格式化分区：  
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

