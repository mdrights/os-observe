---
layout: post
---

> 题注：作为本博客的第一篇文章，这竟然是关于隐私和匿名上网技术的笔记。它是我自己的操作，稍带创新的吧（这几年其实很多操作都值得记录，但皆觉得都是从谷歌或各大wiki中学来的，才没记下来……）。  

这篇文章是我怎样Hack Whonix（[Ta是什么？](https://whonix.org)）。所谓“Hack”并不是真正的去黑，而是“把它搞明白、弄清原理，或改装它”的意思。  

Whonix是出色的用于匿名和隐私保护的操作系统（基于著名而伟大的[Debian GNU/Linux](https://debian.org)）。匿名和开源社区有何关系？待我以后展开，此处一句话带过：开源（其实应该叫“自由”，[Free as in Freedom](https://www.gnu.org/philosophy/free-sw.html)）的本质应该是（对自己电脑等设备）的自主和信息的自主。

**进入正题：**  

**我的需求和目的**: 由于Whonix其实是虚拟机里的两台guest系统（gateway & workstation，来确保所有的流量都走Tor），这虽然很有效，不过也牺牲了机器性能（不是所有机器都带得动两台虚拟机，连Macbook air都发烫），还有便利（麻烦不）。而我平时其实并没有这么严格的匿名要求，但又想依赖人家精心调好的系统（至少专业地审计过）。因此需求来了，决定改它这系统，_成为只允许走socks（如Shadowsocks）隧道的系统_。

**第零步**

研究了Whonix（Workstation）的[技术详情](https://www.whonix.org/wiki/Design)和它的Github（自己搜，其实就是它的主人在Debian的基础上加了一些脚本和配置文件）后，

第零步，是设置Workstation（ws）的虚拟网卡的连接方式（原先因为它是gateway的下级网络，不会直接和HostOS连接的）——设成NAT模式，并在ws里interface那儿设置静态IP，跟虚拟机（我的VirtualBox）在同一网段，网才能通。见：

```
auto eth0
iface eth0 inet static
	address 10.0.2.11
	netmask 255.255.255.0
	gateway 10.0.2.2
```

**第一步**

就是要破解它对非Tor流量的封锁。辣么，Whonix-ws其实没有默认启动firewall（是gateway负责firewall对流量的管控，然而我不打算用gw：）。它封锁的原理是这样的（精准的说法请去其网站看原文：）：在gw的firewall的OUTPUT链允许指定的Tor（SocksPort）端口让其出门，而且真正的Tor开在gw上；在ws上开一个服务（叫`tor.anondist`，其实它是个脚本，里面会启动`/usr/lib/anon-ws-disable-stacked-tor/dummytor`，也是一个它主人自己写的脚本），它是个假Tor，专门开和Tor一样的端口（如9050,9150）；然后还有一个服务（叫rinetd, 其实还有socat），它来转发所有设置了9050等代理端口的软件的流量，转到gw上相一致的Tor端口上（那是真的）。这样ws上的软件（包括TorBrowser都以为本机上有Tor在开启，就防止了`Tor over Tor`的发生。（当然更具体的原理请读一读它主人的脚本，在/usr/lib/anon-* 那四个目录都是）对了，还有，它主人可能是为了“蒙混过关”或者防止用户/软件自己下载Tor，在/usr/bin/设了个软链叫`Tor`，指向`Tor-anondist`，而真的Tor（如果下载了的话），会把它改名为`Tor.anondist-orig`（具体如何自动化做的还不清楚）；而为了让这个假的Tor启动，systemctl里Tor的配置文件都写着`/usr/bin/true`，呵呵呵。

**这就简单了，禁掉rinetd，把/usr/lib/anon-* 的文件都`chmod -x`。想开机自启动的就把/lib/systemd/system/tor.service里的程序路径改为真的Tor的路径。**  

**第二步**

（前面好像不只一步耶）当然就是在ws开启iptables防火墙，只允许你想要的流量/端口。Iptables就不再具体介绍了，我默认你会。我的是：
```
Chain INPUT (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 2819 1060K ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           
  902  868K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 2748 1049K ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0           
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp spt:<socks>
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp spt:12345
  869  126K ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:<your server ip>
   71 10914 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:12345
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:<socks>
```

然后你想只允许目标端口为80,443的才能被转发，可以：
```
Chain OUTPUT (policy ACCEPT 59 packets, 3068 bytes)
 pkts bytes target     prot opt in     out     source               destination         
  217 11284 ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0           
    0     0 REDIRECT   tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:443 redir ports 12345
    4   208 REDIRECT   tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:80 redir ports 12345
```

**第三步**

当然，我们需要一个http代理服务器做端口转发，比如`redsocks`，它开了tcp 12345端口。

**第四步**

当然，去安装部署Shadowsocks啊，网上教程大把，就不赘述啦～

什么，好像还有第五步？你会发现没给DNS留口子啊。是的，你可以用一些DNS协议代理/转换的工具，如`DNS2socks`（TBC）、dns-tcp-proxy等。但我比较懒了，反正这个系统不会被用来上太多的网站，常去的几个也可以直接把IP地址写在hosts里；最重要的还是尽量利用ss、Tor自带的remote DNS 更为安全（DNS是古老而明文的，小心）。如果你在这里只用浏览器操作，那是相当简单的（如Firefox在代理设置里可直接勾选`remote DNS`；如果你在命令行，如git，那需要`proxychains`、`torsocks`等等。这样就不用专门开53端口咯！

**总结**

似乎折腾到这里，这个改装后的Whonix应该可以用了（如果我漏了什么欢迎提醒）。这样，你既能用Tor（一般要前置代理或网桥），也能退一步只用翻墙软件（考虑墙国的网络环境）做隐私方面的自我保护。  
当然（还是当然），这里要提醒一下，网络匿名、匿踪、隐匿真实身份是个艰深而长期的工作，不是光靠tor就能一劳永逸的；不只与技术有关，还跟个人的使用操作习惯、个人的生理、语言特点/风格、社会关系等等有关。这门学问要学习的话请移步[Whonix wiki](https://www.whonix.org/wiki/Documentation)。

Happy hacking, Happy being anonymised!



