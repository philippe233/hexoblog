---
title: Over The Great Wall
date: 2018-04-28 10:47:01
tags:
	- tool
category:
	- tool
---
*科学上网是每个有志知识青年的必备技能，不仅要翻过围墙看外面的世界，还要从外面看围墙内的世界*

### Tools

Client + Server + (Broswer Proxy Extension + GFW List)

通过安装客户端(Client)与在墙外的服务器(Server)建立加密的连接.这种连接除了VPN外，还有基于Shadowsocks这种代理的解决方案，当然也有一些直接的墙外代理服务器，但是所有的数据都会被代理看到，不是一个很安全的方式。(Broswer Proxy Extention + GFW List)这个可选的方案。使科学上网变得更完美。


### Client
#### Shadowsocks Solution (free)
##### Clients Download
页面不一定能打开，但是可以根据图片找到对应的平台搜索提供的Client，**Android或iOS**建议在APP Store中找Outline，亲测有效。(有人反馈在国内的Android应用中找不到Outline，已经上传到我的Github，[点击下载](https://github.com/philippe233/outline-for-android/archive/master.zip))

https://shadowsocks.org/en/download/clients.html

{% img client /2018/04/28/Over-The-Great-Wall/client.png %}

##### Client Config

<https://shadowsocks.org/en/config/quick-guide.html>


1. Config File
Shadowsocks accepts JSON format configs like this:

		{
    	"server":"my_server_ip",
    	"server_port":8388,
    	"local_port":1080,
    	"password":"barfoo!",
    	"timeout":600,
		"method":"chacha20-ietf-poly1305"
		}

2. Explanation of each field:

	server: your hostname or server IP (IPv4/IPv6).
	server_port: server port number.
	local_port: local port number.
	password: a password used to encrypt transfer.
	timeout: connections timeout in seconds.
	method: encryption method.

#### VPN Solution (charge)
通过VPN方案，选择一些付费的墙外VPN服务器，获取稳定，方便，快捷的科学上网方式。下面列出一些比较实用的VPN工具

* [ExpressVPN](https://www.express-vpn-mirror.club/techradar)
* [TunnelBear](http://click.tunnelbear.com/aff_c?offer_id=40&aff_id=2940)
* [Windscribe](https://windscribe.com/upgrade?promo=WS50OFF&afftag=trd-5380592452726525657&affid=fghzq9e1)
* [Hotspot Shield Free](http://hsselite.7eer.net/c/356741/64013/1691)
* [Speedify](http://speedify.evyy.net/c/221109/311275/3088)
* [ProtonVPN Free](https://account.protonvpn.com/signup)



### Server
#### Free Shadowsocks Server list
我们应该感谢这个开放的互联网世界，总有一些热心青年不求任何回报的分享这些免费的资源。这些SS Server都已经配置好了分享出来的，只需要通过将安装的Client配置连接其中的一个服务器，成功后你就可以开始科学上网了。

通过下面第一个Node配置到客户端上翻墙后，打开第二链接获取更多节点。

* [定时更新node](https://freess.pub/)

* [中国免费科学上网解决方案](https://blackpaperxyz.zdhweb.com/2017/02/my-free-ways-of-how-to-use-gmail-in-china.html)(需要翻墙)

* 个人珍藏Singapore Node：

	ss://YWVzLTI1Ni1jZmI6d3d3LnNoYWRvd3NvY2tzcGguc3BhY2VAMTI4LjE5OS42Ni4xNzY6NDQz


#### Private Shodowsocks Server Oversea
如果自己有一定的动手能力，AWS在墙外的服务器可以部署一台EC2 Server，最重要的12个月最低配置Free~。

[AWS EC2 SS Server 搭建教程](http://www.tyrion.wang/2017/02/04/VPN%E6%90%AD%E5%BB%BA-%E4%BA%9A%E9%A9%AC%E9%80%8AEC2-Shadowsocks/)



### Broswer Proxy Extension
有童鞋要问通过上面两个方法已经可以翻墙了，为什么还要这个浏览器插件。

情况是当使用上面工具后所有流量都通过翻墙到国外了，如果需要访问国内网站，通过翻墙代理后在连接国内服务器，会碰到特别慢和打不开的情况。

如果有一个开关，它可以自动根据你访问的域名选择走本地网络出去还是走VPN线路出去，需要时还可以手动选择，这个是不是很智能和实用！！！这就是下面要介绍的。

#### SwitchyOmega
真的非常好用，个人一直在使用的一个Chrome代理插件。

{% img switchyomege /2018/04/28/Over-The-Great-Wall/switchyomega.png %}

#### FoxyProxy
推荐FoxyProxy虽然没有SwitchyOmega好用，但是这个是不同浏览器支持的。

{% img foxyproxy /2018/04/28/Over-The-Great-Wall/foxyproxy.png %}



### GFW List
不多说，非常实用...而且持续更新

<https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt>


设置到上面的Proxy Extension中

{% img autoproxy /2018/04/28/Over-The-Great-Wall/autoproxy.png %}