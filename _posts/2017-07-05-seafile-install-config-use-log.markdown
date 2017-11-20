---
layout: post
title:  "Seafile 安装/配置/升级/使用 记录"
date:   2017-07-06 22:00:30 +0800
categories: server
catalog:    true
tags: 
- raspberry
- linux
- file
- server
- netdisk
---

# Seafile 安装/配置/升级/使用 记录

> Seafile 是一款开源的企业云盘，注重可靠性和性能。支持 Windows, Mac, Linux, iOS, Android 平台。支持文件同步或者直接挂载到本地访问。

由于个人本身不是很喜欢国内在线的网盘，灵活性、速度、各种限制等... ...，故在有了树莓派之后买了块硬盘挂载安装系统后就开始倒腾着安装网盘工具了。初期刚入手时是使用的基于php的[OwnCloud](https://owncloud.org/)，
测试使用期间发觉在树莓派上异常缓慢，遂放弃，无意间搜索到独立进程的Seafile, 试用一段后觉得性能体验比OwnCloud良好，期间也没有遇到过什么大问题，只是不像OwnCloud有那么多插件，便放心将网盘内容迁移到本地网盘服务器上，
由于安装过程些许繁琐，作此记录利己利人予以查阅

## 服务端
### 安装
1. [基本安装](https://manual-cn.seafile.com/)		
2. 通过WEB服务队seafile两个组件代理到一个域
	- [通过Apache](https://manual-cn.seafile.com/deploy/deploy_with_apache.html)
	- [通过Nginx](https://manual-cn.seafile.com/deploy/deploy_with_nginx.html)
3. [开机启动](https://manual-cn.seafile.com/deploy/start_seafile_at_system_bootup.html)

### [升级](http://manual-cn.seafile.com/deploy_windows/upgrading_seafile_windows_server.html)
	
### [添加webdav支持](https://manual-cn.seafile.com/extension/webdav.html)

非https windows下连接需做以下配置

将注册表该键值修改为 `2`

	HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters\BasicAuthLevel

重启WebClient服务

	net stop WebClient
	net start WebClient

### 还原数据

1. 准备

	1. 还原的数据
	
		数据备份使用了[再生龙](http://clonezilla.org/)(这才知道[Ghost](https://en.wikipedia.org/wiki/Ghost_(software))不支持linux分区)，可使用虚拟机操作
		
	2. 提取数据
		- 数据目录
		
		- 数据库文件
		
			在此处没有采用SQL方式备份，因为命令行备份下不太熟悉，本着数据本地持久化一定会是在文件里的思想，将 `/var/lib/mysql/` 下的相关文件拷贝
				- ccnet*
				- seafile*
				- ib*
		
	3. 停止服务
	
		- Mysql
		- Seafile
		- Seahub
		
2. 数据目录
	
	- 采用了将还原的数据覆盖安装时选择的目录方案
	- 更改目录权限为启动权限一致
	
3. 数据库

	1. root用户下进入数据库目录`/var/lib/mysql/`删除旧数据
		- ccnet*
		- seafile*
		- ib*
	2. 转移新数据进入数据库目录
	3. 更改新转移数据目录权限为mysql

### 遇见问题
- 采用最新版本无法正常使用
	发现官网有比备份版本更高版本[6.1.0]，提供了视频缩略图功能，需要依赖ffmpeg，虽然是beta版本，想来也挺有用，就试了试
	
	安装步骤跟之前一样，照着文档去一步一步，结果服务就是500，想来自己没有安装ffmpeg, 就跑去安装
	
	由于各种繁杂操作难度的原因(找不到或不能用apt安装~)，放弃安装ffmpeg, 同时又看到了seafile目录下的upgrade目录，想来是有升级脚本对数据进行兼容的，在考量了一下自己是要还原数据的，还是要版本一致，不然会导致不必要的麻烦，就没有深究这个问题，直接怂去旧版本
	
- Apache 环境下无法下载中文及带空格文件
	搜了搜相关问题
	
	- https://bbs.seafile.com/t/topic/1811
	- https://github.com/haiwen/seafile/issues/1258
		
	在参考了相关问题之后，决定怂去更换nginx
	
- nginx 下木有头像页面木有骨架不完整
	在重启服务重启树莓派确定是有问题的情况下，开始分析问题
	
	1. 将问题转化为关键字进行搜索引擎搜索，试图找到同一问题的伙伴，未果，问题未解决
	2. 看着页面觉得自己真不容易，搞个这耗这么久，自叹。问题未解决
	3. 想起在[HUX](https://huangxuan.me/)博客的某一篇文章看到的一句随口说的话"哥哥可是个前端好吗", 想来安卓也算半个前端，就使用了Chrome开发者工具分析页面网络请求，问题解决有方向
	4. 在network模块发现页面的css等内容请求响应为404，且首层path为media，遂去检查nginx media配置
	5. 最终发现根据官网直接复制来的配置代码中media的配置seafile安装路径与本地安装路径相驳，且官网文档未在此处声明需要替换，所以犯了这个错误，更改后问题解决
	
- 各种问题
	将安装目录、数据目录、等权限一致，有些不可描述的问题可能都与文件访问权限有关，初涉linux需要注意, 常用以下三个命令，具体操作可查看命令help。权限思想容易理解，落地控制还需要摸索摸索相关命令和常规使用方法
	
	- `chmod` 修改所有者权限
	- `chown` 修改所有者
	- `chgrp` 修改所有者组

### 安装nginx 1.10.*
因为seafile官方文档中使用nginx有这样一句提示

	如果要上传大于 4GB 的文件，默认情况下 Nginx 会把整个文件存在一个临时文件中，然后发给上游服务器 (seaf-server)，这样容易出错。使用 1.8.0 以上版本同时在 Nginx 配置文件中设置以下内容能解决这个问题：
		location /seafhttp {
			... ...
			proxy_request_buffering off;
		}
		
但是树莓派默认源中只包含nginx的1.6.*版本，故寻找安装nginx的方法，此处因使用成本问题，不到实在找不着，不去考虑手动编译，最终在debian官方源中找到编译的版本

> [https://packages.debian.org/sid/all/nginx/download](https://packages.debian.org/sid/all/nginx/download)

- 添加镜像源到 `/etc/apt/sources.list`
		
		# 可根据引用debian中文档选择最适合的镜像源
		deb http://ftp.cn.debian.org/debian sid main
		
- 更新、安装

		apt-get update
		apt-get install nginx
		
- 遇到问题
	
	应该是误操作原因，导致apt任何命令都提示nginx依赖未安装，apt无法正常使用
	参考了文章[ubuntu中apt-get install 无法使用的解决办法](http://blog.csdn.net/looong2b/article/details/21403325)
	执行以下命令解决(看了看帮助文档，不太理解用途)
	
		apt-get -f dist-upgrade

## 客户端

### 安装

	apt-get install seafile-cli

### 使用

程序帮助已经描述非常清楚，不赘述~

seaf-cli --help

	usage: seaf-cli [-h]
		{init,start,stop,list,list-remote,status,download,download-by-name,sync,desync,create,config}
		...

	optional arguments:
	  -h, --help            show this help message and exit

	subcommands:

	  {init,start,stop,list,list-remote,status,download,download-by-name,sync,desync,create,config}
	    init                Initialize config directory
	    start               Start ccnet and seafile daemon
	    stop                Stop ccnet and seafile daemon
	    list                List local libraries
	    list-remote         List remote libraries
	    status              Show syncing status
	    download            Download a library from seafile server
	    download-by-name    Download a library defined by name from seafile server
	    sync                Sync a library with an existing foler
	    desync              Desync a library with seafile server
	    create              Create a library
	    config              Configure seafile client


#### 资料库id获取方法

- 根资料库
	
	- 通过客户端子命令 `list-remote` 方式获取
	
			seaf-cli list-remote -s http://sample.seafile.com -u user_name -p user_password

				Name    ID
				disk 0e72563e-ce78-4f0d-b278-46687e062989
				私人资料库 5de2d7ef-69ec-4bcd-8456-b20673d2d865
				个人维基 d44c9cb7-cf12-4e28-9f6f-593fb3c8ca99
	
	- 通过web端链接方式
	
		在登录web端seafile之后，资料库跳转链接中直接会包含资料库id, 复制提取即可
		
			http://sample.seafile.com/#my-libs/lib/0e72563e-ce78-4f0d-b278-46687e062989
	
- 子资料库/资料库子目录
	
	有很多使用场景下是不需要同步/下载整个根资料库的，在seafile_gui客户端中也是存在同步某个根资料库中的子目录的功能，但是在terminal客户端中则没有直接提供相应命令。在个人需求驱动下无意间发现一个获取子目录id方法，可以用此子目录id像根资料库id一样执行相应同步/下载命令。获取子目录id需以下步骤

	1. 使用seafile_gui同步某个想要获取子目录id的目录
	2. 读取本机`seafile-data`目录下的SQLite格式`accounts.db`数据库文件
		可使用gui sqlite管理工具[SqliteExpert](http://www.sqliteexpert.com/), 官网有提供个人版本
	3. 查阅数据库中的表`SyncedSubfolder`
	4. 得到以下字段
		- rowid 列标
		- repo_id 库id
		- parent_repo_id 根库id
		- url 服务器url
		- username 用户名
		- parent_path 相对根路径
	5. 以上字段中的`repo_id`为需要的子目录id, 直接在客户端中当做根库id使用即可

## 参考资料
- [seafile-server-manual](http://manual-cn.seafile.com/)
- [seafile-user-manual - Seafile client for a Cli server](https://github.com/haiwen/seafile-user-manual/blob/master/en/desktop/linux-cli.md)
