---
title: "[折腾]小鸡(VPS)-101"
date: 2023-08-02T11:04:49+08:00
draft: false
description: "折腾：小鸡配置"
images: ["/Apple-Devices-Preview.png"]
lightgallery: true
tags: ["折腾","VPS"]
categories: ["折腾"]
math:
  enable: tr
---


# 小鸡(VPS)-101

## VPS Setup

### 目的

1. 一个随时可以连接全球互联网的linux系统
2. 自建一个支持webdav的云盘，用于zotero同步
3. ~~折腾扶墙~~

### 选购

1. 有钱国外厂商，搬瓦工，vultr等。
2. 没钱等活动

    1. 硅云学生特惠，1C1G1M，600五年。（我是没抢到）

~~在我选购时(2023/10/1)，腾讯云277一年2C4G30M新加坡小鸡，感觉还可以。(ping IP延迟350ms，用个毛）~~

最后换了UCLOUD香港小鸡，1C2G, 2M==250元，速度比腾讯新加坡的快多了，还不限流量，不错不错

### DD

DD就是重装系统，避免各厂商的预装系统加料，使用原生态系统更省心。已经有不少先辈制作了各种一键DD脚本。可参照：

1. [一键DD系统脚本 支持国内小鸡 - 爱墨迹 (imgki.com)](https://imgki.com/archives/774.html)
2. [一键重装支持 raid0 CentOS AlmaLinux RockyLinux Fedora，不同系统互装-美国VPS综合讨论-全球主机交流论坛 - Powered by Discuz! (hostloc.com)](https://hostloc.com/thread-1106004-1-1.html)
3. [Linux reinstall：一款功能强大的服务器系统一键重装脚本-美国VPS综合讨论-全球主机交流论坛 - Powered by Discuz! (hostloc.com)](https://hostloc.com/forum.php?mod=viewthread&amp;tid=1094336)
4. [腾讯dd Linux纯小白教程，大佬忽略-美国VPS综合讨论-全球主机交流论坛 - Powered by Discuz! (hostloc.com)](https://hostloc.com/forum.php?mod=viewthread&tid=1211202&highlight=%E6%95%99%E7%A8%8B)

### 安全

1. [保护好你的小鸡！保姆级服务器安全教程！ | 爱玩实验室 (iwanlab.com)](https://iwanlab.com/how-to-secure-a-linux-server/)

### 丝滑终端（安装oh my zsh）

1. 安装zsh`sudo apt-get install zsh`

2. 切换默认shell为zsh.`sudo chsh -s /bin/zsh`, then  `reboot`
3. [Oh My Zsh - a delightful &amp; open source framework for Zsh](https://ohmyz.sh/), 照着主页的curl安装即可
4. 安装开箱即用主题`power10k`,安装官方github wiki 走即可（记得下载字体，不然这个主题漂亮的图标没法显示）[romkatv/powerlevel10k: A Zsh theme (github.com)](https://github.com/romkatv/powerlevel10k)
5. 安装常用插件：补全和高亮
    [zsh-users/zsh-syntax-highlighting: Fish shell like syntax highlighting for Zsh. (github.com)](https://github.com/zsh-users/zsh-syntax-highlighting)
    [zsh-users/zsh-autosuggestions: Fish-like autosuggestions for zsh (github.com)](https://github.com/zsh-users/zsh-autosuggestions)
    插件是在`~`，即`root`路径下

    1. git clone XXX
    2. `vim ~/.zshrc`, 在plugins项中添加插件名字
    3. `source ~/.zshrc`启用插件

### WebDav

kdbox（可道云）使用docker，基本可以傻瓜式安装。

* [Docker环境可道云网盘的安装示例 | Kodcloud Documentation](https://docs.kodcloud.com/setup/docker/)

### 黑魔法

请搜索 `V2Fly`, Vmess裸奔截止2023/12/28暂时正常。