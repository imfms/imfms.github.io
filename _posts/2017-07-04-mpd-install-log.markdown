# MPD | Music Player Daemon 远程音乐播放器 安装/配置记录

> Music Player Daemon (MPD) is a flexible, powerful, server-side application for playing music. Through plugins and libraries it can play a variety of sound files while being controlled by its network protocol.

> 音乐播放器守护进程（MPD）是一个灵活，功能强大的服务器端应用程序，用于播放音乐。通过插件和库，它可以播放各种声音文件，同时可以通过网络协议控制

个人平时挺喜欢听音乐，并且相对喜欢外放，也买了个小小劣质音响，用于娱乐自己的闲暇coding时光

因为本身个人是自己管理曲库的，平时遇到喜欢的音乐都自行下载存储，并不依赖指定播放设备/环境。平时听音乐时觉得有线连接个人电脑有些麻烦，蓝牙连接又相对繁琐。想到自己角落的树莓派，于是以架设一个远程播放器的目标开始行动。

## 远程音乐播放器查找过程
- 因为之前入手树莓派时有见到过别人发树莓派使用心得有类似远程播放的功能，则直接以 `树莓派 远程播放` 为关键字进行搜索，发现大多为使用python自行脚本实现，想来如果自己短期自行小制作唯恐灵活性太低，遂考虑直接寻找相关linux下远程播放轮子
- 发现项目 [clementine](https://www.clementine-player.org), 试用之后发现clementine为gui实现，依赖相关gui库，但个人的树莓派的操作系统安装的是内核无gui版本，自己本身又排斥服务端存在gui，故考虑clementine为备选方案
- 发觉不得入门，直接进行相关经验搜索方式， 最终在知乎他人提问中的[McZoden](https://www.zhihu.com/people/yiwen-sun-14/answers)回答的[https://zhihu.com/question/27329130/answer/36179775](https://zhihu.com/question/27329130/answer/36179775)中得知最终方案播放器: MPD

## MPD 安装&配置

`端口、地址、密码、权限、音乐库、播放列表、自动更新库、自动更新库目录深度、防火墙配置`

## MPD client 连接

## 相关链接
- [Music Player Daemon](https://www.musicpd.org/)
- [Music Player Daemon Document](https://www.musicpd.org/doc/user/)
