---
layout: post
title:  "树莓派服务器架设记录"
date:   2017-05-10 12:50:30 +0800
categories: raspberrypi
tags: raspberrypi
---

### 环境
- Raspberrypi: Raspberry3 Model B
- OS: RASPBIAN JESSIE LITE 2017-03-03-17:50

# 使用USB存储盘启动树莓派

> 此处参考 [https://www.chiphell.com/thread-914770-1-1.html](https://www.chiphell.com/thread-914770-1-1.html) 片段:U盘启动篇

0. 准备
    - 硬件
      - SD卡一枚 >= 128MB
      - 外置USB存储盘 (U盘, 启动硬盘)
    - 系统镜像
      - [https://www.raspberrypi.org/downloads/](https://www.raspberrypi.org/downloads/)
      - USB-ZIP.zip (DOS系统文件? 原因不明, 应该是引导相关内容)
    - 软件
      - Windows操作系统
      - 镜像烧写工具 [Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/)
      - SD卡格式化工具 [SD Card Formatter](https://www.sdcard.org/downloads/formatter_4/)
1. 使用 SD Card Formatter 对 SD卡格式化并选择烧入解压后的USB-ZIP.zip
2. 使用 Win32 Disk Imager 将树莓派系统镜像烧入U盘
3. 将U盘中的commline.txt文件中的 `root=/dev/mmcblk0p2` 修改为 `root=/dev/sda2`
    - 个人理解
      - mmcblk0p == 内存卡
      - mmcblk0p2 == 内存卡的第二个分区(树莓派镜像烧写中会将系统烧写到第二个分区)
      - sda == 第一个外置磁盘
      - sda2 == 第二个外置磁盘的第二个分区
4. 将U盘第一个分区(FAT格式)所有内容复制到SD卡下
5. 将SD卡插入树莓派SD卡位置, U盘插入USB位置，点亮树莓派

# 时区更改
    sudo dpkg-reconfigure tzdata

# 默认启动开启ssh
    在启动盘第一个分区(FAT_Boot)下新建空文件 `ssh`
    
# 替换 apt-get 源
    vim /etc/apt/sources.list
    
    # 首行默认官方源，国内访问速度太慢，故注释
    # deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi
    
    # Uncomment line below then 'apt-get update' to enable 'apt-get source'
    # deb-src http://archive.raspbian.org/raspbian/ jessie main contrib non-free rpi
    
    # 添加阿里镜像源
    deb http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
    deb-src http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib

# 修改pi默认密码
> 默认用户: pi </p> 默认密码: raspberry
    
    passwd
    [当前密码]
    [新密码]
    [确认新密码]
