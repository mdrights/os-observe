---
date: 2022-02-20
---



## 如何利用更多的 antiS USB 空间（在 live USB 创建分区）

> 最新更新：2022-02-20

如果你是在一个比较大的 U盘上使用 antiS，那你可能会感兴趣是否能把 U盘剩余的空间都利用上吧！

**注意**：只有在 antiS 版本 >= 2022.01.rc3 才支持这样操作。  

下面我们来试试：  

## 首先我们先把 antiS 烧录到 U盘：  

方法已写过，在[这里](https://github.com/mdrights/liveslak#installation)。   

以下均在 **Linux 操作系统**上操作（大多数 Linux 发行版都会配有本教程需要用到的工具，若系统没有相关命令请自行安装；antiS 已配备这些命令）。  

(macOS 因缺乏相应工具暂不支持本文的操作； Mac 的机器也不支持从这种自建分区的 antiS U盘的启动；但是 Mac 机器可以在 macOS 系统上打开（挂载）含自建分区的 antiS U盘，详见下文)  

因此建议用家平时备2个U盘，每次轮流烧录，特别是有自建分区的，也方便把该分区里的东西倒到新U盘分区。  

<!--more-->

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

- 上面这命令用的是 Linux 默认的 ext4 文件系统格式；如果想该分区也能在 macOS 上打开（Windows不行），请使用 exfat 格式：（在macOS打开方式有些特别，详见附录）   
```
   # mkfs.exfat -L ANTIS-persist /dev/sdb3
```

- 然后重启进入刚烧录的 antiS 系统，在文件管理器里就可以挂载了。祝使用愉快！

- Tips: 格式化命令也可以替换为一个图形化界面的工具：Gparted （在程序菜单里就有）。

<hr />

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

- 同样，重启进入 antiS 系统，在文件管理器就可以解密并挂载这个分区啦！（需要输入两次密码）  

![]({{ site.baseurl }}/images/antis-decrypt.jpg)
	图一：第一次密码：创建加密分区时的密码  

![]({{ site.baseurl }}/images/antis-mount.jpg)
	图二：第二次密码：本系统 root 密码：toor  


<hr />

# 附录

- 在 macOS 挂载 antiS U盘分区的方法：（插入U盘会提示不支持之类的错误，可 ignore 忽略掉即可）。

	- 先查看该分区的编号：
	```
	  diskutil list
    ```
	- 使用命令挂载
	```
	  sudo mkdir -p /Volumes/usb/   （创建一个文件夹，创建一次即可，以后都用这个文件夹；也可以选择现有的文件夹就不用创建了）
	  sudo mount -t exfat /dev/disk2s3  /Volumes/usb/  （比如我的分区编号是 disk2s3 ）
	```

	- 然后从 finder (Command + shift + G) 输入上面的文件夹路径 就可以看到里面的文件了。  

	- 用完记得卸载U盘：
	```
      sudo umount /Volumes/usb/
	```


