---
date: 2018-07-22
permalink: /antig/
---

## 又一款 live linux 系統：antiG

> 時隔一年，我的 live 操作系統再出新版！繼續在保護數字隱私、對抗監視和取證上努力。

`antiG` 的意思是 GFW 中的 G （可能還會有 antiF、antiW 呢）。  

Live 型系統的好處就不說了，之前已介紹過：[LiveSlak 系統介紹](https://mdrights.github.io/os-observe/Liveslak-intro/)。

<!--more-->

### 主要特性： 

- 基於 Devuan GNU+Linux, 軟件包豐富（大部分與 Debian 通用），軟件嚴謹可靠；
- 更好的 UEFI 支持（即能在更多的機器上啓動了）；
- 對虛擬機的支持更好（尤其是 virtualbox）；
- 儘量使用自由開源軟件，除了兩類軟件目前還做不到自由開源：
	- virtualbox 的客戶機插件  
	- 一些硬件的固件（如沒有的話很可能無線網卡無法工作）

### 使用者：
	主要面向的是有信息安全和隱私泄漏風險的、和/或正在受到強大對手（如黨國）監視和威脅的人士。  

### 設計用途   
	- 本系統在虛擬機上運行，打開來自郵件附件的或USB盤的文件，即使該文件有木馬也不會入駐到電腦硬盤的系統裏。  
	（刻錄到 USB 移動存儲設備上 效果相同且安全係數更高）
	- 防取證。在本系統上做的操作在系統關閉/重啓後都不復存在，不留痕跡。

### 主要軟件：
	- Shadowsocks
	- Shadowsocks-libev
	- Telegram
	- Signal
	- Firefox 瀏覽器
	- Pidgin （XMPP 通訊工具）
	- Hexchat （IRC 聊天室）
	- Riot.im（新型去中心化加密聊天室）
	- Tor 匿名瀏覽器
	- Torchat（基於 Tor 的聊天室）
	- uTox （基於 P2P 的去中心化加密通訊工具）

準備釋出 0.0.1 beta 版，還有不少功能/軟件會陸續添加 :^)

iso 鏡像請到 [sourceforge](https://sourceforge.net/projects/antig/) 下載，歡迎各種需求/問題，請到 [GitHub](https://github.com/mdrights/antiG) 發 issue，我會儘量滿足關於隱私保護、防範攻擊的需求~！

### 使用提示：

- ss 翻牆工具，請在 `/etc/shadowsocks/config.json` 填寫自己的服務器的相關信息。這樣點擊菜單裏的ss條目才可正確運行。由於live 系統無法保存配置，可以自己保存一份 config.json 覆蓋它。也可以從命令行執行： `sslocal -c <your config file>` 。    


Happy hacking
