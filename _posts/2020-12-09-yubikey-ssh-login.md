---
title: Yubikey 有大用处之：SSH 登录
date: 2020-12-12
---



# Yubikey 有大用处之：SSH 登录

## 缘起

`Yubikey` 若你知道它是什么的话，你应该晓得它对于注重信息安全和隐私的人们是多么重要啦～（此处不再赘述，可自己搜索搜索）  

它首先是个 U 盘（其实具体是个 USB 键盘格式），至少有这么些用处：  

- 一次性密码（OTP）生成器，为网上账号登录时提供验证码（强烈推荐！比短信验证码安全一万万倍）；
- 个人密码存储器；
- 个人数字身份的证明 （Oauth）；
- PGP 密钥存储器；
- SSH key 存储器。


## SSH key on Yubikey

下面我就介绍介绍怎么把 SSH 登录用的 key 私钥放在 Yubikey 里。  

好处：即可随身携带，在多台电脑登录我的 VPS，又免去了把私钥放在本地电脑而被取证的担忧。  

以下分两部分介绍，一是关于如何在 Yubikey 上生成 SSH 密钥对；二是如何用 Yubikey 里的 key 来 ssh 登录远程服务器。两者需要用到的工具不太一样。

本人已分别在 Slackware 和 OpenBSD [1](https://undeadly.org/cgi?action=article;sid=20190302235509) 上成功使用了 Yubikey。 


## （首次使用）重置 Yubikey 相关 key/PIN 

需要安装：  

- pcsc-lite:   一个用于连接 smart card 和应用层的中间件   
  -	记得启动它（一般OS都可设置：`service pcscd start`）  
- ccid:		    也是一个中间件，pcsc 需要用它  
- yubico-piv-tools		核心工具，用于初始化、密文设置等高级别操作  
- ykpers:       核心工具，用于一般密文存储等操作 （包含命令：ykpersonalize） 

### 把 yubikey 设置为 smart card 模式：  
```
  ykpersonalize -m86
```

### 更改 yubikey 里的初始密钥密码，一般首次使用时都要求更改一下：   

- 更改默认的 management key：  
```
  dd if=/dev/random bs=1 count=24 2>/dev/null | hexdump -v -e '/1 "%02X"'
  yubico-piv-tool -aset-mgm-key -n<key>
```

- 更改 PIN 和 PUK 码：  
```
 yubico-piv-tool -achange-pin -P123456 -N<PIN>
 yubico-piv-tool -achange-puk -P12345678 -N<PUK>
```
**重要**： 请把这些存储在一个安全的地方。  

注： 后来官方出了更 high-level 的工具，命令更友好，详情请见：[官方文档](https://developers.yubico.com/PIV/Guides/Device_setup.html)  


## 在 Yubikey 上生成 SSH 密钥对

需要安装：  

- pcsc-lite:   一个用于连接 smart card 和应用层的中间件   
  -	记得启动它（一般OS都可设置：`service pcscd start`）  
- ccid:		    也是一个中间件，pcsc 需要用它  
- yubico-piv-tools		核心工具，用于初始化、密文设置等高级别操作  

### 方法一：直接在 yubikey 生成 ssh 密钥对：  
```
  yubico-piv-tool --key=<key> -s 9a -a generate -o rsa.public
```
其中 `-key=<key>` 就是你之前设置的 management key。   

也可以简化为：  
```
yubico-piv-tool -s 9a -a generate -o public.pem
```


### 方法二：在本地生成 ssh 密钥对然后导入到 yubikey：  
```
  ssh-keygen -m PEM -t ecdsa -b 384 -C yubikey_ecdsa384 -f yubikey_ecdsa384
```

这样生成了一个叫`yubikey_ecdsa384` 和 `yubikey_ecdsa384.pub` 的文件。接下来我们把私钥导入到 yubikey：  

```
  mv yubikey_ecdsa384 yubikey_ecdsa384.key   # just to rename the key
  yubico-piv-tool --key=<key> --pin-policy=once --touch-policy=always -s 9a \
	-a import-key -i yubikey_ecdsa384.key
```

也可以简化为：  
```
yubico-piv-tool -s 9a -a import-key -i key.pem  （注意需要转为 pem 格式）  
```

还需要为导入的 ssh key 生成证书（证明是您导入的）：  
```
    openssl ec -inform PEM -in yubikey_ecdsa384.key -outform PEM \
		-pubout -out yubikey_ecdsa384.public

	yubico-piv-tool -a verify -a selfsign --valid-days 3650 -s 9a \
		-S "/CN=SSH key yubikey_ecdsa384/" -i yubikey_ecdsa384.public -o cert.pem

	yubico-piv-tool --key=<key> --pin-policy=once --touch-policy=always \
		-a import-certificate -s 9a -i cert.pem
```

也可以简化为：  
```
yubico-piv-tool -a verify-pin -a selfsign-certificate -s 9a -S "/CN=SSH key/" -i public.pem -o cert.pem
yubico-piv-tool -a import-certificate -s 9a -i cert.pem
```

此时，记得拔出 yubikey 再重新插入。  

### 导出 SSH 公钥 （如果是直接在 yubikey 生成的 key 的话）  

- 先定位到 PKCS#11 的库的路径，通常的 Linux/macOS/BSD 会有`ykcs11` 这个包在系统上。如果没有，也可以自行安装`opensc`这个包。  
  - 比如我的 Slackware，opensc 的 pkcs11 库路径：`/usr/lib64/opensc-pkcs11.so`  
  - For OpenBSD: `/usr/local/lib/pkcs11/opensc-pkcs11.so`  
  - For Debian-based systems, the default path is `/usr/local/lib/libykcs11`  
  - For MacOS, the default path is `/usr/local/lib/libykcs11.dylib`  

- 这个命令导入公钥：  
```
  ssh-keygen -D path/to/libykcs11.so -e
```

- 然后你应该知道了，把这个公钥放到你的 VPS 的`authorized_keys` 里啦～！  


## 使用 Yubikey 的 SSH key 登录 VPS

需要安装：  
- pcsc-lite:   一个用于连接 smart card 和应用层的中间件   
  -	记得启动它（一般OS都可设置：`service pcscd start`）  
- ccid:		    也是一个中间件，pcsc 需要用它  
- opensc:		一个存储公钥的库；也可以使用很多系统带的`ykcs11`库  

### 先知道 PKCS#11 的库的路径

上面已经提到了。  

可以先尝试：
```
  ssh -I/path/to/pkcs11/opensc-pkcs11.so <host>
```

成功了的话就把它写到 ssh client 的配置文件里：  
```
  vi ~/.ssh/config

  PKCS11Provider /path/to/opensc-pkcs11.so
```

也可以让密钥一直留在内存里喔：  
```
  ssh-add -s/path/to/opensc-pkcs11.so
```

**大功告成！**  

<hr />  

注1：  也可以选用图形化的工具来操作：  
- yubikey-personalization-gui    

注2： 后来官方又出了个更 high-level 的工具：   
- ykman       更友好的操作工具（命名行；依赖 `ykpers`；可且仅取代 `yubico-piv-tool`）

