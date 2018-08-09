---
author: F_Ms
comments: true
date: 2015-11-24 13:06:46+00:00
layout: post
link: https://imf.ms/?p=150
slug: ednsp%e7%bd%91%e5%ba%97%e5%9b%be%e7%89%87%e7%a6%bb%e7%ba%bf%e5%b7%a5%e5%85%b7-%e6%94%af%e6%8c%81%e4%b8%8b%e8%bd%bd%e6%b7%98%e5%ae%9d%e3%80%81%e5%a4%a9%e7%8c%ab%e3%80%81%e9%98%bf%e9%87%8c%e5%b7%b4
title: EDNSP网店图片离线工具-支持下载淘宝、天猫、阿里巴巴商品图片Bat工具
wordpress_id: 150
categories:
- windows-batch
tags:
- windows-batch
- 工具
---

用途：

    
    指定一个网店商品的地址给此工具，会将商品详情主图颜色分类等图片离线到本地


啧啧：

    
    EDNSP是EasyDownloadNetShopPic的缩写
    
    更新了N多个版本，从支持天猫到、阿里巴巴到最后的淘宝，没做到全阿里兼容没敢放出来 - -
    因为是通过html代码分析进行下载的所以稳定性不太能担当，但是自己也已经拿了很多的进行样本测试，运行起来应该没什么大问题 当然人平台更新的话工具没更新应该也会有些稳定性的问题
    



    
    工具由批处理码出，由QuickBFC将批处理编译为exe
      调用了一些第三方工具(有点大才小用了)：
        sed  著名牛叉文本处理工具
        strrpc  未找到出处:(
        winclip  未找到出处:(
        aria2c  著名命令行多线程下载器
      在此向原工具作者致敬


提示：

    
    为了能够跟上官方更新故添加了自动更新
    如工具运行中安全软件提示一律信任即可，无任何损害电脑的代码


截图：

![](/img/post/wp/2015/11/0011.jpg)
![](/img/post/wp/2015/11/0021.jpg)
![](/img/post/wp/2015/11/003.jpg)
![](/img/post/wp/2015/11/005.jpg)


[https://github.com/imfms/BatchProjectsContent/tree/master/EDNSP%E7%BD%91%E5%BA%97%E5%9B%BE%E7%89%87%E7%A6%BB%E7%BA%BF%E5%B7%A5%E5%85%B7](https://github.com/imfms/BatchProjectsContent/tree/master/EDNSP%E7%BD%91%E5%BA%97%E5%9B%BE%E7%89%87%E7%A6%BB%E7%BA%BF%E5%B7%A5%E5%85%B7)
    
