---
date: 2017-09-03
---

## 私人定制隐私加强的 live 操作系统：LiveSlak


### 缘起
本人其實自去年起便定期（每月）製作一份定製的live iso鏡像，採用的操作系統是之前介紹過的 Devuan GNU/Linux，它是基於 Debian GNU/Linux 的，现在本人仍会不定期更新它，可看看 [以前的博文](https://mdrights.github.io/os-observe/posts/2017/08/Refracta-live.html)）。

从今年（2017）9月开始，本人更换了平台，基于 [Slackware Linux](http://slackware.com) 制作 Live 系统，代码来自 [Alien Bob 的 LiveSlak](http://www.slackware.com/~alien/liveslak/)（感谢！）。 **目前还是beta测试状态，请谨慎使用。**  

它主要有这样的特性：   

- 面向中文用户 _（似乎是仅有的中文化的隐私向操作系统）_    
- 隐私保护和墙国特色信息自由类软件
- 应用类软件均由本人亲自编译、打包（确保代码的安全和系统的纯净）
- 内核及系统加固（TODO）
- live 系统：

<!--more-->
> live系統的修改，和用户产生、放置的所有文件都**不會保存**，每次重啓都會還原到製作時的狀態。這一招就能抵禦諸如木馬的长期駐留，最大限度降低它的破壞。

### 为什么需要它
我们终端用户受到的黑客攻击/监控，最主要的方式就是 網頁和電郵的附件夾帶的惡意程序/木马啦。而木马等恶意程序最容易藏在我们日常最常用的office文档啦、PDF啦、图片啦音视频啦等二进制文件，大部分用户基本没有能力分辨其中是否有木马，但又必须每天都要下载和打开这些文件，风险非常大，对不对？

那么你需要 live 操作系统^ ^。当需要打開網頁，和/或 打開別人發給你的文件（包括各種文檔、pdf、exe、圖片、音頻等）時，木马只会在当前环境中存活，它看到的只是当前空空如也的系统，并随着机器的关机而消失。

### 用法
1. 放在虚拟机 （如 Virtualbox）里使用（须先安装 [Virtualbox虚拟机软件](https://www.virtualbox.org/wiki/Downloads)；或   
2. 刻录到 USB盤上，插入任何一台电脑启动；（记得开机时进入BIOS设置为USB优先启动；刻录方法见[这里](https://rufus.akeo.ie/?locale=zh_CN)，或[这里](https://program-think.blogspot.com/2013/12/create-bootable-usb-stick-from-iso.html)。   
- 这两种方法都不会访问和修改本来的系统和文件（除非你主动挂载）
- 开机画面可以选择不同的语言（目前有英语、简体中文、台湾正体、香港繁体和日文）

### 两款桌面
- Xfce：轻量版（1.0G） 
- MATE：完整版（约2.0G）

### 下载
其他不再贅述，上干货 _（下載後記得做校验；網盤服務商爲 Blaucloud.de 位於德國）_ ：

 --> [下載 LiveSlak](https://github.com/mdrights/LiveSlak#download)   
(系統的帳號/密碼：live/live     


### 设备要求  
_（可能有点罗嗦，但还是要提醒一下）_   
- 目前在 x86_64 架构（人话就是大家都在用的 Intel/AMD 64位电脑）运行；
- 如果你的电脑是UEFI启动的话，那很抱歉目前本系统有点 bug 还无法用 USB盘在这种系统上启动（传统BIOS的是可以的）；
- 鉴于这是 GNU/Linux 系统，有时遇到奇葩的 显卡（如Nvidia） 或 无线网卡 可能会缺少驱动，可能导致显示/网络问题（但在虚拟机里不受影响）
- （如遇这种情况请告诉我哈～发issue吧）   


### 内含软件
_（以上两款均有，并逐步添加更多 // 若你有需求或好想法可以告诉我～）_
- fcitx 輸入法 （按 `Ctrl + 空格` 激活；目前有拼音/双拼/五笔等）
- Shadowsocks-libev （执行：`ss-local -c <你的ss配置文件>`）
- ShadowsocksR 3.1.2 (在 `/opt/` ，请进入其两层目录后执行：`python local.py -c <你的ssr配置文件>`)
- Telegram 1.1.19
- icecat-hardened (浏览器增强版，在沙盒中运行）
- firefox-hardened (火狐浏览器增强版；已装了相应插件（普通版也有），启动时请点击允许）
- Tor浏览器 7.0.4 ( & Tor 0.3.0.10 )
- Tor-hardened （Tor 增强版，在桌面)
- NetworkManager 1.8.0 （网络连接工具，可点击`连接网络`图标连接WiFi（XFCE版），已MAC地址随机化处理） 
- firejail （沙盒，用于隔离应用软件）
- macchiato （MAC地址随机化工具）
- VeraCrypt （加密工具；当然 GPG 肯定是自带哒）
- keepassx （密码管理器；MATE版才有）
- proxychains （网络代理工具）
- privoxy （网络代理工具）
- 防火牆 iptables （在桌面）
- wipe, secure-delete （磁盘擦除工具 -注：一般仅对传统磁盘有效）
- testdisk  （数据恢复工具）

- 其他日常：
    - Libreoffice 5.4
    - XPDF, Astril （看PDF）
    - GVIM, Ted (文字编辑器）
    - docx2txt  （docx格式转换 txt）
    - Audacious (音频编辑）
    - GIMP （图像编辑）
    - Mplayer, GNOME-mplayer （视频播放）
    - xsane （扫描）

- 完整的[自添加软件列表](https://github.com/mdrights/LiveSlak/blob/mdrights/pkglists/mdrights.lst)


### 你的隐私最重要
**目前还是beta测试状态，请谨慎使用。**  
_本帖會持續更新，歡迎測試，今后会有更多强大特性喔，祝使用愉快～_   

### 源码 & 许可
本人构建自己魔改的 LiveSlak 的代码在：[https://github.com/mdrights/LiveSlak](https://github.com/mdrights/LiveSlak)  
LiveSlak 作者的代码在：[http://bear.alienbase.nl/cgit/liveslak/](http://bear.alienbase.nl/cgit/liveslak/)  
**欢迎参与协作 ;-)**   
**交流反饋**：到 [LiveSlak repo](https://github.com/mdrights/LiveSlak) 發個issue；或 IRC/Riot 頻道：#digitalrightscn; Telegram: https://t.me/slackware_unix      


=========  
Copyright 2014 - 2017 Eric Hameleers, Eindhoven, NL   
Copyright 2017 MDrights <mdrights at tutanota dot de>   
All rights reserved  

只要本版权声明和许可声明出现在所有版本的本软件中，本软件即可被允许以任何目的（有偿或无偿地）使用、复制、修改和分发。

=========

