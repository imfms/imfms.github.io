# FreeBSD 安装 shadowsocks-server

> 参考[如何在FreeBSD中安装Shadowsocks并使用PAC科学上网](https://www.tomczhen.com/2015/12/08/howto-install-shadowsocks-in-freebsd-and-use-pac/)

1. 安装
		
	FreeBSD使用包管理器pkg
		
		pkg install shadowsocks-libev
		
	此步骤在操作时dpkg提示我升级，结果升级后dpk再使用则报错
	
		/usr/local/lib/libpkg.so.4: Undefined symbol "openat"
	
	[经过查询发现有人与我有相同的问题](https://yq.aliyun.com/ask/46495)，且一楼贴出了原因及临时解决方案，临时先使用pkg-static命令替换pkg
	
		pkg-static install shadowsocks-libev
		
2. 配置文件

	- 参考示例文件 `/usr/local/etc/shadowsocks-libev/config.json.sample`

			{
				"server":"127.0.0.1",
				"server_port":8388,
				"local_port":1080,
				"password":"barfoo!",
				"timeout":60,
				"method":null
			}

	- 通过 `ss-server help` 命令得知 -c 命令可以指定配置文件

	- 个人配置示例

			{
				"server":"0.0.0.0",
				"server_port":11111,
				"local_address":"127.0.0.1",
				"local_port":1080,
				"password":"password",
				"timeout":300,
				"method":"aes-256-cfb",
				"fast_open":false
			}

3. 配置服务启动

	1. 参考

		1. [shadowsocks-libev#freebsd](https://github.com/shadowsocks/shadowsocks-libev#freebsd)
		2. [FreeBSD简明用户指南:5.1. rc.conf章节](http://bsdelf.github.io/posts/freebsd-brief-user-guide)

				rc.conf 掌管着所有系统服务。与之相关的文件和路径如下：

				默认的配置位于 /etc/defaults/rc.conf。最好不要修改它，但是建议阅读一下它，看看 FreeBSD 默认做了哪些设置，开机启动哪些服务。
				用户自定义的配置位于 /etc/rc.conf。例如，如果想让系统自动启动 ssh、ipfw、nginx 等服务，就要修改本文件。注意，如果某项配置与默认的配置有冲突，则以本文件为准。
				基系统的服务脚本位于 /etc/rc.d/。第三方应用的服务脚本位于 /usr/local/etc/rc.d/。当遇到问题时，建议阅读这些脚本，找出问题所在。
				其次，service 命令可用于控制各种服务项。对于 rc.conf 中已启用的服务，我们可以这样操作：

				让正在运行中的 nginx 重载配置文件：service nginx reload。
				停止 nginx 服务：service nginx stop。
				对 em0 接口重启 DHCP 服务： service dhclient restart em0。
				当然也可以直接调用 /etc/rc.d/ 和 /usr/local/etc/rc.d/ 下的那些脚本，只不过要多打一些字：

				/usr/local/etc/rc.d/nginx reload
				/usr/local/etc/rc.d/nginx stop
				/etc/rc.d/dhclient restart em0
				如果 rc.conf 中并没有启用某项服务，但我们想临时启动它，那么可以这样（以 tomcat7 为例）：

				service tomcat7 onestart
				service tomcat7 onestop

	2. 问题寻觅过程
		
		1. 按照对debian的经验，服务脚本应该存在于 `/etc/init.d` 中
		2. 发现freebsd无此文件夹，发现 `/etc`目录下存在 `rc.d` 隐隐觉得该目录应该功能与服务相关相关
		3. 进入`/etc/rc.d`发现相关服务脚本，但未能找到`shadowsocks`相关服务脚本，但是明显参考1中描述是存在脚本的
		4. 根据参考2了解到 `/etc/rc.d` 是用来存放系统服务脚本，`/usr/local/etc/rc.d`用来存放第三方应用服务脚本，`/etc/rc.conf` 用来配置要启动的某些服务
		5. 发现服务脚本 `/usr/local/etc/rc.d/shadowsocks_libev`
		
				#!/bin/sh
				# $FreeBSD: head/net/shadowsocks-libev/files/shadowsocks_libev.in 335066 2013-11-27 22:04:32Z delphij $

				# PROVIDE: shadowsocks-libev
				# REQUIRE: LOGIN cleanvar
				# KEYWORD: shutdown

				# Add the following lines to /etc/rc.conf to enable shadowsocks-libev:
				# shadowsocks_libev_enable (bool):  Set to "NO" by default.
				#      Set to "YES" to enable shadowsocks-libev.
				# shadowsocks_libev_config (path): Shadowsocks config file.
				#      Defaults to "/usr/local/etc/shadowsocks-libev/config.json"


				. /etc/rc.subr

				name="shadowsocks_libev"
				rcvar=shadowsocks_libev_enable


				load_rc_config $name

				: ${shadowsocks_libev_enable:="NO"}
				: ${shadowsocks_libev_config="/usr/local/etc/shadowsocks-libev/config.json"}

				command="/usr/local/bin/ss-server"
				pidfile="/var/run/shadowsocks-libev.pid"
				required_files="${shadowsocks_libev_config}"

				command_args="-f $pidfile -c $shadowsocks_libev_config"

				run_rc_command "$1"

	3. 配置

		1. 添加以下内容到 `/etc/rc.conf`

				shadowsocks_libev_enable="YES"
				# shadowsocks_libev_config="配置文件路径" # 通过`/usr/local/etc/rc.d/shadowsocks_libev`获取到信息，该键可以指定配置文件路径

		2. 执行命令启动服务

				service shadowsocks_libev start
