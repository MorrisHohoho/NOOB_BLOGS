---
title: "[SCU]研究开发与实践-开发笔记"
date: 2023-08-02T11:04:49+08:00
draft: false
description: "Courses Notes"
images: ["/Apple-Devices-Preview.png"]
lightgallery: true
tags: ["notes-SCU Courses"]
categories: ["SCUDev"]
math:
  enable: tr
---

# FakeCeleste

# 简介

用Unity制作一个仿制的Celeste，尽力还原游戏的手感。

# Day 1

## 地图绘制

在网上能够找到别人整理好的素材，虽然素材不太好使，但是不细看的话，效果还是不错的。  
==素材大小是32*32的==，记住这点就好。然后对着游戏画图就行。

# Day 2

## 人物贴图

创建一个2D sprite,然后将madline的贴图替换掉原来的正方形。

## 渲染顺序

对于面板里可看到的有关渲染顺序的选项有两个：Sorting Layer和Order in Layer。前者优先级大于后者。当Sorting Layer相同时，按照Order in Layer中的顺序（小的优先）进行渲染。  
所以我们把整个游戏从先到后分成四层：BG,Terrain，Player，UI，设置好Sorting Layer。

## ==人物动画（状态机）==

单个动画Animation做就行（快捷键ctrl+6)，记得赋予对应的sprite，并勾选上Loop。

如果要进行动画的转换，就需要利用状态机。Unity有个可视化的状态机Animator，低代码量地写出动画变换的逻辑。缺点是容易造屎山，当状态过多的时候，连线将非常多，极度影响代码可读性，所以身为一个优秀的程序员。状态机咱还是老老实实写一个接口来实现。

## 人物碰撞

对于人物，我们需要Rigidbody2D和 colliderbox2D。碰撞盒子画的小一点，看起来比较自然。当然不能过分小，不然会穿模。

对于地图，同样需要Rigidbody2D和tile colliderbox2D。不过地图的物理特性一般是不需要更新的，所以我们将其设置为static。在Celeste里，没有人物和单个tile交互的情况。所以我们用Compsitecolliderbox2D，把所有tile当成一个巨大的碰撞盒子，这样可以提高运算速率。

## ==人物运动(利用Unity新的Input System实现）==

这里的思想同人物动画中的状态机思想。也是单独写一个PlayerInput类来管理玩家的输入，然后根据这个类捕获到的输入信号，再在相应的类中去响应对应的输入。非常优雅的面向对象的思想。比Unity自带的那个老的输入系统要好不少。

# Day3

## 跳跃功能

跳跃功能实现了，但是Bug非常多。很烦，明天再改。

# Day4

## 爬墙和跳跃

爬墙和跳跃的优化，现在五个状态:Idle、Run、Jump、Climb、Fall衔接还是比较流畅了。还差个Dash，功能就实现的差不多了。明天把Dash和土狼时间实现，基础功能就全部实现。后续就是优化控制器和增加地图元素交互。

# Day5

## 冲刺

Dash功能的初步实现，效果还是不错的。不过没有残影，后面再加一下

# Day 6 

## 冲刺残影-对象池实现

### 残影预制体

首先我们需要写一个残影预制体，用对象池对不同的具体残影进行管理。残影需要获取到玩家的贴图，所以需要一个spriterender组件，并且把Sorting Layer设置为和玩家一样的层级，这样才能显示。然后给其赋予一个脚本，脚本的逻辑为：寻找到玩家对象，获取玩家对象的坐标和贴图，对获取到的贴图随时间更改其透明度。（要在残影的显示时间内）如果残影显示时间超出了设置的时间，那么就把这个残影对象停用，并返回到对象池。

### 对象池

用一个单独的空物体来管理所有需要反复出现消失的对象。用队列实现对象池。初始阶段，先把对象池填满（入队），但不启用。当对象池中的对象被触发时，依次出队即可。如果队中所有对象均出队却仍然不满足触发条件（比如冲刺时间>残影显示时间），这时需再往对象池中填充对象。写一个if语句判断就行。

对象池建议使用单例模式（因为对象池只有一个，用单例模式很合理），这样能够在其他脚本中直接调用，而不需要实例化对象，减少开销。必要操作有FillPool（），填充对象池；ReturnObject（Obejct），停用对象池中的某个对象，并放入对象池；GetFromObejct（），获取对象池对象。

### 实现过程的小问题

1. 把残影的更新逻辑放到FixedUpdate里面，不要放在Update里面。否则在一些高性能电脑上，渲染帧数过高，残影很快就会消失。
2. RGB的alpha通道不要用1f来乘变化值，alpha小于1的值，Unity好像会处理成完全透明，根本不显示。

## 爬墙

