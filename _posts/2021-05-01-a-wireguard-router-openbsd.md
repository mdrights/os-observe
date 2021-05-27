---
---



# 一个 OpenBSD 上的 WireGuard VPN 路由器

> 前面的博文已有一些记录，讲了我使用 Linux/BSD 系统的一个基本也是终极需求：数据自主、通讯私密。贯穿我的整个 FOSS 生涯中，我都在折腾一件事，搭建安全的 VPN。

那么之前我已经用 BSD （又）搭了一次 WireGuard VPN server，只要客户端（不管什么系统）安装了 WireGuard，都能安全地连接到 WG VPN了，呼呼。 可是还不够，我还想让没有条件安装 WG 客户端的设备也能访问我的VPN，那么我就需要有一个在本地 LAN 的 VPN router （路由器）啦。当然之前也做了一个基于 Alpine Linux 的 VPN router，性能不错，但也还不过瘾（此时的 Alpine 3.12 还没上 Linux 5.10 没有自带 Wireguard：）  

0x00

其实很简单，主要感谢 BSD 和 pf 的配置是有文档写得很清晰周到的（但得花时间去阅读和学习呀～～QAQ）。  

我现在已有一台旧 amd64 上网本可以用作路由器吧（而且体积小方便携带，可以在公共 wifi 环境下也能使用）

1x00

给内核开启 ip 转发：  
```
  net.inet.ip.forwarding=1
```

2x00

pf 防火墙设置 DNAT 地址转换，路由器会把内网要出去的包源地址改成它自己的地址，这样返回的包才能顺利回来。  

```
##    When using vanilla network      ##

pass out on egress inet from any to any nat-to egress

##    When using WireGuard tunnel     ##

#pass out on $wg inet from any to any nat-to $wg

```

这里有2个小插曲，一是：  
- 花了不少时间研究怎么做到当路由器只有1个NIC（网卡）时还能把内网流量转发出去，其实就上面这一句话（是我想太多啦）：把 egress 网口上来自 any 的流量都 pass out。

另一个：  
- 当 WG 开着的时候，应启用下面一行的命令，因为流量都在 wg 网卡上。如果把 WG 做成开机启动的话，应该是不麻烦的。
但如果 WG 会时开时关，就需要在这两行命令上来回切换啦（我比较懒没做）。  

所以如果你没有给 Wireguard 做开机自启动（或者不愿），那么需要将 pf.conf 设置成 vanilla 的（这样刚开机时才有网络，除非你不想）。等手动启动 Wireguard 后，再调用 wg 专用 pf.conf 文件。  

```
# wg-quick up wg0
# pfctl -f /etc/pf-wg.conf
```


3x00

好啦，viola，最后就只需要让设备连上跟 OpenBSD 路由器相同的内网，并**把设备上的网关（gateway）**设置为路由器的 IP (在你连上的热点的设置里)。  

Happy Connecting！
