---
title: 适配 QBImagePickerController
date: 2016-04-10 21:58:00
tags: iOS
categories: iOS
---
## 问题
一个IM类软件的文件传输功能，UI大致如下：
![](http://7xskzj.com1.z0.glb.clouddn.com/im_send_file.png)
<!-- more -->
这货叫SelectFileViewController, 被放在一个UINavigationController里，然后persent出来。

为什么要套一个NavigationController里？因为点击`本地文件`的时候要push另外一个ViewController。

那手机相册就调用系统的吧。因为系统提供的相册是一个UINavigationController， Apple规定不能把UINavigationController Push到另外一个 UINavigationController 中去。哦！那也persent出来吧。

OK！ 现在 persent 2层，交互看起来是有些奇怪的，但功能也work，自测也没问题。提交！

一会QA MM就跑来找我了，在iOS7上选了照片以后只dismiss掉了一个，低下那个并不消失！

## 分析
在iOS7上，这种情况确实只能dismiss一个。除非你将第二次dismiss进行一定的延时，放到后面的事件循环中。这种解法自然是不靠谱的，被老大一把就拍成了翔。

这样又跟PM回到设计上的改进，看看别人是怎么做的？PM研究了一番，嗯！别人都是自己写的，然后push进去的。

那就找个第三方的相册来push进去。

## 解决
网上找了一圈，GitHub上 有个叫 QBImagePickerController 的看起来还不错，星星有1000多个，看起来很靠谱的样子。

赶紧集成进来看看！
```objc

QBImagePickerController* controller = [[QBImagePickerController alloc] init];
controller.delegate = self;
controller.allowsMultipleSelection = NO;

[self.navigationController pushViewController:controller animated:YES];
```

哎呀，怎么有2个NavigationBar，真是Shit！
![](http://7xskzj.com1.z0.glb.clouddn.com/qbimagepicker.png)

看了一下实现，原来这货从头文件看是个普通的ViewController，但是实现的时候，在自己内部先套了一个UINavigationController, 导致出现了2个NavigationBar！

有点挫折啊！先看看能不能把它内部的UINavigationController给去掉。

QBImagePickerController的内部结构是这样的：
![](http://7xskzj.com1.z0.glb.clouddn.com/qbimagepicker_arch.png)

QBImagePickerController只是一个壳，提供了delegate代理。那么事件处理还是靠它。但是UI，直接使用 QBAlbumsViewController好了。
这种tricky的东西还是写个类封装一下吧。

QBImagePickerAdapter.h
```objc
#import <Foundation/Foundation.h>
#import "QBImagePickerController.h"
#import "QBAlbumsViewController.h"

/**
 *  @brief 1.QBImagePickerController 默认存在一个NavigationController，不满足需求。
 *           所以去掉内部的NavigationController.
 */
@interface QBImagePickerAdapter : NSObject

/**
 *  @brief 相册VC，使用者将其Push进NavigationController。
 */
@property(nonatomic, strong, readonly) QBAlbumsViewController *albumsViewController;

/**
 *  @brief 使用代理进行初始化
 *  @param delegate 代理
 *  @param type 过滤方式，QBImagePickerControllerFilterTypeNone表示不进行过滤
 *  @return 实例
 */
- (instancetype)initWithDelegate:(id<QBImagePickerControllerDelegate>)delegate
                          filter:(QBImagePickerControllerFilterType)type;

@end
```

QBImagePickerAdapter.m
```objc
@interface QBImagePickerController (UsePrivateProperties)
@property (nonatomic, strong) NSBundle *assetBundle;
@end

@interface QBImagePickerAdapter ()
@property(nonatomic, strong, readwrite) QBAlbumsViewController *albumsViewController;
@property(nonatomic, strong, readwrite) QBImagePickerController *imagePickerViewController;
@end

@implementation QBImagePickerAdapter

- (instancetype)initWithDelegate:(id<QBImagePickerControllerDelegate>)delegate
                          filter:(QBImagePickerControllerFilterType)type {
    if (self = [super init]) {
        _imagePickerViewController = [[QBImagePickerController alloc] init];
        _imagePickerViewController.delegate = delegate;
        _imagePickerViewController.allowsMultipleSelection = NO;
        _imagePickerViewController.numberOfColumnsInPortrait = 3;
        _imagePickerViewController.numberOfColumnsInLandscape = 6;
        _imagePickerViewController.filterType = type;

        UIStoryboard* board = [UIStoryboard storyboardWithName:@"QBImagePicker"
                                                        bundle:_imagePickerViewController.assetBundle];
        _albumsViewController = [board instantiateViewControllerWithIdentifier:@"AlbumsViewController"];
        _albumsViewController.imagePickerController = _imagePickerViewController;
        _albumsViewController.navigationItem.leftBarButtonItem = nil;
    }

    return self;
}

@end
```
