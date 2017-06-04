# ngrok server-client 使用记录

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

1. 步骤1

	- 记录类型 `A`
	- 主机记录 `子域名名称`

		想要映射地址为 `xxx.example.com` 则填写根域名标识符号(一般为@)
		想要映射地址为 `xxx.subdomain.example.com` 则填写 `subdomain`

	- 记录值 `服务器ip地址`

2. 步骤2

	- 记录类型 `A`
	- 主机记录 `*.子域名名称`

		想要映射地址为 `xxx.example.com` 则直接填写 `*`
		想要映射地址为 `xxx.subdomain.example.com` 则填写 `*.subdomain`

	- 记录值 `服务器ip地址`

## 配置服务器

1. 将对应平台ngrok服务端上传到服务器
2. 添加ngrok服务器文件执行权限
3. 指定域名参数启动ngrok服务器
4. 配置为服务开机启动
	

## 配置客户端

6. client config
	
		server_addr: $domain:4443
		trust_host_root_certs: false


## ngrok 配置并开机启动
1. [ngrok config 配置文档](https://github.com/inconshreveable/ngrok/blob/master/docs/DEVELOPMENT.md)
	
	Create an ngrok configuration file, "debug.yml" with the following contents片段
	
2. [配置开机启动](#指定脚本开机自启)


## 参考文档
- [Run Ngrok on Your Own Server Using Self-Signed SSL Certificate](http://www.svenbit.com/2014/09/run-ngrok-on-your-own-server)
- [Ngrok搭建服务器 搭建自己的ngrok服务器](http://blog.lzp.name/archives/24)