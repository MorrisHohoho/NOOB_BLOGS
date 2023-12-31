---
title: "[折腾]WSL跑MAC OS"
date: 2023-08-02T11:04:49+08:00
draft: false
description: "折腾：用WSL跑MAC OS"
images: ["/Apple-Devices-Preview.png"]
lightgallery: true
tags: ["折腾","黑苹果"]
categories: ["折腾"]
math:
  enable: tr
---

## 参考教程

1. 源项目：[sickcodes/Docker-OSX: Run macOS VM in a Docker! Run near native OSX-KVM in Docker! X11 Forwarding! CI/CD for OS X Security Research! Docker mac Containers. (github.com)](https://github.com/sickcodes/Docker-OSX#initial-setup)
2. 视频教程：[WSL+Docker+Apple MacOS X Big Sur installation in Windows 11 - YouTube](https://www.youtube.com/watch?v=BxBw16OCQME)

## 我的配置

主机：12700F+3080+B660M+32G内存+1TBSSD

系统：WINDOWS11 21H2

## 从0开始装

### 安装WSL

安装WSL有两种方法：

1. 第一种是直接以管理员身份运行powershell，执行命令`wsl --install`，该命令默认安装Ubuntu20.04LTS

    ![image](/assets/image-20220908092655-y0b3zea.png "右键windows徽标")
2. 第二种是在官网下载wsl安装包，安装包在哪个地方，wsl就会安装在哪个地方

以上方法，我选择了第一种。WSL默认安装在C盘。安装好后，在powershell窗口输入wsl就能够打开Ubuntu.

![image](/assets/image-20220908093249-9pw8zms.png "ubuntu")

### 开启KVM虚拟化

在`C:/Users/<Your Name>/.wslconfig`中添加

```C++
[wsl2]
nestedVirtualization=true
```

如果没有.wslconfig就自己创建一个

![image](/assets/image-20220908093918-nnt9fr6.png ".wslconfig内容")

配置好后保存退出。然后在powershell中输入`kvm-ok`，成功后应该像下面一样。==如果提示找不到命令kvm什么的，终端提示安装什么包就安装什么包。==

![image](/assets/image-20220908094227-uv68q84.png)

### Docker安装与配置

[Install Docker Desktop on Windows | Docker Documentation](https://docs.docker.com/desktop/install/windows-install/)

官网下载安装即可。把下面两个配置打开

```C++
General -> "Use the WSL2 based engine";
Resources -> WSL Integration -> "Enable integration with my default WSL distro",
Resources -> WSL Integration -> Ubuntu那个按钮勾选上
```

换源直接在Docker GUI界面里添加镜像站即可（阿里云的不要用，要申请才有加速效果）

![image](/assets/image-20220908094703-u3yd260.png)

### 安装图形化配置

Ubuntu终端输入命令，等待安装即可

```C++
sudo apt install x11-apps -y
```

### 更换wsl和docker镜像、容器位置

wsl和docker的工作目录默认都是在C盘，当docker越用越久的时候，空间会越来越大。所以更换工作目录是非常有必要的。利用wsl自带的import、export功能就能很方便的实现。

1. 在powershell中，查看已安装的linux发行版

```C
wsl -l -v 
```

![image](/assets/image-20220908095248-04akvu8.png)

2. 把所有running的linux全部停止

```C
wsl --shutdown
```

3. 导出

```C
wsl --export [NAME] D:\[EXPORT_NAME].tar
```

4. 注销当前发行版

```C
wsl --unregister [NAME]
```

5. 重新导入并安装到迁移路径

```C
wsl --import [NAME] D:\[PLACE YOU WANT] D:\[EXPORT_NAME].tar --version 2
```

比如，迁移Ubuntu到D盘的WSL下

```C
wsl --shutdown
wsl --export Ubuntu D:\Ubuntu.tar
wsl --unregister Ubuntu
wsl --import Ubuntu D:\WSL D:\Ubuntu.tar
```

迁移后的Ubuntu默认登录为root，这对平常使用来说，权限太大了，我们需要更改一下默认用户为普通用户。在powershell下输入

```C
ubuntu2004 config --default-user [NAME]
或
ubuntu config --default-user [NAME]
```

### 下载docker镜像并创建实例

具体命令可以看源项目read.me，我们这里选择安装Catalina

```C
docker run -it \
    --device /dev/kvm \
    -p 50922:10022 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    sickcodes/docker-osx:latest

# docker build -t docker-osx .
```

等待镜像下载好之后，就可以运行macOS的安装程序了。==注意格式化硬盘的时候，选择200多G那个虚拟硬盘就可以了。==

## 后记

捣鼓了一下午，总算装上了。登录APPLE ID，要改一下DNS（改成8.8.8.8）。显存只有7MB，卡顿明显，不适合日常使用。要体验体验，可以折腾一下。日用还是黑、白果吧。