---
author: F_Ms
comments: true
date: 2016-03-19 00:37:47+00:00
layout: post
link: http://f-ms.cn/?p=498
slug: '%e6%89%8b%e6%9c%baqq%e8%af%ad%e9%9f%b3-slkpcm-%e8%bd%ac%e6%8d%a2-wav-%e8%bd%ac%e6%8d%a2%e5%99%a8-qq_slk2wav'
title: 手机QQ语音 slk/pcm 转换 wav 转换器 QQ_SLK2WAV
wordpress_id: 498
categories:
- windows-batch
tags:
- windows-batch
---

### 批处理编写初衷：


安卓版手机QQ初期存储在手机上是标准amr格式，后来更改为slk格式，百度查询信息好像是skype首创的格式，找不到能直接播放的这种格式的音频播放器(不过个人对比了下，在体积变化不大的基础上音质确实有了很大的提升)，不过手机QQ的这种格式在文件头加了一个字符，让解析转换变得有些难度，从网上搜索了一些播放qq语音文件的软件发现大多使用很繁琐且格式播放不全(我已知的大概有三种变换格式)，个人手机上也有很多qq语音的珍贵回忆，每每归档对于此种类音频格式都会觉得很苦恼，在自己的促使下编写了此工具


### 批处理用途：


转换手机QQ语音文件 slk 为 wav
支持手机QQ生成的slk、pcm音频文件，程序会自动识别符合转换型的文件头进行转换并配有基本的错误判断及错误文件生成


### 转换原理：


判断文件头是否需要裁剪
通过自写方法裁剪文件头信息
通过SilkDecoder转换slk为pcm
通过FFmpeg将pcm转换为wav


### 使用帮助：


直接将待转音频文件或包含待转音频文件的文件夹拖动到批处理文件上即可
程序会自动识别文件头及基本的转换错误判断

![01](/img/post/wp/2016/03/01-1.png)

![03](/img/post/wp/2016/03/03.png) ![02](/img/post/wp/2016/03/02-1.png)


### 资料参考：


silk手机音频播放器


### 第三方工具调用：向工具原作者致敬


文件裁剪工具 - split(textutils)
slk2pcm工具 - SilkDecoder(无原作者信息)
pcm2wav - FFmpeg (FFmpegDevelopers)


### 下载地址：


[https://github.com/imfms/Tencent_SLK2WAV-Bat/releases](https://github.com/imfms/Tencent_SLK2WAV-Bat/releases)