在playcontroller中设置一个监测爬墙键是否被按下的变量`public bool Climb => playerInputActions.Gameplay.Climb.IsPressed();`​IsPressed()当按键一直被按着时返回的是true。  
然后再climb状态的fixedupdate中，添加if判断，如果按住爬墙键，则爬墙，否则进行滑落。  
同时设置一个爬墙时间，如果爬墙时间过久，则直接下滑。

沿墙滑落状态：人物与墙碰撞且玩家按住对应的方向键  
爬墙状态：玩家按着爬墙键，当玩家松开爬墙键，且什么都没按时，人物直接掉落，

## 蹬墙跳（待优化）

这块没做好，后面再优化一下。重点是如何让蹬墙跳后的人物脱离墙面一段距离再进行控制，避免人物蹬墙跳后又回到墙面上

## 土狼时间

新建一个土狼时间状态，用状态持续时间来进行土狼时间的判断

## 跳跃缓冲

用协程进行跳跃缓冲处理

# TODO

* [ ] 爬墙时间快到时，人物红光闪烁。
* [ ] 蹬墙跳取消惯性：就是蹬墙跳后不能控制人物，让人物不能马上又粘在墙上
* [X] 跳跃缓冲：让人物的跳跃条件不用严格在边界条件下执行
* [X] 冲刺跳：也该有一个缓冲，在缓冲时间内按下跳，将速度加到跳跃的逻辑上

  原来的想法有点难，短时间没法实现。我选择设计一个新的DashJump状态，只有Dash才能进入这个状态，进入这个状态之后，跳跃高度和空中移动速度都变快。
* [X] 死亡判定
* [X] 尖刺、跳板、移动平台
* [X] 增加一些关卡，可能还会有镜头切换效果
* [X] 开始界面UI
* [ ] 冲刺抖动、涟漪效果

  要改shader装插件还是算了
* [X] 音效
* [ ] 胜利提示
* [X] 跳跃粒子特效

  做了冲刺和跳跃、像一坨答辩。

## BUG

* [X] 贴着墙能够无限跳：脚底碰撞盒子也和墙体发生了碰撞

  其实这不是BUG，本来就该有这种机制
* [X] 爬到平台上面应该自动上墙

  我用了一个最蠢的方法，在人物的头上加一个攀爬标志，当人物应该上墙时，直接把人物的position的移动到攀爬标志上。
* [ ] 大半截身子跳到平台上应该助推其跳上平台

# 12.11 TODO

* [X] 再画几个关卡，把游戏时长堆到3mins以上
* [X] 第一关做个乌鸦TIPS
* [X] 最后一关收集之后播放胜利音乐，并弹窗You Win，然后有个退出到主菜单和再来一遍按钮。
* [ ] 做个ESC菜单，调音量，从头开始、返回游戏、重新开始本关以及退出到主菜单

  不做了，游戏内容已经非常丰富了。
* [X] 弹簧床以及移动平台的音效添加

# 12.11 Done

画了一共11关，同时有开始和胜利界面。到今天，算是完全做完了。后面好好整理一下，写一篇开发笔记。算是一次Unity2D的开发经验了。

# 参考资料

* 弹簧床教程：[https://www.bilibili.com/video/BV1Ha411s7sG](https://www.bilibili.com/video/BV1Ha411s7sG/)
* 一个完整的模型Celeste手感的项目思路分享：[https://www.bilibili.com/video/BV1xr4y1s71V](https://www.bilibili.com/video/BV1xr4y1s71V/)
* 素材来源：[https://space.bilibili.com/76823602?spm_id_from=333.337.0.0](https://space.bilibili.com/76823602?spm_id_from=333.337.0.0
  )
* 场景搭建和雪花特效：[https://www.bilibili.com/read/cv7068806/](https://www.bilibili.com/read/cv7068806/
  )
* 原版手感实现思路分享：[https://zhuanlan.zhihu.com/p/186713195](https://zhuanlan.zhihu.com/p/186713195
  )
* （Youtube）Unity2D入门：[https://www.youtube.com/watch?v=Ii-scMenaOQ&amp;list=PLrnPJCHvNZuCVTz6lvhR81nnaf1a-b67U](https://www.youtube.com/watch?v=Ii-scMenaOQ&list=PLrnPJCHvNZuCVTz6lvhR81nnaf1a-b67U
  )
* ==平台跳跃，状态机详解：==​[==https://www.bilibili.com/video/BV1a14y1a72w==](https://www.bilibili.com/video/BV1a14y1a72w/?spm_id_from=333.337.search-card.all.click&vd_source=812be8baf0074e4d7fd5491cf54f958f
  )
* 残影效果（对象池）：[https://www.bilibili.com/video/BV1gJ411E7gx](https://www.bilibili.com/video/BV1gJ411E7gx/?spm_id_from=333.788.recommend_more_video.0&vd_source=812be8baf0074e4d7fd5491cf54f958f
  )

‍
