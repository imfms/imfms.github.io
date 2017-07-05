---
layout: post
title:  "Samba 安装/配置记录"
date:   2017-07-05 12:50:30 +0800
categories: server
catalog:    true
tags: 
- raspberry
- linux
- server
- network
- file
---

# Samba 安装/配置记录
> Samba是一款linux下提供Windows网络文档共享服务使Windows与Linux之间可以数据交换的方便工具

## 安装

	apt-get install samba

## 基本使用配置

### 共享目录配置

> 配置文件路径: `/etc/samba/smb.conf`, 配置文件中有详细的示例和说明

- 配置

	- [global] 节点
		此节点下用于设置smb服务相关事项，工作组、日志、日志最大限制、是否接受访客
	- [自定义共享目录]节点
		- comment 注释
		- read only 是否只读
		- path 共享目录
		- create mask 文件创建权限
		- directory mask 目录创建权限
		- guest ok 是否允许访客
		- browseable 是否可发现

- 校验
> samba提供配置文件校验工具 `testparm`

	testparm # 校验配置文件
	
	testparm --help # 查看帮助

## 访问用户配置

> 配置工具 `pdbedit`

	pdbedit --help # 帮助
	pdbedit --list # 列出当前用户列表
	pdbedit --create # 创建用户
	pdbedit --modify # 修改用户
	pdbedit --delete # 删除用户

## 题外
之前试图安装在树莓派上安装samba， 一直未能成功，apt包管理器方式安装持续失败，使用的操作系统为[RASPBIAN JESSIE LITE 2017-02-27](https://downloads.raspberrypi.org/raspbian_lite/images/raspbian_lite-2017-02-27/)，
前些天通过 `apt upgrade` 更新了程序包，发现其中有类似树莓派201703XX内核的包程序被更新，遂重新安装samba, 成功

## 参考文档
- [Samba Documentation](https://www.samba.org/samba/docs/)
- [鳥哥的 Linux 私房菜 - 案伺服器之二： SAMBA 伺服器](http://linux.vbird.org/linux_server/0370samba.php)
