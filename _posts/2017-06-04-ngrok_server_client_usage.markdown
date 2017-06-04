---
layout: post
title:  "ngrok server-client 使用总结"
date:   2017-06-04 11:50:30 +0800
categories: server
catalog:    true
tags: 
- linux
- ngrok
- network
- server
---

# ngrok server-client 使用总结

- 测试环境
	- 编译平台: ubuntu
	- 服务器平台: debian, freebsd, windows, centos
	- 客户端平台: debian, freebsd, windows, centos

## 编译应用

1. 准备环境

	1. 安装 `build-essential`

			sudo apt-get install build-essential
		
	2. 安装 `golang`

			sudo apt-get install golang
		
	3. 安装 `mercurial`

			sudo apt-get install mercurial
		
	4. 安装 `git`
	
			sudo apt-get install git
		
2. 获取 `ngrok` 源码

	个人至此篇文章撰写, ngrok开源最新版本为`1.7.1`
	
		git clone https://github.com/inconshreveable/ngrok.git ngrok
		cd ngrok
	
3. 生成自签名 SSL 证书

	替换 `$yourdomain` 为你的域名
	
	例如你想ngrok客户端映射的地址为 (前提你拥有此域名) `xxx.example.com` 则填入 `example.com`, `xxx.tunnel.example.com` 则填入 `tunnel.example.com`

		openssl genrsa -out rootCA.key 2048
		openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$yourdomain" -days 5000 -out rootCA.pem
		openssl genrsa -out device.key 2048
		openssl req -new -key device.key -subj "/CN=$yourdomain" -out device.csr
		openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000
	
4. 替换默认应用证书文件

		cp rootCA.pem assets/client/tls/ngrokroot.crt
		cp device.crt assets/server/tls/snakeoil.crt 
		cp device.key assets/server/tls/snakeoil.key
	
5. 编译应用

		make GOOS="$OS" GOARCH="$CPU_ARCHITECTURE" release-server release-client
	
	`GOOS`为指定目标应用的操作系统平台，`GOARCH`为指定目标应用的操作系统平台的CPU架构，例如要生成windows-386应用则需要执行以下命令
	
		make GOOS="windows" GOARCH="386" release-server release-client
	
	具体支持的系统平台和cpu架构列表可参考此文档[https://golang.org/doc/install/source#environment](https://golang.org/doc/install/source#environment)
	
	将会生成`ngrok-server`、`ngrok-client`到 `bin/`目录下, `ngrok` is client, `ngrokd` is server


## 配置域名解析

1. 记录类型 `A`
2. 主机记录 `*.子域名名称`

	想要映射地址为 `xxx.example.com` 则直接填写 `*`
	想要映射地址为 `xxx.subdomain.example.com` 则填写 `*.subdomain`

3. 记录值 `服务器ip地址`

## 配置服务器

1. 将对应平台ngrok服务端上传到服务器
	
	windows平台用户可使用[winscp](https://winscp.net)
	
2. 添加ngrok服务器文件执行权限
	
		chmod +x "${ngrok_server_path}"
	
3. 指定域名参数启动ngrok服务器

		${ngrok_server_path} -domain="${domain}"
	
	启动后使用浏览器访问域名响应 `Tunnel ${domain} not found` 则为成功

4. (可选) 配置为服务开机启动

	参考[指定脚本开机自启](https://github.com/imfms/imfms.github.io/blob/master/_posts/2017-05-10-raspberry-install-log.markdown#%E6%8C%87%E5%AE%9A%E8%84%9A%E6%9C%AC%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF)
	
	自己的服务配置脚本, 随手复制的其他服务脚本更改后，只支持`start`
		
		#!/bin/bash

		### BEGIN INIT INFO
		# Provides: ngrokd
		# Required-Start: $local_fs $network $remote_fs
		# Required-Stop: $local_fs $network $remote_fs
		# Default-Start: 2 3 4 5
		# Default-Stop: 0 1 6
		# Short-Description: ngrokd
		# Description: ngrokd
		### END INIT INFO


		status() {
				if [ -f "$LOCKFILE" ]; then
					echo "$base is running"
				else
					echo "$base is stopped"
				fi
				exit 0
		}

		case "$1" in
			restart)
				;;

			start)
				${ngrok_server_bin_path} -domain="${domain}" &
				;;

			stop)
				;;

			status)
				;;

			*)
				echo $"Usage: $0 {start|stop|status|restart}"
				exit 3
				;;
		esac


## 配置客户端

- 使用命令行参数
	
	自行查看帮助
	
		ngrok help
		
- 配置文件配置项

		server_addr: $domain:4443 # 服务器地址
		trust_host_root_certs: false # 如使用自签名证书，则须添加此键且指定值为 `false`
		inspect_addr: disabled # 关闭web观察器
	*个人使用的1.7.1版本，存在内存泄露，在正常使用期间会将客户端内存耗尽，关闭web观察器可使其保持稳定，具体内容参考此链接[ngrok/issues/Unknown, big memory-leak](https://github.com/inconshreveable/ngrok/issues/109), 且有作者的回答*
		
		tunnels: # 指定通道列表
		  ${tunnel_name}: # 通道名
			proto:
			  ${proto}: ${port_number} # 协议, 端口号

- (可选)配置为服务开机启动
参考服务器配置方法

## 参考文档
- [官方文档](https://github.com/inconshreveable/ngrok/blob/master/docs/DEVELOPMENT.md)
- [Run Ngrok on Your Own Server Using Self-Signed SSL Certificate](http://www.svenbit.com/2014/09/run-ngrok-on-your-own-server)
- [Ngrok搭建服务器 搭建自己的ngrok服务器](http://blog.lzp.name/archives/24)
