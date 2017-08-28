---
date: 2017-08-28
---

## 私人定制隐私加强的 live 操作系统：LiveSlak

### 缘起

本人其實自去年起便定期（每月）製作一份定製的live iso鏡像，採用的操作系統是之前介紹過的 Devuan GNU/Linux，它是基於 Debian GNU/Linux 的，现在本人仍会不定期更新它，可看看 [以前的博文](https://mdrights.github.io/os-observe/posts/2017/08/Refracta-live.html)）。

从今年（2017）9月开始，本人更换了平台，基于 [Slackware Linux](http://slackware.com) 制作 Live 系统，代码来自 [Alien Bob 的 LiveSlak](http://www.slackware.com/~alien/liveslak/)（感谢！）。 **目前还是beta测试状态，请谨慎使用。**  

它主要有这样的特性：   

- 面向中文用户 __（似乎是仅有的中文化的隐私向操作系统）__  
- 安全/隐私类软件，并由本人亲自编译、打包（确保代码的安全和系统的纯净）
- 内核及系统加固（TODO）
- live 系统：

<!--more-->
> live系統的修改，和用户产生、放置的所有文件都**不會保存**，每次重啓都會還原到製作時的狀態。這一招就能抵禦諸如木馬的长期駐留，最大限度降低它的破壞。

### 为什么需要它
我们终端用户受到的黑客攻击/监控，最主要的方式就是 網頁和電郵的附件夾帶的惡意程序/木马啦。而木马等恶意程序最容易藏在我们日常最常用的office文档啦、PDF啦、图片啦音视频啦等二进制文件，大部分用户基本没有能力分辨其中是否有木马，但又必须每天都要下载和打开这些文件，风险非常大，对不对？

那么你需要 live 操作系统^ ^。当需要打開網頁，和/或 打開別人發給你的文件（包括各種文檔、pdf、exe、圖片、音頻等）時，木马只会在当前环境中存活，它看到的只是当前空空如也的系统，并随着机器的关机而消失。

### 用法
1. 放在虛擬機 （如 Virtualbox）裏使用，或 
2. 刻錄到 USB盤上，插入任何一台电脑 __（还可以隨身攜帶哟～ 不过留意有的电脑无线网卡可能不支持 Linux喔，如果你遇到了请告诉我喵～）__

### 下载
其他不再贅述，上干货 __（下載後記得做校验；網盤服務商爲 Blaucloud.de 位於德國）__ ：

[LiveSlak-mdrights-beta-0828.iso](https://mdrights.blaucloud.de/index.php/s/1TBusYed5Qbu5ah)   
md5sum : 90560f2156f5093db80cccca7c82cd7b


### 内含软件
	（逐步添加 // 若你有需求或好想法可以告诉我～）
- 系統更新
- Shadowsocks-libev （执行：`ss-local -c <你的ssr配置文件>`）
- fcitx 輸入法 （按 Ctrl + 空格 激活）
- NetworkManager 1.8.0 （网络连接工具，自带MAC地址随机化工具） 
- macchiato （MAC地址随机化工具）
- icecat-hardened (浏览器增强版，暂时需要从终端启动）
- proxychains （网络代理工具）
- privoxy （网络代理工具）
- firejail （沙盒，用于隔离应用软件）
- Tor 0.3.10 （包括核心程序 + Tor浏览器 7.0.4）
- Tor-hardened （Tor 增强版，暂时需要从 `/opt/tor-hardened/` 启动)
- 其他日常：
    - Libreoffice 5.4
    - XPDF
    - GVIM (文字编辑器）
    - Audacious (音频编辑软件）
    - GIMP （图像编辑软件）
    - MPlayer （视频播放软件）

### 你的隐私最重要
**目前还是beta测试状态，请谨慎使用。**
_本帖會持續更新，歡迎測試，今后会有更多强大特性喔，祝使用愉快～_   

### 源码 & 许可
本人构建自己魔改的 LiveSlak 的代码在：[https://github.com/mdrights/LiveSlak](https://github.com/mdrights/LiveSlak)
LiveSlak 作者的代码在：[http://bear.alienbase.nl/cgit/liveslak/](http://bear.alienbase.nl/cgit/liveslak/)
**欢迎参与协作 ;-)**

=========
Copyright 2014 - 2017 Eric Hameleers, Eindhoven, NL
Copyright 2017 MDrights <mdrights at tutanota dot de>
All rights reserved

只要本版权声明和许可声明出现在所有版本的本软件中，本软件即可被允许以任何目的（有偿或无偿地）使用、复制、修改和分发。
=========

