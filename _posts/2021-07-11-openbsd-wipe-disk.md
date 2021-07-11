---
---



# OpenBSD 上安全擦除磁盘分区  

> 个人笔记，仅供参考。

## 0x00

我的 openBSD 是按照默认方式安装的，`/home` 被按照 ffs 格式挂载在 `sd0k` 分区上。  

擦除它（不仅仅是 `rm` 那么简单），也不是很难 ;-)  

## 使用 dd 命令  

先把分区里的东西先备份走。  
登陆进 root 用户；在 `/etc/fstab` 把 /home 这一条注释掉。  
umount /home （卸载不了的话就重启）  

用 root 用户执行：
```
  # dd bs=4096 if=/dev/urandom of=/dev/rsd0k  
```

(第二天 XDD) 完成后给该分区格式化：  
```
  # newfs rsd0k
```
在 `/etc/fstab` 把 /home 这一条去掉注释。

`mount /dev/sd0k /home`  

或者直接重启。

Bingo！又是一块干净的分区，舒服了！

