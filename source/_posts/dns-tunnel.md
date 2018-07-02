---
title: dns tunneling
date: 2018-05-14 16:10:32
tags:
	- network
	- security
	- dns
category:
	- security
	- network
---
_最经常碰到用户反馈，我们的反垃圾邮件网关总是向一些奇怪的地址做DNS查询请求，被防火墙检测出可能是DNS Tunnel的情况。从我们反垃圾邮件网关工作原理来说，确实会用到比较多的DNS查询来实现过滤的功能，但是该过滤功能跟DNS Tunnel是否有联系呢？_

{% img dnstunnelingsample /2018/05/14/dns-tunnel/dns-tunneling-sample.png %}

### what is DNS tunnel

DNS tunnel即DNS隧道。从名字上来看就是利用DNS查询过程建立起隧道，传输数据。

简单来说，当一个Client想要将数据发送给Server, Client会先把数据进行编码放在DNS payload中。例如Client可以发其域名为“MRZGS3TLEBWW64TFEBXXMYLMORUW4ZI.t.example.com”的A记录查询请求，然后Server返回“NVWW2IDPOZQWY5DJNZSQ.t.example.com”的CNAME的响应。这样数据以encode的方式发送给了Server，Server同时也将数据encode的方式响应了Client。这种通讯方式缺点是Server无法做到主动发起到客户的的连接，因为Client并没有监听DNS请求的服务，并且大多数情景是Client部署在防火墙后面。Server想要完成控制Client的通讯只有Client定期的监听发起到Serve的连接，然后Server通过响应数据包的控制Client通讯。

#### DNS Query

先了解下DNS查询请求的过程。DNS协议默认使用的端口为53(TCP/UDP),一般在进行DNS查询的时候通常使用的是UDP协议,但是在主服务器向备服务器同步数据的时候通常使用的是TCP协议.

{% img dnsquery /2018/05/14/dns-tunnel/dns-query.png %}

当你需要访问某个服务器时，需要知道其域名对应的服务器地址。

1. 你需要使用Local/Public DNS服务器进行查询，向该服务器的53端口发送查询请求，比如需要查询abc.sample.com的服务器地址。
2. 如果 Local/Public DNS服务器上如果没有abc.sample.com缓存记录，那么它将请求Root DNS服务器。
3. Root DNS会响应Local/Public DNS服务器sample.com的Name Server地址。
4. Local/Public DNS服务器再将请求转发到sample.com的服务器地址上。
5. sample.com的域名服务器上收到请求后，查看是abc.sample.com，如果它有这条A记录，那么就会返回abc.sample.com的地址给客户端。

#### Tunnel
再了解一下tunnel，所谓 tunnel 就是把下一层（比如IPv4层）的包封装到上一层（比如 SSH，HTTP）或者同一层（比如IPv6层）的协议中进行传输，从而实现网络之间的穿透。发送端和接收端各有一个解析和封装这种包的程序或者内核模块，将数据通过其他比较常用的通讯协议进行传输。常见的Tunnel有基于SSH或HTTPS的tunnel方式，这两种方式既是常用的通讯协议，又是基于加密的安全方式通讯。另外我们VPN中用到的PPTP，L2TP都是tunnel的技术实现的，Tunnel的实现方式从网络传输模型的2层-7层都有解决方案。

#### DNS Tunneling

**DNS隧道技术简单来说就是将网络流量封装成DNS流量,以DNS查询的方式将数据传输到服务器上，服务器再通过DNS查询结果的方式响应客户。这里的流量封装通常由一个客户端来完成,Tunnel服务器将封装的DNS流量还原成正常的流量.**

{% img dnstunnel /2018/05/14/dns-tunnel/dns-tunnel.png %}

在复杂和较为安全的网络环境中,防火墙方对内部网络出去的流量一般有严格的控制。攻击者拿到内网机器的权限后如果想保持长久的对目标的控制并且不被发现,难度是比较大的,因为一些敏感操作(比如:执行命令、内部数据外传等)可能会触犯防火墙或者安全设备的规则,有时候拿下一台机器容易,但是长久控制就比较难.

