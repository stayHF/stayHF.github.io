---
title: 添加Storyboard Reference
date: 2016-03-28 23:02:25
tags:
- iOS
- Storyboard
categories: iOS
---
如果你使用Interface Builder，将应用的所有场景都放在一个Storyboard，那久而久之，随着应用的膨胀，场景越来越多，这个Storyboard将会越来越臃肿。之前可能没有很好的方法来对这些UI进行重构，但随着Apple推出iOS9和Xcode7，让重构成为可能。

<!-- more -->
在Xcode7中，细心的朋友会发现，在Object Library中View Controller下面，多了一个叫Storyboard Reference的对象，如下图：
![](http://7xskzj.com1.z0.glb.clouddn.com/storyboard_objects.png)

# Storyboard Reference 基本用法
Storyboard Reference大致有3种用法：
- 指向不同的Storyboard;
- 指向相同Storyboard中的某个场景；
- 指向不同Storyboard中的某个场景。

我们将Storyboard Reference拖到Storyboard上之后, 根据使用场景, 可以设置它的以下三个属性：
![](http://7xskzj.com1.z0.glb.clouddn.com/storyboard_reference_attrs.png)

- 设置Storyboard属性️，确定是否指向相同的Storyboard(有默认值，即当前Storyboard)；
- 设置Referenced ID属性，确实指向的具体场景；
- 设置Bundle属性，确定`.storyboard`文件的位置(有默认值，即MainBundle)；

# 对现有Storyboard进行重构
假设如下：
Main.storyboard, 一个TabBarViewContoller的应用，内有4个Tab，每个Tab下的场景平均有10个，那总共40个场景。
![](http://7xskzj.com1.z0.glb.clouddn.com/big_storyboard.png)

按以下思路进行重构：把每个Tab内的所有场景都放在一个单独的Storyboard中，例如`Tab{N}.storyboard`中。
重构步骤如下：
1. 选中第一个Tab下所有的场景(按住Command键或者使用鼠标框选)；
2. 点击Xcode7的菜单 Editor --> Refactor to Storyboard...;
![](http://7xskzj.com1.z0.glb.clouddn.com/refactor_storyboard.png)
3. 给新的Storyboard一个名字并保存。
重复上面的步骤直到每个Tab都被分离出去。
![](http://7xskzj.com1.z0.glb.clouddn.com/storyboard_reference_result.png)

Done.
