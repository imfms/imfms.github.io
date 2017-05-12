---
layout: post
title:  "树莓派服务器架设记录"
date:   2017-05-10 12:50:30 +0800
categories: raspberrypi
tags: 
- raspberryp
- linux
---

### 环境
- Raspberrypi: Raspberry3 Model B
- OS: [RASPBIAN JESSIE LITE 2017-02-27](https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2017-02-27/)

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
    
    apt-get update
    apt-get upgrade

# 修改pi默认密码
> 默认用户: pi </p> 默认密码: raspberry
    
    passwd
    [当前密码]
    [新密码]
    [确认新密码]

# 安装MySQL
    apt-get install mysql-server

# 指定脚本开机自启
> 本段参考[http://blog.chinaunix.net/uid-26000296-id-3986914.html](http://blog.chinaunix.net/uid-26000296-id-3986914.html), [https://q.cnblogs.com/q/26733/](https://q.cnblogs.com/q/26733/)中[麦田里的守望者](https://q.cnblogs.com/u/x2048/)的回答

## 1. 修改 /etc/rc.local 方式
直接在文件中底部添加需要执行的命令即可，不可含有阻塞式命令(不得不含有可在语句后添加`&`)，在成功执行整个rc.local文件后应返回状态码0，以下是rc.local文件头部注释

    rc.local

    This script is executed at the end of each multiuser runlevel.
    Make sure that the script will "exit 0" on success or any other
    value on error.

    In order to enable or disable this script just change the execution
    bits.

    By default this script does nothing.

## 2.linux服务方式启动 /etc/init.d/
> 本段参考自 [http://blog.chinaunix.net/uid-26000296-id-3986914.html](http://blog.chinaunix.net/uid-26000296-id-3986914.html)
0. 阅读 /etc/init.d/README

		Configuration of System V init under Debian GNU/Linux
		
		Most Unix versions have a file here that describes how the scripts
		in this directory work, and how the links in the /etc/rc?.d/ directories
		influence system startup/shutdown.
		
		For Debian, this information is contained in the policy manual, chapter
		"System run levels and init.d scripts".  The Debian Policy Manual is
		available at:
		
			http://www.debian.org/doc/debian-policy/#contents
		
		The Debian Policy Manual is also available in the Debian package
		"debian-policy".  When this package is installed, the policy manual can be
		found in directory /usr/share/doc/debian-policy. If you have a browser
		installed you can probably read it at
		
			file://localhost/usr/share/doc/debian-policy/
		
		Some more detailed information can also be found in the files in the
		/usr/share/doc/sysv-rc directory.
		
		Debian Policy dictates that /etc/init.d/*.sh scripts must work properly
		when sourced.  The following additional rules apply:
		
		* /etc/init.d/*.sh scripts must not rely for their correct functioning
		  on their being sourced rather than executed.  That is, they must work
		  properly when executed too. They must include "#!/bin/sh" at the top.
		  This is useful when running scripts in parallel.
		
		* /etc/init.d/*.sh scripts must conform to the rules for sh scripts as
		  spelled out in the Debian policy section entitled "Scripts" (§10.4).
		
		Use the update-rc.d command to create symbolic links in the /etc/rc?.d
		as appropriate. See that man page for more details.
		
		All init.d scripts are expected to have a LSB style header documenting
		dependencies and default runlevel settings.  The header look like this
		(not all fields are required):
		
		### BEGIN INIT INFO
		# Provides:          skeleton
		# Required-Start:    $remote_fs $syslog
		# Required-Stop:     $remote_fs $syslog
		# Should-Start:      $portmap
		# Should-Stop:       $portmap
		# X-Start-Before:    nis
		# X-Stop-After:      nis
		# Default-Start:     2 3 4 5
		# Default-Stop:      0 1 6
		# X-Interactive:     true
		# Short-Description: Example initscript
		# Description:       This file should be used to construct scripts to be
		#                    placed in /etc/init.d.
		### END INIT INFO
		
		More information on the format is available from insserv(8).  This
		information is used to dynamicaly assign sequence numbers to the
		boot scripts and to run the scripts in parallel during the boot.
		See also /usr/share/doc/insserv/README.Debian.

1. 提取关键
	
	- 大多unix系统下 `/etc/rc?.d` 目录对应着系统开机/关机时会调用的脚本
	
		所有 rc?.d 中的脚本来自 /etc/init.d 的软链接, 以下英文文档来自 各个rc?.d目录下README
	
		- rc0.d
		
			Generally it is not necessary to alter the scripts in this directory
			Their purpose is to stop all services and to make the system ready
			for shutdown.
			
			在系统执行停止所有服务准备关机时
			
		- rc1.d
		
			Generally it is not necessary to alter the scripts in this directory
			Their purpose is to stop all services and thus to put the system in
			single-user mode.
			
			在系统执行停止所有服务进入单用户模式时
			
		- rc2-5.d document
		
			To disable a service in this runlevel, rename its script in this
			directory so that the new name begins with a 'K' and a two-digit
			number, and run 'update-rc.d script defaults' to reorder the scripts
			according to dependencies.  A warning about the current runlevels
			being enabled not matching the LSB header in the init.d script will be
			printed.  To re-enable the service, rename the script back to its
			original name beginning with 'S' and run update-rc.d again.
			?
			
			- 在当前运行级别关闭一个服务脚本，将符号链接更名为以前缀以'K'开头带两个数字，并执行 `update-rc.d $script_name defaults` 使脚本适应变化
			
			- 如果开启当前运行级别而不匹配脚本LSB头，会打印警告
			
			- 当要重新开启这个服务，将符号链接的更名为以前缀'S'带两个数字并执行 `updadate-rc.d $script_name defaults` 使脚本适应变化
			
		- rc2.d
		
			不开启NFS的多用户状态
		
		- rc3.d
		
			完全的多用户状态，包含NFS，登录后进入多用户模式
		
		- rc4.d
		
			未使用，系统保留
		
		- rc5.d
		
			X11控制台，登录后进入图形GUI
		
		- rc6.d
		
			Generally it is not necessary to alter the scripts in this directory.
			Their purpose is to stop all services and to make the system ready
			for reboot.
			
			在系统执行停止所有服务准备重新启动时
			
2. LSB文件描述头
> 引用[http://blogread.cn/it/article/4791](http://blogread.cn/it/article/4791)

3. Do it
	1. 根据规范编写脚本, 根据需要添加LSB头
	2. 脚本添加执行属性 `chmod +x $script_path`
	3. 测试脚本在任何目录下都能运行
	4. 移动脚本到 `/etc/init.d/`
	5. 执行 `update-rc.d $script_name default`