如果你在互联网上有台定制的服务器。只要依靠 DNS 的数据包，就可以实现数据交换，那么对内网的渗透难度会小很多。从 DNS 协议上看，你只是在一次次的查询某个特定域名，并得到解析结果。但实际上，你在和外部通讯，你没有直接连到局域网外的机器，因为防火墙不会转发你的 IP 包出去。但局域网上的 DNS 服务器帮你做了中转。这就是 DNS Tunnel了。通过DNS Tunnel可以比较好的维持对目标的长期控制并且不易被发现.DNS隧道将所有流量进行封装,通过DNS请求传送出去,一般的安全设备和软件不会对DNS请求进行详细的检查,攻击者通过将payload加密隐藏在查询的hostname中进行发送,DNS服务器递归查询,最终到达攻击者的服务端解密,服务端也可以下发指令给客户端,客户端解密之后执行控制端的命令。

{% img dnspayload /2018/05/14/dns-tunnel/dns-payload.png %}

### DNS tunnel implement tools

DNS tunnel实现的工具有很多，比如：OzymanDNS、tcp-over-dns、heyoka、iodine、dns2tcp

Win7平台：[dns2tcp](https://github.com/iagox86/dnscat2)
Linux平台：[iodine](https://github.com/yarrick/iodine)

这里只介绍基于Linux(CentOS)平台下iodine软件的实现方法.

#### Tools
域名：barracudachina.com
主机Linux两台：CentOS 7
Tunnel软件：iodine

#### Operations
**[Server]**

	# yum -y install iodine-server

	# iodined -f 10.0.0.1 -P SecretPassword tunnel.barracudachina.com
	Opened dns0
	Setting IP of dns0 to 10.0.0.1
	Setting MTU of dns0 to 1130
	Opened IPv4 UDP socket
	Listening to dns for domain tunnel.barracudachina.com

	# ifconfig
	dns0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1130
        	inet 10.0.0.1  netmask 255.255.255.224  destination 10.0.0.1
        	unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        	RX packets 59  bytes 4956 (4.8 KiB)
        	RX errors 0  dropped 0  overruns 0  frame 0
        	TX packets 59  bytes 4956 (4.8 KiB)
        	TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

**[Client]**

	# yum -y install iodine-client
	
	# iodine -f -r 192.158.0.1 -P SecretPassword tunnel.barracudachina.com   (Replace 192.168.0.1 with your server's ip address)
	Opened dns0
	Opened IPv4 UDP socket
	Sending DNS queries for tunnel.barracudachina.com to 192.168.150.158
	Autodetecting DNS query type (use -T to override).
	Using DNS type NULL queries
	Version ok, both using protocol v 0x00000502. You are user #0
	Setting IP of dns0 to 10.0.0.2
	Setting MTU of dns0 to 1130
	Server tunnel IP is 10.0.0.1
	Skipping raw mode
	Using EDNS0 extension
	Switching upstream to codec Base128
	Server switched upstream to codec Base128
	No alternative downstream codec available, using default (Raw)
	Switching to lazy mode for low-latency
	Server switched to lazy mode
	Autoprobing max downstream fragment size... (skip with -m fragsize)
	768 ok.. 1152 ok.. ...1344 not ok.. ...1248 not ok.. ...1200 not ok.. 1176 ok.. 1188 ok.. will use 1188-2=1186
	Setting downstream fragment size to max 1186...
	Connection setup complete, transmitting data.

	# ifconfig
	dns0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1130
        	inet 10.0.0.2  netmask 255.255.255.224  destination 10.0.0.2
        	unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        	RX packets 0  bytes 0 (0.0 B)
        	RX errors 0  dropped 0  overruns 0  frame 0
        	TX packets 0  bytes 0 (0.0 B)
        	TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

**[DNS]**
在域名解析商的网站上，设置一条barracudachina.com的子域tunnel.barracudachina.com的NS记录：

	tunnel.barracudachina.com. 600  IN      NS      ns10.barracudachina.com.

另外，再设置ns10.barracudachina.com的A记录指向上面的**[Server]**地址
	
	ns10.barracudachina.com. 600    IN      A       101.231.149.69

**[Test]**

	# dig -t TXT z456.tunnel.barracudachina.com
	z456.tunnel.barracudachina.com. 300 IN  TXT     "tpi0dknro"

	# dig -t SRV z456.tunnel.barracudachina.com
	z456.tunnel.barracudachina.com. 300 IN  SRV     10 10 5060 hpi0dknro.nx.

	# dig -t CNAME z456.tunnel.barracudachina.com
	z456.tunnel.barracudachina.com. 300 IN  CNAME   hpi0dknro.qf.
以上测试结果可以证明**[Client]**可以通过**[DNS]**递归查询实现与**[Server]**通信。

	# ping 10.0.0.1
	PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
	64 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=1.54 ms
	64 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=1.13 ms
	64 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=1.00 ms

该设置证明**[Client]**与**[Server]**之间的Tunnel已经建立成功。


**PS**:
以上设置有任何疑问可以通过[点击](https://github.com/yarrick/iodine)确认可能出现错误的原因。




### Detecting DNS Tunneling

因为DNS协议最初使用目的并不是用来传输数据的，其主要作用还是为了网站或邮件等重要服务。也正式这个原因，许多公司或组织往往会放行所有的DNS端口不做任何的监控。大部分企业将更多的资源专注于web或email的攻击，而忽略了通过DNS进行攻击的行为。例如最常见的情况为有些WIFI开启了http的portal进行身份验证后才能上网，因为这种情况下Client可以连接到DNS服务器查询，那么Client就可以通过DNS Tunnel获取免费的WIFI上网权限。也有攻击者通过建立DNS Tunnel获得远程控制主机权限，或者通过DNS Tunnel非法上传公司内部重要数据。

目前有很多工具可以实现DNS Tunnel，实现的方法也不近相同，有些工具通过在本地创建tap虚拟网卡并配置一个IP，服务器跟客户端建立了一个tunnel，所有tunnel数据封装在DNS请求和响应中传输。也有工具通过将数据直接以二进制数据的方式直接封装在DNS的请求和响应中。DNS请求类型也会不同，有的工具采用A记录方式，有的直接以Null的类型。要再说明一下的是数据在DNS payload中的编码（encode）。关于encode涉及的范围非常广如果想做更多的了解可以参考我之前的文档（[encoding彻底理解字符编码](https://www.hopeline.cn/2018/04/23/encoding/),[base64编码](https://www.hopeline.cn/2018/04/24/base64/)）。DNS Tunnel将encode后的数据封装在DNS的查询或请求中，encode格式也不太一样，像dns2tcp使用Base64编码，但是iodine又使用非标准的Base64编码格式...

基于以上的分析，许多DNS Tunneling工具都不会尝试隐匿，主要是因为实际情况下DNS不会被监控。目前有许多识别检测DNS Tunnel的技术，大致可以分为两类：基于数据包（Payload）分析和流量（traffic）分析

#### Payload Analysis

数据包分析识别技术主要基于域名生成的一般规律[Domain Generation Algorithms (DGA)],数据编码生成的域名与DGA生成的域名相比不太正常，主要有这几类：

##### Size of request and response
 DNS请求和响应的数据大小，通过这种在源和目的通信流量方法识别可疑的DNS Tunneling流量。既可以通过他们之间的通信数据统计量设置一个阀值（将DNS数据存储在MySQL中），也可以尝试检查DNS查询或相应的数据长度，因为DNS Tunneling总是尽可能的传输更多的数据。一个比较明显的判断依据是查看超过64-255字节的数据包，或者查看检查主机名请求长度超过52字节。

##### Entropy of hostnames
通过请求hostname的熵检测DNS Tunnel。合法的DNS名通常可以通过字典匹配或者看上去有正常含义的，但是编码过主机名有更高的熵即使他们都是使用字符集。虽然DNS名有些类型的有列外表示一些特定的含义，例如CDN，但是这是参考条件之一。

##### Statistical Analysis
检查包括的特殊字符在DNS名中是另外一种方法识别DNS Tunnel。合法的DNS名应该是比编码过的名字有更少的数字，有人提出通过基于在域名中数字的百分比，也可以通过在域名中通过最长的有意义的字符串等等。

##### Uncommon Record Types
检查请求的记录类型为不是非常用的类型，例如请求类型为TXT记录。

##### Policy Violation
如果设置一个策略要求所有DNS查询必须通过内部DNS Server，这种强制策略可以用于做检测DNS Tunnel，所有到Internet的DNS流量都可以被监控到，因为大部分DNS Tunnel工具都可以正常工作即使通过内网DNS Server转发的请求。

##### Specific Signatures
有些研究者提供了特征码为一些特定的DNS Tunnel工具，通过DNS数据包头部的特殊字段识别。例如Snort Signature被开发出来用于检测NSTX DNS Tunnel。
	alert udp $EXTERNAL_NET any -> $HOME_NET 53 (msg:"Potential NSTX DNS
	Tunneling"; content:"|01 00|"; offset:2; within:4; content:"cT";
	offset:12; depth:3; content:"|00 10 00 01|"; within:255; classtype:badunknown;
	sid:1000 2;)


#### Traffic Analysis
流量分析指的是通过查看一个时间段的多个请求/响应，这个数量和频率可以查看是否包括DNS Tunnel，这种技术方法主要包包括如下：

##### Volume of DNS traffic per IP address
一个比较直接的方法检查基于单个特定的Client IP的DNS流量，因为Tunnel的请求数据包大小一般限制在512字节，需要大量的请求包保持连接，并且如果Client建立了到Server的连接，那么会持续发送请求包。

##### Volume of DNS traffic per domain
基于某个特定域的DNS流量分析是不是有特别大。DNS Tunnel需要设置一个域名来转发数据，所有转发到该DNS Tunnel的数据都会使用这个域。当然也有可能会使用多个域的情况，这样分到每个域的流量就小了。

##### Number of hostnames per domain
统计一个给定的域名包括的hostname的数量，DNS Tunnel工具每次请求的hostname都不一样，这个比典型合法的域名要多很多，这是要很有效的流量分析方法。

##### Geographic location of DNS server
将地理位置作为一个考虑因素也会被使用，很多DNS的流量都是没有实际商业用途，如果企业不是遍布全球的话，那么这个方法很实用。

##### Domain history
域名历史也是可以用于提升识别DNS流量的方法，检查他们的A记录或者NS记录添加的情况，这个也常被用于检测一些恶意攻击行为，跟DNS Tunnel也相关。如果一个域名NS记录最近才被添加那么很有可能是用户DNS Tunnel的目的。

##### Orphan DNS requests
孤立的DNS请求，以上的这些方法都是基于我们能看到的流量进行分析。另外一种途径是预测我们可以看到的情况。通常DNS请求是被其他的请求的附加行为，例如Web请求，这种预测可以很容易过滤出来。安全设备可以会使用DNS反向查询，反垃圾邮件通过DNS查询确定一个给定的IP地址是不是在黑名单中，终端安全产品使用包括一个FQDN hash码的DNS查询检查信誉库或可疑的文件。

##### Others
查看NSDomain响应数量，流量可视化(Visualization)。


DNS[[数据包结构](https://www2.cs.duke.edu/courses/fall16/compsci356/DNS/DNS-primer.pdf)]
DNS Tunnel数据包[样本](https://github.com/philippe233/dus-tunnel-capture.git)
(https://www.sans.org/reading-room/whitepapers/dns/detecting-dns-tunneling-34152)

