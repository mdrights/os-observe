---
title: 用 Tor 连接 IRC (Freenode)
---

用过 IRC 的人都知道，登录 IRC 后你的 IP 就会显露出来，咋整？（除非你跟管理员要 cloak）   

解决方法之一就是 用 Tor 来访问 IRC。有的 IRC 服务如 OFTC 一直支持从 Tor 网络访问，直接访问就好了。使用者最多的 Freenode.net 曾经不支持从 Tor 访问，后来可以了（见 [他们的问答](https://freenode.net/kb/answer/chat#accessing-freenode-via-tor)）。

当然用 Tor 网络直接访问 freenode.net 还是会被拒的，正确姿势就是要从 Tor 网络访问他们的暗网地址：

还要满足他们的条件，下面基本按照 [weechat 文档](https://www.weechat.org/files/doc/stable/weechat_user.en.html#irc_sasl_authentication)操作即可（其他 IRC 客户端的文档在[这里](https://freenode.net/kb/answer/sasl)：  

- 启动 Tor，在 weechat 里设置其代理走 Tor：   
	- 添加 Tor 代理：  
	`/proxy add tor socks5 127.0.0.1 9050`  
	- 添加 Freenode 的暗网地址：  
	`/server add freenode-tor freenodeok2gncmy.onion`  
	- 让 Freenode 走代理：  
	`/set irc.server.freenode-tor.proxy "tor"`  

- 使用 SASL 登录：
	- 我们选择这种密钥算法才行：ECDSA-NIST256P-CHALLENGE (Freenode 要求的两种算法之一)
	- 然后生成私钥：  
	`$ openssl ecparam -genkey -name prime256v1 -out ~/.weechat/ecdsa.pem`  
	- 生成公钥，并 base64 转码：  
	`$ openssl ec -noout -text -conv_form compressed -in ~/.weechat/ecdsa.pem | grep '^pub:' -A 3 | tail -n 3 | tr -d ' \n:' | xxd -r -p | base64`  
	- 连接 Freenode 并登录你的帐号，然后填入你刚刚生成的公钥（上一步的输出）：  
	```
	/connect freenode-tor
	/msg nickserv identify your_password
	/msg nickserv set pubkey <your-base64-string>   
	```
	- 配置 weechat 让其启用 SASL：
	```
	/set irc.server.freenode-tor.sasl_mechanism ecdsa-nist256p-challenge
	/set irc.server.freenode-tor.sasl_username "your_nickname"
	/set irc.server.freenode-tor.sasl_key "%h/ecdsa.pem"  
	```

- 开启 SSL：

（这里一个坑就是有的 Freenode 节点的 SSL证书有问题，这样让客户端不去验证就好了：   
在 weechat 开启 SSL：  

`irc.server.freenode.ssl "on"`

但是要 关掉 verify：  

`irc.server.freenode.ssl_verify "off"`  

- 开始你的 匿名 IRC 聊天吧：

`/connect freenode-tor`  


祝隐私保护愉快
