---
date: 2017-09-17
---

## 软件使用方法

### 基本需求
- fcitx 輸入法 
	（按 `Ctrl + 空格` 激活；目前有拼音/双拼/五笔等）
- NetworkManager 1.8.0 
	网络连接工具，如果你是用 USB盤啓動本系統的 XFCE版，請点击 `连接网络` 图标连接WiFi；用虛擬機方式或其他版本則不必
	（另，已MAC地址随机化处理可更好保護你的網上身份） 
- icecat-hardened 
	(浏览器增强版，在沙盒中运行，並在首頁可選擇禁止Javascript運行等；特別推薦使用icecat，如網頁顯示有問題再轉 firefox吧~）
- firefox-hardened 
	(火狐浏览器增强版；已装了 Noscript, HTTPSeverywhere 插件（普通版也有），启动时请点击允许；〔警告〕請謹慎安裝第三方插件！）
- Libreoffice 5.4
	(文檔編輯套件）

### 穿牆
- Shadowsocks-libev 
	（执行：`ss-local -c <你的ss配置文件>`）
- ShadowsocksR 3.1.2 
	(進入 `/opt/shadowsocksr/shadowsocks`, 执行：`python local.py -c <你的ssr配置文件>`)

### 隱匿/隱身
- Tor浏览器 7.0.4 ( & Tor 0.3.0.10 )
	（記得啓動時，第一個問題選否（我們不用網橋），第二個選是（我們走本地代理），在Socks5處選擇 127.0.0.1 端口 1080 ）
- Tor-hardened 
	（Tor 高級模式，功能包括：
	1. Tor 會以 Stream Isolation模式開啓——即每類應用走不同的通道，避免監視者能通過對同一迴路的流量進行比對關聯而識別出用戶的身份（具體請自行谷歌~
	2. 開啓帶有特別規則的防火牆，讓本地所有DNS請求強制走 Tor 隧道，避免 DNS 泄漏。啓動防火牆時需要輸入用戶密碼 `live`；  
	3. 該防火牆還可讓本系統成爲 “洋蔥”網關（路由），讓在同一網絡中接入本機的機器流量完全/強制走 Tor隧道而不會出現泄漏（當然，還需要其他設置，如 ip_forward, 開啓DHCP server……恕不贅述）；
	4. 開啓 sandbox沙盒模式 （待完成）
 
### 安全地聯繫
- Telegram 1.1.19
	(記得可在設置裏設置 socks（1080）代理（翻牆），或 Tor（9050））
- Jitsi 
	(加密視頻通訊軟件，cinnamon版才有；記得開啓 OTR 和 ZRTP 加密協議)
- Hexchat
	( IRC 客戶端，無需註冊帳戶；記得本軟件最好要 **走代理（包括翻牆或Tor）再用** ！！！否則會直接暴露你所用的 IP 地址，別說我沒提醒你蛤~）

### 加密大法
- VeraCrypt 
	（加密工具；）
- keepassx 
	（密码管理器；cinnamon版才有）
- GnuPG 2.1 
	(当然啦， GPG 是每個 GNU/Linux 都自带哒）

### 毀屍滅跡 vs 反毀屍滅跡
- wipe, secure-delete 
	（磁盘擦除工具 -注：一般仅对 **传统磁盘** 有效）
	（在終端，`wipe` 接你要刪除的文件/目錄 即可；
	secure-delete 則包括：
	`srm`: 反覆擦除文件/目錄
	`sfill`: 填充磁盤或某個目錄的可用空間（一般耗時很長.avi）
	`smem`: 擦除內存中的數據（不知要等到什麼時候.jpg）
- testdisk 
	（数据恢复工具，在終端裏輸入 `testdisk` 即可按提示操作）

### 一些高級用法
- 防火牆 iptables 
	（在桌面點擊，會要求輸入用戶密碼 `live`；配有一些簡單的規則）
- firejail 
	（沙盒，用于隔离应用软件，執行 `firejail` 後接你想要運行的程序即可）
- macchiato 
	（MAC地址随机化工具; 進入 `/opt/macchiato/`，執行 `macchiato` 看看）
- proxychains 
	（网络代理工具；用於在終端中讓程序走代理，已設成 本地的 1080 端口）
- privoxy 
	（网络代理工具, http 代理服務器；用於讓本地和同網的其他機器的流量走代理（像全局一樣）~（還需進一步設置））

### 其他日常：
    - XPDF, Astril （看PDF）
    - GVIM, Ted (文字编辑器）
    - docx2txt  （docx格式转换 txt）
    - Audacious (音频编辑）
    - GIMP （图像编辑）
    - Mplayer （视频播放）
    - xsane （扫描）

（還會陸續添加喔，如你有好點子，歡迎告訴我~）

更新日期：{{ page.date | date: "%B %e, %Y" }}
