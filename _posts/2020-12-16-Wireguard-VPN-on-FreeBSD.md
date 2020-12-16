---
title: 在FreeBSD搭建 Wireguard VPN (Server)
date: 2020-12-16
---



# 在FreeBSD搭建 Wireguard VPN (Server)

> 这是我今年最开心的事情之一。另[有一篇](https://mdrights.github.io/os-observe/posts/2020/12/install-wireguard-on-alpine-armv7.html)讲 Wireguard 的客户端的安装（其实不大区分 client-server，而是都可叫 peer）。搭建过程还是比较简单的，望有用！  


## 安装 Wireguard 包

FreeBSD/amd64 系统有编译好的包可直接用：

```
  pkg install wireguard-1.0.20200319_2 wireguard-go-0.0.20200320
```

把配置文件（必须命名成类似`wg0.conf`）放到`/usr/local/etc/wireguard/`  


## 设置成 rc 服务

这样可以开机自启。  
```
  vi /etc/rc.conf

  wireguard_enable="YES"
  wireguard_interfaces="wg0"     # 须跟配置文件主体部分一致
```

## 开启内核端口转发  
```
  sysctl net.inet.ip.forwarding=1

  vi /etc/rc.conf

  gateway_enable="YES"
  ipv6_gateway_enable="YES"
  pf_enable=yes              # 把防火墙也启用
```

## 配置防火墙  
```
  vi /etc/pf.conf

	ext_if="vtnet0"
	wg_if="wg0"
	localnet=$wg_if:network

	nat on $ext_if from $localnet to any -> ($ext_if)

	pass from {lo0,$localnet} to any keep state
	pass in inet proto tcp to ($ext_if) port ssh
	#pass in on $ext_if proto tcp to ($ext_if) port 80   # 如果你有其他服务就记得开启
	pass in inet proto udp from any to any port 1153

  service pf start
```

## 启动 Wireguard
```
  service wireguard start
```

## 结论
小功告成～ 如果怕 wireguard 中途挂了的话，可以用一些工具监控并重启之（如`monit`）。  

祝你的基本自由长青！

