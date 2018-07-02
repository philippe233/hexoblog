---
title: ICMP Redirect Attack
date: 2018-04-12 14:57:47
tags:
	- linux
	- route
	- security
category:
	- security	
	- linux
---

### 原理分析
ICMP协议的redirect,在某些特定的环境下,还是有些用处的。具体的可以参看:
[When Are ICMP Redirects Sent](http://www.cisco.com/en/US/tech/tk365/technologies_tech_note09186a0080094702.shtml)
[Explanation of ICMP Redirect Behavior](http://support.microsoft.com/kb/195686/en-us/)

由于ICMP redirect可以动态的更改host的路由,从安全角度考虑,允许accept ICMP redirect的信息话带来的弊大于利。因此在*系统加固*的手册中,往往都建议将icmp redirect丢弃掉。

我想知道的是,在系统默认的配置下。通过构造特定ICMP redirect的数据包,被攻击者是否真的会受到影响；以及需要满足什么样的条件?

#### icmp redirect packet
一个标准的icmp redirect packet 如下所示:
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Type | Code | Checksum |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Gateway Internet Address |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Internet Header + 64 bits of Original Data Datagram |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
**TYPE**为5,表示为一个icmp redirect类数据；
**Code**有4类:
>0 = Redirect datagrams for the Network.
1 = Redirect datagrams for the Host.
2 = Redirect datagrams for the Type of Service and Network.
3 = Redirect datagrams for the Type of Service and Host.

其中Code为0的这类,在microsoft网站的文档中显示已经被废除掉了；我通过测试发现,Code设置为0和1是没有什么区别。

#### icmp redirect processes
一个正常的icmp redirect过程是这样的,host_A希望访问remote_A的tcp 123端口,当数据包发送到gateway_A,gateway_A发现到remote_A的路由应该走和host_A同网段的host_B,因此就会发 送一个icmp redirect信息,告诉你要访问remote_A的路由应该是走host_B,因此host_A就临时修改路由表,将访问remote_A的路由指向 host_B。

其实,操作系统设计人员,已经考虑到了可能会受到的攻击,因此当host_A收到icmp redirect数据时,会对该数据包进行验证,通过后才会修改自己的路由表。一下是我在freebsd6.2-7.2和linux center-os 5.2下测试的结果:
freebsd：
1.	icmp redirect数据包的的源ip地址必须是主机的default gateway;
2.	remote_A必须和自己不是同一个网段;
linux: 
1.	icmp redirect数据包的的源ip地址必须是主机的default gateway;
2.	remote_A必须和自己不是同一个网段;
3.	先前有发送访问remote_A的数据包，但是并不验证协议和端口号是否和icmp redirect回应的数据包相符。
4.host_B是存活的——可以通过arp学到host_B的mac地址。

从 以上这些限制中,可以看出linux比freebsd在icmp redirect这块儿是要严格一些。但是依然可以通过发送伪造的icmp redirect数据包,恶意修改被攻击者的路由表,修改被攻击者和特定ip之间的网络路径;实现流量劫持或者DoS的目的。更重要的是经过验证,该攻击 是可以跨网段的。

### 攻击实例
假设一台放在IDC内的服务器,操作系统为freebsd,ip地址为1.1.1.5。我们通过扫描,能够猜测到网关为1.1.1.1。这样我们可以从一台直接连接到互联网上的机器发起攻击(直接连接互联网的原因是为了避免SNAT)。使用[SING](http://sourceforge.net/projects/sing/)伪造ICMP攻击数据包:

	./sing -red -gw 1.1.1.4 -dest x.x.x.x -S 1.1.1.1 -x host -prot tcp -psrc 123 -pdst 123 1.1.1.5

这样,当freebsd接受到该icmp redirect数据包后,就会在路由表中添加一条到x.x.x.x的路由,该路由gw为1.1.1.4,可以通过netstat -rn查看。
	
	Destination Gateway Flags Refs Use Netif Expire
	default 1.1.1.1 UGS 0 11164121 em1
	x.x.x.x 1.1.1.4 UGHD3 0 0 em1 3600
......
过期时间为3600秒。如果1.1.1.4是不存在的ip或者没有转发功能,那么这台机器到x.x.x.x的网络将会中断。

如果针对linux的话,可能会有些限制。针对上个例子,就要求1.1.1.4这台机器是存活的同网段主机,不过这里机器应该不难找。

### 危害分析
这 类攻击有一个很大的限制,就是一个icmp redirect只能影响受攻击者和单独一个ip地址之间的正常通信;无法造成受攻击者完全被DoS。但是在具体的应用环境中,可以对一些关键设备实施攻 击,从而造成比较大的影响;毕竟对攻击者来说,发送几个icmp数据包的成本还是比较低的。例如:针对DNS服务器,可以将从A-M的的几个root server的ip地址通过icmp redirect,那么应该会造成影响(当然也要看具体DNS服务器的服务类型)。

### 修复建议
找到system内核参数配置文件/etc/sysctl.conf：

修改：

	###################################################################
	# Additional settings - these settings can improve the network
	# security of the host and prevent against some network attacks
	# including spoofing attacks and man in the middle attacks through
	# redirection. Some network environments, however, require that these
	# settings are disabled so review and enable them as needed.
	#
	# Do not accept ICMP redirects (prevent MITM attacks)
	#net.ipv4.conf.all.accept_redirects = 0
	#net.ipv6.conf.all.accept_redirects = 0
	# _or_
	# Accept ICMP redirects only for gateways listed in our default
	# gateway list (enabled by default)
	# net.ipv4.conf.all.secure_redirects = 1
	#
	# Do not send ICMP redirects (we are not a router)
	#net.ipv4.conf.all.send_redirects = 0`

为

	###################################################################
	# Additional settings - these settings can improve the network
	# security of the host and prevent against some network attacks
	# including spoofing attacks and man in the middle attacks through
	# redirection. Some network environments, however, require that these
	# settings are disabled so review and enable them as needed.
	#
	# Do not accept ICMP redirects (prevent MITM attacks)
	net.ipv4.conf.all.accept_redirects = 0
	net.ipv6.conf.all.accept_redirects = 0
	# _or_
	# Accept ICMP redirects only for gateways listed in our default
	# gateway list (enabled by default)
	# net.ipv4.conf.all.secure_redirects = 1
	#
	# Do not send ICMP redirects (we are not a router)
	net.ipv4.conf.all.send_redirects = 0

然后应用内核参数配置

	$ sudo sysctl -p

全部相关参数：

	net.ipv4.conf.all.accept_redirects = 0
	net.ipv6.conf.all.accept_redirects = 0
	net.ipv4.conf.all.send_redirects = 0
	
	net.ipv4.conf.default.accept_redirects = 0
	net.ipv6.conf.default.accept_redirects = 0
	net.ipv4.conf.default.send_redirects = 0



**reference**  
<http://www.cymru.com/gillsr/documents/icmp-redirects-are-bad.pdf>  
<http://sourceforge.net/projects/sing/>  
<http://tools.ietf.org/html/rfc792>