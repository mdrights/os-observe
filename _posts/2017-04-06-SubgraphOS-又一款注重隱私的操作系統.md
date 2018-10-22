---
---

source: https://subgraph.com/blog/index.en.html

譯者：MDrights

> （譯者註）和Tails，Whonix一樣，Subgraph就是另一款主要通過代理來保證信息傳輸安全，以及在線匿名的平臺。「代理」主要是 Tor，VPN等等（選擇會多一些）。它和QubesOS 有着類似的「隔離以防範入侵」的理念，採用沙盒（sandbox）的方法。

（下面主要意譯自Subgraph的博文：**新特性：Alpha 3發佈**）

### 1. ROFLCoptor：Tor控制端口過濾和狀態追蹤守護進程

ROFCoptor 過濾Tor控制端口（control port）的流量。可以通過制定針對每個應用的安全策略來限制哪些行爲可以走控制端口。它還通過整合OZ來動態地端口轉發，以支持完全沙盒化的應用（這些應用通常都是短暫的Tor暗網服務，比如OnionShare， Ricochet，比特特幣核心服務等）  

### 2. OnionShare

Subgraph OS 現在支持OnionShare了。後者是通過Tor網絡來安全且匿名地傳送和分享文件的應用。它在OZ裏完全被「沙盒」了了，而且被整合進 Nautilus文件管理器了（在文件上右鍵點擊有「通過OnionShare分享」選項）。

### 3. Ricochet

Ricochet 是一款匿名的客戶端到客戶端（peer-to-peer）即時通訊工具。它和其他即時通訊工具的不同之處在於其他軟件使用中央服務器，它則是通過Tor暗網服務點對點地連接。它也是完全在沙盒OZ裏運行。 

### 4. Subgraph OS 手冊

我們把我們的技術文檔放在操作系統裏。從桌面便能讀到簡潔版本。完整版可以在這裏找到：/usr/share/sgos-handbook/  

<!--more-->

## 改進和bug修復

### 1. 我們的Grsecurity 內核更新到4.8.15 

最主要的修補是，該版本修復了“臟牛”漏洞(CVE-2016-5195)。我們之前給SubgraphOS的用戶通告了這個漏洞。現在在live版本也處理了。  

### 2. MAC 地址僞裝（失效）問題得到解決

從有記憶以來，Linux用戶一直用 `if-pre-up` 腳本來完成網卡的MAC地址僞裝任務。好幾年以前NetworkManager裏的一個修改破壞了這個功能。這個bug在 [2010年第一次報告](https://bugzilla.gnome.org/show_bug.cgi?id=600167)，一直沒修復直到兩年前。然而，這個通過nm-dispatcher執行的修復並沒有實際允許MAC地址的僞裝，只是對iptables規則的開啓比較有用。  

還是要感謝有一個可靠辦法來在啓動時進行僞裝，就是通過udev可以完成。近來，NetworkManager開始用它自己的新的MAC僞裝技術。但是，這些並不總是有效，且還把udev的相關功能給繞過了。幸虧上游這次很快進行了修復。也就是說我們的udev機制又可以正常工作了。這樣就只要在NetworkManager配置中“預留”僞裝的MAC地址（選項）即可。  

### 3. OZ中的音頻問題解決  

我們更新了PulseAudio配置，更正了「被沙盒」的應用中的音頻問題。  

### 4. 應用防火牆改進  

防火牆現在包含了“系統”規則，這些是SubgraphOS的默認規則。此外防火牆的UI（用戶界面設計）也得到改進。  

### 5. OZ 現在包含了對動態轉發的支持  

動態轉發器是在OZ沙盒裏運行服務器的基礎性工作。這樣我們有能力支持那些在沙盒裏運行Tor暗網服務的應用，如OnionShare, Ricochat, and Bitcoin core。從客戶端進來的連接全都轉發到沙盒裏。 

### 6. APT 簽名驗證漏洞解決

Subgraph OS 只有x64架構，因此不受影響。  

### 7. 與gosecco對接 

在這一發佈中我們整合了一個新的Go（語言）seccomp-bpf庫，由ThoughtWorks Tiger team 開發。[Gosecco](https://github.com/twtiger/gosecco) 讓我們可以爲沙盒應用創建有表達力和有效率的規則。  

### 鳴謝

（略）


**(譯者註——什麼是OZ）**  

就是 Subgraph團隊開發的沙盒管理守護進程和客戶端（是該系統的核心組建）。它還正在開發中，見[github repo](https://github.com/subgraph/oz)，看上去應該挺好用的（不過截至2016年底還在重度開發中……）  

他們的圖巧妙地畫出了Subgraph OS 的基本防禦原理（其實大多數安全型系統都是類似的功能）：

![SubgraphOS structure](images/sgos.png)

