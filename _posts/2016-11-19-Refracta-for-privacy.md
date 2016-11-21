---
---

## Refracta: 定制自己的Live系统

之前我自己一直折腾着Tails, Whonix, 等与隐私和信息安全相关的系统，他们要么在用户友好易用性不够满意，要么对系统性能要求较高，我们还是需要继续折腾，满足用户对隐私和易用的双重需求。

著名的_网红_程序员，“[编程随想](https://program-think.blogspot.com/)”，写过很多很好的教程，各个领域的包括教大家如何用虚拟机保护自己的信息，你感兴趣，可移步[这里](https://program-think.blogspot.cz/2010/04/howto-cover-your-tracks-0.html)。

今天我想添油加醋的是，再推荐一款易用性更好，更容易复制推广的系统，[Refracta/Devuan](http://ibiblio.org/refracta)。跟前两者一样是基于Debian的。

它有这样的优势，

- 对虚拟机做了优化，比如自带适合虚拟机vbox的包和驱动，不必自己折腾一些诸如屏幕大小，分辨率，文件共享等狗血琐碎的问题了，节省时间，  
- 他们团队原创的杀手锏，refracta2usb, refractaSnapshot，极其有用。注重隐私的朋友应该会注重使用live系统，是防范木马恶意软件的大招。这俩工具就是让你毫不费力地在当前系统装好自己要用的软件，配置好系统，制作成iso，就可以在虚拟机里或USB盘里用起来了，它可以让你不怕被悄悄安装恶意软件，下次启动就会恢复原样。不然的话你需要去研究live-build的工作方式，顺利的话chroot进去定制你的系统。  
- 好用的沙盒，firemenu，作用是隔离你不信任的软件/文件，比如你接受到了一些二进制文件，包括doc, pdf,jpg, deb等文档或程序安装包，打开它怕被悄悄安装恶意软件，那就在沙盒下的相应软件中打开它。
- 最小化安装。无systemd, 甚至可以不要DBus（但输入法就用不了了)。
- 最后但最重要的，它是开源自由的系统，代码完全公开，接受所有人检验。它甚至连闭源的硬件驱动程序都没有，这也令它有时在有些机器上有些问题，但在虚拟机里就不存在这个问题了。
- 当然你也可以说，要“彻底”防范恶意软件的入侵和漏洞利用，还需要内核加固（如装备Pax/Grsecurity）。这当然好，不过这对非技术人士的门槛就比较高了，且需要每次更新内核时手动装备（个别发行版除外）。这里有一些比较小白的教程可以参考: [Hardenedlinux.org](http://hardenedlinux.org/system-security/2016/01/10/hardening-your-desktop-linux-mint-with-grsec.html)，和[这里](http://hardenedlinux.org/system-security/2016/08/10/grsec-kernel-full-commentary.html).
