---
---

## 反“棱鏡”-隱私加強的軟件庫

在此向大家介紹一些軟件的隱私加強版，這些軟件（當然都是自由開源的）來自 [Parabola GNU/Linux-libre](https://www.parabola.nu/) ——一個基於Arch GNU/Linux 的純自由軟件的操作系統。他們的軟件倉庫全部都是符合 [GNU 的自由操作系統指導意見](http://www.gnu.org/distros/free-system-distribution-guidelines.html)的。而本站想推介的是，他們的軟件倉庫裏非常有趣的一個子倉庫：[Nonprism](https://wiki.parabola.nu/Nonprism)，裏面的軟件都是經過維護者和社區的修改，去掉了可能引起隱私/個人信息泄漏、元數據/指紋暴露和其他漏洞等低級別的協議。

本站特地學習了每個軟件所修改的地方，並親自編譯安裝。改動的地方其實主要包括：以sandbox方式（iceweasel / icedove) 或 以 chroot方式（Tor）運行；去掉一些不安全的/閉源的/有過監控之名的 協議/服務（如Google相關服務/Skype...等等）；etc....

Nonprism 的 PKGBUILD 在這裏：[https://git.parabola.nu/abslibre.git/tree/nonprism](https://git.parabola.nu/abslibre.git/tree/nonprism)

今後還會不斷增加更多能夠做到去掉不安全部分的軟件。它還自帶一個 [your-privacy](https://www.parabola.nu/packages/nonprism/any/your-privacy/) 工具，可以檢測系統是否安裝了有隱私泄漏風險的軟件，並提示卸載換成 Nonprim 倉庫裏的修改版本。

<!--more-->

直接使用已編譯好的安裝包，用pacman安裝也是可以的。只要在你的 `/etc/pacman.conf` 加入:

```
[nonprism]
SigLevel = PackageRequired
Include = /etc/pacman.d/mirrorlist
```

然後升級：

```
# pacman -Syu
```

> 注意：nonprism repo 需要放在其他 repo 上面，這樣才能優先安裝 nonprism 裏的版本。
