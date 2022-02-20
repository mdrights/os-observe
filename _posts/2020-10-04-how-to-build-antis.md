---
date: 2022-02-20
---



# 如何制作一个 antiS live 系统 ISO 镜像  

> 最新更新：2022-02-20

## 前提

- 你（最好）需要一个运行了 Slackware 的机器；  
- 同步最新的 Slackware 官方 -CURRENT [repo](https://mirrors.slackware.com) 源（即系统自带的包）：  
- 同步最新的 [Slackbuilds](https://slackbuilds.org) 第三方应用脚本库：  
- 包管理工具：[sbopkg](https://sbopkg.org)  

整个过程需要打两次 ISO 镜像。

## 制作第一个 ISO

> 目的是让我们有个基于最新 -CURRENT 源的系统环境，以便后续让应用基于这个环境进行构建。  

- 构建：
```
  /home/user/repo/LiveSlak/make_slackware_live.sh -R 3 -l zh_cn -v -X 
```

- 烧录到 USB 盘：   
```
  dd .... (略)
```


    记得使用我们调制好的 [`tagfile`](https://github.com/mdrights/LiveSlak/tree/mdrights/tagfiles)。  

## 构建我们需要的应用

**重启电脑** 至刚才做好的 USB 盘。  

配置好 `sbopkg`。

有的应用源码下载需要梯子，因此我们分开两步，

- 先下载：  
```
  proxychains sbopkg -V local/local -d liveslak-full.sqf
```

- 构建/安装：
```
  sbopkg -V local/local -i liveslak-full.sqf
```


都安装好那安装包应该都在我们制定的目录里了。


## 制作第二次 ISO，把构建好的应用打包进去

这时我们再重启回到我们电脑里的 Slackware 环境（命令同上）。  

- 构建  

- 烧录到 USB 盘   

大功告成！用 USB 盘启动电脑试试看 ：）



############# 一些细节 #############

1. 你需要先下载 Slackware 的官方源（内有构建 Slackware 所有的包）：https://mirrors.slackware.com  

2. 如果你需要用自己定制的内核，可以把重新编译好的内核包和内核模块包覆盖进上述下载的官方内核和模块包即可。通常官方内核和模块包在：`<your/path>/slackware64-current/slackware64/a`   记得 Liveslak 只采用 generic 内核包，因此你的定制内核包的名称要和官方源里的 generic 内核包相同。

3. 如果你有第三方软件需要加进 Liveslak，可以参考下面的打包脚本先制作 Slackware 安装包，然后把制作的包（tgz 或 txz）放置于一个目录下（比如 $HOME/liveslak），LiveSlak 构建时会导入这些软件包：

    - Liveslak 采用：[Slackbuilds-nonprism](https://github.com/mdrights/Slackbuilds-nonprism) 
    - 还有这里： [Slackwarecn-slackbuilds](https://github.com/slackwarecn-slackbuilds)
	- 还有 Slackbuilds（半官方脚本源）：[slackbuilds.org](https://slackbuilds.org)

    为了达到这个目的，请自行创建（或修改）本repo里的 xxx.conf & xxx.lst 配置文件（也可以用我的：mdrights{.conf, .lst}）   
    其中 `SL_REPO` 变量要指向放置你的软件包的目录。

4. 其他修改/自定义的地方就是：`make_slackware_live.conf`   
        - `SL_REPO` = 你的本地 Slackware （官方）仓库地址  
        - `LIVEDE`  = 给它起个名字吧  

5. 运行构建脚本(如我的)：
	`./make_slackware_live.sh -R 3 -l zh_cn -v`  


Happy building!
