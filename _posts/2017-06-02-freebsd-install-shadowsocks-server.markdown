# FreeBSD 安装 shadowsocks-server

> 参考[如何在FreeBSD中安装Shadowsocks并使用PAC科学上网](https://www.tomczhen.com/2015/12/08/howto-install-shadowsocks-in-freebsd-and-use-pac/)

1. 安装
		
	FreeBSD使用包管理器pkg
		
		pkg install shadowsocks-libev
		
	此步骤在操作时dpkg提示我升级，结果升级后dpk再使用则报错
	
		/usr/local/lib/libpkg.so.4: Undefined symbol "openat"
	
	[经过查询发现有人与我有相同的问题](https://yq.aliyun.com/ask/46495)，且一楼贴出了原因及临时解决方案，临时先使用pkg-static命令替换pkg
	
		pkg-static install shadowsocks-libev
		
2. 配置

> 参考官方示例[https://github.com/madeye/shadowsocks-libev/blob/master/debian/config.json](https://github.com/madeye/shadowsocks-libev/blob/master/debian/config.json)

通过 ss-server -c 参数可指定配置文件
	
ss-server help
	
		shadowsocks-libev 1.6.4

		maintained by Max Lv <max.c.lv@gmail.com> and Linus Yang <laokongzi@gmail.com>

		usage:

		ss-[local|redir|server|tunnel]

			  -s <server_host>           host name or ip address of your remote server
			  -p <server_port>           port number of your remote server
			  -l <local_port>            port number of your local server
			  -k <password>              password of your remote server


			  [-m <encrypt_method>]      encrypt method: table, rc4, rc4-md5,
										 aes-128-cfb, aes-192-cfb, aes-256-cfb,
										 bf-cfb, camellia-128-cfb, camellia-192-cfb,
										 camellia-256-cfb, cast5-cfb, des-cfb, idea-cfb,
										 rc2-cfb, seed-cfb, salsa20 and chacha20
			  [-f <pid_file>]            file to store the pid
			  [-t <timeout>]             socket timeout in seconds
			  [-c <config_file>]         config file in json


			  [-i <interface>]           network interface to bind,
										 not available in redir mode
			  [-b <local_address>]       local address to bind,
										 not available in server mode
			  [-u]                       enable udprelay mode
										 not available in redir mode
			  [-L <addr>:<port>]         setup a local port forwarding tunnel,
										 only available in tunnel mode
			  [-v]                       verbose mode


			  [--fast-open]              enable TCP fast open,
										 only available on Linux kernel > 3.7.0
			  [--acl <acl_file>]         config file of ACL (Access Control List)


个人配置示例

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

3. 配置服务
> 参考[https://github.com/shadowsocks/shadowsocks-libev/issues/19](https://github.com/shadowsocks/shadowsocks-libev/issues/19)

	1. 添加以下内容到 `/etc/rc.conf`
	
			shadowsocks_libev_enable="YES"
			shadowsocks_libev_flags="-c 配置文件位置"
			
	2. 执行命令启动服务
	
			service shadowsocks_libev start
			
实际在操作过程中发现服务启动之后除了会将shadowsocks_libev_flags内容添加到参数，还会自动追加参数以下参数

		-f /var/run/shadowsocks-libev.pid -c /usr/local/etc/shadowsocks-libev/config.json
		
其中-f为指定启动后pid存储位置，-c 为指定配置文件

但是刚刚已经在shadowssocks_libev_flags中指定了-c参数，而ss-server在这种情况下会识别最终指定的参数，故导致了不能达到预期的效果

不耗费太多精力和时间，故怂去直接修改`/usr/local/etc/shadowsocks-libev/config.json`为自己需要的参数，并注释`/etc/rc.conf`中的行 shadowsocks_libev_flags="-c 配置文件位置"
			
			
			
			
			
			
			
			