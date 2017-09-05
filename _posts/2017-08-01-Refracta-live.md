---
date: 2017-08-01
---
<br />
<br />
<br />

## Refracta live 系统下載

**注：现在已更换到 [LiveSlak 系统](https://mdrights.github.io/os-observe/posts/2017/08/Liveslak-intro.html)，但本系统仍会不定期更新。**

本人其實自去年起便定期（每月）製作一份定製的live iso鏡像，採用的操作系統是之前介紹過的 Refracta GNU/Linux，它是基於 Debian GNU/Linux 的改版：Devuan 的一個分支版本（關於 Refracta 的介紹在 [這裏](https://mdrights.github.io/os-observe/posts/2016/11/Refracta-for-privacy.html)）。

這個發行版的一個好處是它有自己的 live iso 鏡像製作工具：[refracta2snapshot](http://www.ibiblio.org/refracta/documents.html)，可以方便地把Live系統加入自己需要的東西。那這玩意跟隱私/安全 有幾毛錢關係呢？

> 答案就是：live對系統的修改不會保存，每次重啓都會還原到製作時的狀態。這一招就能抵禦諸如木馬的駐留，最大限度降低它的長期破壞（比如網頁和電郵的附件夾帶的惡意程序）。對於打開不信任的網頁，和/或 打開別人發給你的可執行文件（包括各種文檔、pdf、exe、圖片、音頻等）時特別有效。live iso 放在虛擬機裏使用更加有效（避免對宿主機的破壞） 或 刻錄到 USB盤上隨身攜帶也非常方便。:-)

其他不再贅述，上乾貨（下載後記得做驗證；網盤服務商爲 Blaucloud.de 位於德國）：

[Refracta-20170904.iso](https://mdrights.blaucloud.de/index.php/s/fFKWLwIuhB8zd6w) 
  md5sum: e7ca092b213dbb3b9caede52e07cfc8f


9月鏡像已更新，包含：  

<!--more-->

- 系統更新
- ShadowsocksR (& Shadowsocks）
	- （python版：進入`~/shadowsocksr/shadowsocks`，執行 `python local.py -c <你的ssr配置文件>`）
- fcitx-googlepinyin 輸入法
- Libreoffice
- Riot-web
- Tor （包括命令行版 + Tor浏览器 7.0.4）
- Calibre （电子书阅读器）
- macchanger （MAC地址随机化工具）
- Poedit & translate-tools (协助翻译的强大工具）
- Firefox瀏覽器（带以下插件）
	- User-Agent Switcher，可以僞裝成別的瀏覽器和系統
	- Noscript
	- HTTPSeverywhere
	- ublock origin （防广告）
	- Privacy Badger （防第三方cookies）


_本帖會持續更新，歡迎測試，祝使用愉快_   

