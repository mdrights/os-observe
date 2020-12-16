---
date: 2020-12-06
---



# 在树莓派2的 Alpine Linux 安装 Wireguard VPN

> 缘起：这篇博文是想纪念一下我安装 Wireguard client（以下简称`wg`）的故事。事情不大却来回折腾许久，很值得纪念，毕竟这个需求是我一个很刚的需求（甚至是当时出发时就揣在怀里的）。如今我终于享用到了树莓派2 （`rpi2`）上这个轻巧、配置简单且安全的 VPN，透明代理（其实直接当作路由器了）给我们的其他设备，畅游网络，保障我仅剩的这点儿资讯/新闻自由。  
Wireguard 的 server 端就比较简单轻松了，见[我的安装记录](https://mdrights.github.io/os-observe/posts/2020/12/Wireguard-VPN-on-FreeBSD.html)。  

## 0x00

上面已讲到“为什么是 wg”了，再说下为什么是 RPi2 + Alpine Linux。主要还是我特别想用 wg —— 而不是 socks 代理（我已有好多了）—— 能全局隧道的那种，迫使 client 设备必须走隧道，无法绕过 socks 代理。现在家里只有：一个 RPi2，一个 RPi3 （但似乎电源有问题），和一个 Orange Pi One （已安装 NetBSD）。那么这个 Rpi2 正在跑 FreeBSD，虽然它很稳，我用它开设 socks 代理和 dnsmasq DNS 解析服务。但 FreeBSD on armv7 有个大问题是，pre-built 的包没有 wg （维护者没打这个架构的包），甚至连 go1.14 都没有（需要自己编译，但我编译时机器资源不足直接 killed 了）。因此只能暂时放弃 FreeBSD，换 Alpine （只是正好瞄到它们有 wg 的包，且 Alpine 的无盘模式也很喜欢，极小的磁盘操作，减少磁盘损坏的概率）。  

<!--more-->

## 1x00

Okay, 首先先安装 Alpine Linux。作为它的老朋友，安装还是很容易的。可参见[官方文档](https://wiki.alpinelinux.org/wiki/Raspberry_Pi)  
- 把RPi2 要用的 microSD 卡建好分区表（dos），新建 FAT32 分区，用 msdosfs 格式化；
- 把 Alpine tar 包解压到上面即可拿去启动了。

## 2x00

新系统首次进入（必须得从本机登入，不能 SSH），打理一下（略）。  

安装 wg，
```
  apk add wireguard-rpi2
```
报错，跟这里一样：[https://lists.alpinelinux.org/~alpine/users/%3CCAEhkKgV-OdZ8y406_yynrH7tcxjgXkKzSc6dCSZ_a6CUPUfBiA@mail.gmail.com%3E](https://lists.alpinelinux.org/~alpine/users/%3CCAEhkKgV-OdZ8y406_yynrH7tcxjgXkKzSc6dCSZ_a6CUPUfBiA@mail.gmail.com%3E)  

幸好遇见了这个帖子，真是感谢这位老兄！XD  

原来 Alpine/arm 默认 diskless，把 kernel+modules 用 squashfs 打包了，启动后只读解压。因此启动后再安装内核模块什么都装不上了（其实我的`antiS`里的 wg 也有这个问题！也一起解决了！哈哈）。  

上面那位大神在最后给出了很 hackful 的解决办法：

```bash
# Remount kernel module dir /.modloop as an overlay, to allow writing
modprobe overlay
mkdir -p /.modloop.lower /.modloop.upper /.modloop.workdir
mount /dev/loop0 /.modloop.lower
umount /.modloop/
mount -t overlay -o 
lowerdir=/.modloop.lower,upperdir=/.modloop.upper,workdir=/.modloop.workdir 
none /.modloop
lbu include /.modloop.upper
lbu commit -d

# Manually get the Wireguard kernel module to avoid installing
# the wireguard-rpi2 which does not work with diskless systems
cd /tmp
pkgname=$(apk list | grep wireguard-rpi2 | cut -d " " -f 1)
wget http://dl-cdn.alpinelinux.org/alpine/v3.12/community/armhf/$pkgname.apk
mkdir /tmp/$pkgname
tar -xzf $pkgname.apk -C /tmp/$pkgname
mkdir -p /lib/modules/$(uname -r)/extra/
cp /tmp/$pkgname/lib/modules/$(uname -r)/extra/wireguard.ko \
   /lib/modules/$(uname -r)/extra/
rm -fr /tmp/$pkgname /tmp/$pkgname.apk
depmod

# Create an init script to remount the /.modloop overlay on next boot
cat > /etc/init.d/modloopoverlay <<EOF
#!/sbin/openrc-run

depend() {
     before networking
     need modules
}

start() {
     ebegin "Starting modloop overlay"
     modprobe overlay
     mkdir -p /.modloop.lower /.modloop.upper /.modloop.workdir
     if [ ! -d /.modloop.lower/modules ]; then
         mount /dev/loop0 /.modloop.lower
     fi
     umount /.modloop
     mount -t overlay -o 
lowerdir=/.modloop.lower,upperdir=/.modloop.upper,workdir=/.modloop.workdir 
none /.modloop
     eend 0
}
EOF
chmod +x /etc/init.d/modloopoverlay
/etc/init.d/modloopoverlay restart
rc-update add modloopoverlay boot
lbu include /etc/init.d/modloopoverlay
lbu commit -d
```

也就是把原来用squashfs 挂载的包，卸载再用 overlayfs 挂载。  

ps. 当然，如果以后 Alpine 用 5.6 的内核就不需要这么麻烦了，内核自带 wg 模块了。现在还是 5.4。  


## 3x00

Okay, 本以为大功告成，结果用`wg-quick`一看，还是不行。VPN 内网并没有通。检查来检查去，怀疑是`ifconfig`工具有问题。  

Alpine 不用 GNU 那套，连`ifconfig`都是`Busybox`的！ 所以干脆换回常规 Linux 发行版大家都用的那个吧（因此在这里劝告新手们还是先别碰 Alpine Linux 啦～ 很多工具虽然命令一样但都是简化版的）。  

来一发：  
```
  apk add net-tools
```

然后记得改 PATH 环境变量，把 /bin 提前（可能还需要重启一下）。  

然后再`wg`，ping 你的 server 端的 ip，nice！


## 4x00

最后，现在 RPi2 可以自由连接互联网了。我们还需要把它变成「软路由」，帮别的设备上网。只需简单两个命令打通防火墙 NAT 和 ip 转发：  
```
  iptables -A POSTROUTING -o wg0 -j MASQUERADE  
```  
```
  sysctl -w net.ipv4.ip_forward = 1
```  
（注：这两个命令只对本次运行有效，生效的持久化方法请自行搜索）  


这样，才算大功告成！  

当设备要连接时，只需要在其 WIFI 设置本网热点的时候，把 RPi2 的 IP 设置成该热点的网关（gateway）即可，这样所有流量都走 RPi2了。


## 小结

总是好事多磨，现在总算有了完美的解决方案。本来是想在 ×BSD 上部署这些的，可惜各有各的原因：FreeBSD/armv7 没法构建 Go；OpenBSD/arm 的安装太麻烦复杂了（但它内核已经带了wg了，x86 上是很方便的，已安装）；NetBSD 的 pkgsrc 也没有可用的 wg 脚本…… 不过 Alpine 也很优秀 :D  

为了自由的资讯获取，这是花了多少时间和精力啊！ 当然即使资讯已经自由，也需要保障隐私，这事也还是要做，还有很多很多要做。  

今后看看有没有可能把这个做成一个 project （如：freedombox）。  
