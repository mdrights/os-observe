---
tags: trans,tails
---


_非官方試翻譯_

[原文地址](https://tails.boum.org/news/report_2017_04/)

- 新發佈
	- [Tails 2.12 已在4月19日發佈](https://tails.boum.org/news/version_2.12/index.en.html)（大版本發佈）
	- [Tails 3.0 計劃於6月13日發佈](https://tails.boum.org/contribute/calendar/)

- 在2.12中引入了如下更新：
	- 我們把 GNOME sound recorder（一個簡單的聲音記錄器）再次安裝了進來，這樣可以對更複雜的 `Audacity` 做個補充。用該應用做的錄音會存放在 `recordings` 目錄。
	- 我們移除了 I2P（另一種匿名網絡），因爲我們很不幸沒能找到一個在Tails維護該應用的開發者。爲Tails維護、整合 I2P 需要大量的時間和精力，而我們團隊還有更重要的事情要忙。
	- 把 Linux（內核）升級到 4.9.13。這將增強對新硬件（顯卡、wifi等）的支持。

<!--more-->

- 文檔和網站
	- Cody Brownstein 寫了文檔：[如何修改一個加密分區的密碼](https://tails.boum.org/doc/encryption_and_privacy/encrypted_volumes/index.en.html#change)。  
	- Cody Brownstein 寫了文檔：[如何刪除 勾掉了永久存儲功能的文件](https://tails.boum.org/doc/first_steps/persistence/configure/index.en.html#deselect)。
	- 我們添加了這個指引：[如何從 MacOS El Captian 及以後版本 燒錄DVD](https://tails.boum.org/install/mac/dvd/index.en.html#burn-dvd)。

- 用戶體驗
	- 我們收集了一些[用戶訪談](https://tails.boum.org/blueprint/intercept_interviews/)，以更好地瞭解我們的用者，他們的需求，喜歡Tails的什麼又不喜歡什麼。我們還收穫了來自西歐、北美、拉丁美洲和非洲的精彩故事。

- 基礎設施
	- 我們把部分系統升級到了 Debian 9 （Stretch），爲了在其成爲穩定版本之前定位遺留問題。
	- 我們在我們的持續集成系統上的 Vagrant 構建系統有了好的進展，讓其更配合開發者的使用。這也是我們在 Reproducible ISO構建方面的一部分工作。
	- 我們在面試一位想加入我們系統管理組的人。
	- 我們設計了一個機制 [用來分發和激活Tails簽名密鑰的廢止證書](https://tails.boum.org/doc/about/openpgp_keys/signing_key_revocation/index.en.html)。現在已部署並讓23個人共享了這個廢止證書（revocation certificate)。它能允許廢止（當前）的Tails簽名密鑰（GPG key），以應對非常壞的事情發生，但同時又讓單個人和惡意的人在不需要的時候廢止簽名密鑰變得很難。我們仍然鼓勵專家來檢查這個機制，也鼓勵其他項目採取類似的機制。

- 資助
	- 我們準備加一個 [合作者頁面](https://labs.riseup.net/code/issues/6972)到我們網站上，並在與幾個潛在的集團贊助者溝通。
	- 我們仍在與 OTF 討論我們的項目書，並根據相關情況重新工作相關項目。
	- 我們被 [MIT 媒體實驗室 不服從獎](https://www.media.mit.edu/disobedience/) 提名。

- 翻譯進展（略）
