---
title: 如何校验Signal apk包
---

## 如何校验Signal apk包

不知不觉，[Signal](signal.org)（端对端加密通讯软件）官方已经允许了直接下载和安装Signal (安卓版)了。即它不再一定依赖Google框架GCM。这对在天朝的安卓用户来说真是一大利好，23333。

下载地址：[https://signal.org/android/apk/](https://signal.org/android/apk/)  

然额，官方并不赞许这样做，在其下载页面打出了大大的「Danger Zone」（即危险区）。 

官方的一些理由（见其Github issues）是：  
1. 遇到报错很难排查  
2. 没有GCM后无法自动更新（进而不够安全）  
3. 发布的过程变得不严谨，容易被攻击者篡改  

官方给出了其apk包的 签名证书的SHA256校验值（注意是TLS证书，呵呵）。  
这里可能难道了非技术人士/极客了，校验方法在这里：

<!--more-->  

如果你是 GNU/Linux 用户 （已安装openjdk）：

	keytool -list -printcert -jarfile <Signal-xxx-xxx-xxx.apk>

如果你是 macOS 用户，命令同上。（打开终端窗口，去到你下载的 Signal apk包的位置然后输入以上命令）

但很可能你的系统没有安装 JDK(Java 开发工具) 所以没有上面的命令。 

可以这样安装 JDK：打开一个终端窗口，输入 `java`，如果系统没有安装JDK则弹出提示框，点击「更多资讯」就可以去到Java官网进行下载和安装了。 

然后再在终端里执行以上命令。

### 命令输出结果应是：

```
签名者 #1:

签名:

所有者: CN=Whisper Systems, OU=Research and Development, O=Whisper Systems, L=Pittsburgh, ST=PA, C=US
发布者: CN=Whisper Systems, OU=Research and Development, O=Whisper Systems, L=Pittsburgh, ST=PA, C=US
序列号: 4bfbebba
有效期为 Tue May 25 23:24:42 HKT 2010 至 Tue May 16 23:24:42 HKT 2045
证书指纹:
	 MD5:  D9:0D:B3:64:E3:2F:A3:A7:BD:A4:C2:90:FB:65:E3:10
	 SHA1: 45:98:9D:C9:AD:87:28:C2:AA:9A:82:FA:55:50:3E:34:A8:87:93:74
	 SHA256: 29:F3:4E:5F:27:F2:11:B4:24:BC:5B:F9:D6:71:62:C0:EA:FB:A2:DA:35:AF:35:C1:64:16:FC:44:62:76:BA:26
签名算法名称: SHA1withRSA
主体公共密钥算法: 1024 位 RSA 密钥
版本: 3
```
其中 **SHA256** 那行的一串数字应跟Signal下载页面上的数字完全一样，这个apk包就可以称得上完整、来源可靠了！

最后唠叨一句，为了自己、爱人和战友的安全，请勿相信网上随便传来传去的apk包 ：）

祝好
