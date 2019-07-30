---
title: CSObot: 一个帮助中国公民社会组织的机器人
---

## CSObot: 一个帮助中国公民社会组织的机器人

    A bot serving the civil society organisations in China.

推送平台：

    #csobot:matrix.org （Matrix 平台的房间）


目前推送：
<!--more-->

- 使用 OONI probe 探测可能被墙网站（采用 Citizen Lab 的cn 列表），每日执行一次。OONI probe 报告发送到 OONI API, 报告编号和被墙网站数量推送到以上平台。
- IPFS 转换器：把单一网页上传到 IPFS 并返回相应 IPFS gateway 网址（大部分可直接访问；约4～5个url）
  用户发送以下命令，稍等片刻即可得到 IPFS gateway 网址（如果遇到下载网页失败请再试一下）：
```
    ipfs-add <url>
```

- 检查 Signal (Android)无谷歌框架版的更新（官方发布网站），如有更新，推送下载链接和其校验码。（每日3次）

项目仓库：[csobot](https://github.com/mdrights/csobot)

<hr>
〔附〕Matrix平台使用方法

Matrix 房間

#csobot:matrix.org

加入姿势：

    使用 Matrix 客戶端 (推荐 [Riot.im](https://riot.im))，点击「 + 」搜索如 #csobot:matrix.org 即可进入。
    Riot.im 也有網頁版:  访问 https://riot.im/app/#/room/#csobot:matrix.org即可看到消息（但想互动则需登录）。

riot.im 网页版，点击[ Room Directory] 可搜索房间。


另有 IRC 頻道与 Matrix 同步：

#aqi-data-share at oftc.net

(加入姿势： IRC 客戶端(如Hexchat)中的「网络列表」里选择： OFTC, 再在「服务器」菜单选择「加入一个频道」，加入上边的频道名即可 )

（謹記：必須搭配 Tor 否則完全暴露自己 IP 。）


祝使用愉快～
