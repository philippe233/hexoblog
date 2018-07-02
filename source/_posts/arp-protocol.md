---
title: ARP Protocol
date: 2018-05-08 14:52:42
tags:
	- network
category:
	- network
---
_ARP(Address Resolution Protocol)用于链路层地址发现的的协商协议。将已经分配了网络层的IPv4地址进行关联，该协议被定义在RFC 826标准中。
ARP已经应用于多种不同的数据链路层的技术。例如IPv4，Chaosnet，DECnet和PUP。FDDI，X.25,Frame Relay，ATM等。_


### Operating scope

ARP协议基于请求-响应链路层封装协议。数据包传输的边界为单个网络，不会再夸网段范围内传输。 ARP的该特性是基于因特网协议组的数据链路层工作模式。

### Packet strature

ARP使用简单消息格式包括地址解析的请求和响应包。包的大小基于上下层协议地址而决定。通常在IPv4网络协议下使用Hardware Address或virtual link address。消息报头定义了协议的类型和每个地址的大小。同事消息的头部Operation code定义了请求（1）和响应（2）。Payload中一共有4个地址，收发主机的Hardware Address和收发主机的IP地址。

下图中是一个标准的ARP数据包结构。如图所示，数据包包括48位的Sender Hardware Address（SHA）和Target Hardware Address（THA），以及32位的Sender/Target Protocol Address(SPA/TPA),因此协议包总共大小为28字节，ARP的以太帧类型ID为0x0806。

{% img arp /2018/05/08/arp-protocol/arp-payload.png %}

**Hardware type (HTYPE)**
该字段定义了网络层协议类型。 例如，以太网为1

**Protocol type (PTYPE)**
该字段定义数据包类型为ARP协议数据包，对于IPv4中对应的值为0x8000，改字段允许与以太帧其他类型共用。

**Hardware length (HLEN)**
Hardware Address长度，以太网中地址长度为6字节。

**Protocol length (PLEN)**
上层协议的地址长度（上层协议类型定义在PTYPE字段），在IPv4中大小为4字节。

**Operation** 
定义消息的操作类型。1表示请求，2表示响应。

**Sender hardware address (SHA)**
发送者的物理地址，在ARP请求包中该字段主要是用于表示请求消息的发送地址。在ARP响应包中，该字段被用于指示请求者需要查找的地址。对于交换机来说对改字段不会做任何更改只是用于MAC地址的学习。

**Sender protocol address (SPA)**
局域网中发送者的网络地址。

**Target hardware address (THA)**
数据包接收者的物理地址，在ARP请求包中该字段为空。在ARP响应包中该字段被用于表示原始ARP请求者的物理地址。

**Target protocol address (TPA)**
局域网中的目标接受者网络地址。

### Example
主机A的IP地址为192.168.1.1，MAC地址为0A-11-22-33-44-01；

主机B的IP地址为192.168.1.2，MAC地址为0A-11-22-33-44-02；

当主机A要与主机B通信时，地址解析协议可以将主机B的IP地址（192.168.1.2）解析成主机B的MAC地址，以下为工作流程：

第1步：根据主机A上的路由表内容，IP确定用于访问主机B的转发IP地址是192.168.1.2。然后A主机在自己的本地ARP缓存中检查主机B的匹配MAC地址。

第2步：如果主机A在ARP缓存中没有找到映射，它将询问192.168.1.2的硬件地址，从而将ARP请求帧广播到本地网络上的所有主机。源主机A的IP地址和MAC地址都包括在ARP请求中。本地网络上的每台主机都接收到ARP请求并且检查是否与自己的IP地址匹配。如果主机发现请求的IP地址与自己的IP地址不匹配，它将丢弃ARP请求。

第3步：主机B确定ARP请求中的IP地址与自己的IP地址匹配，则将主机A的IP地址和MAC地址映射添加到本地ARP缓存中。

第4步：主机B将包含其MAC地址的ARP回复消息直接发送回主机A。

第5步：当主机A收到从主机B发来的ARP回复消息时，会用主机B的IP和MAC地址映射更新ARP缓存。本机缓存是有生存期的，生存期结束后，将再次重复上面的过程。主机B的MAC地址一旦确定，主机A就能向主机B发送IP通信了。

### ARP probe
ARP检测是一类ARP请求包，其结构为发送者的网络IP地址（SPA）为空，被用于网络中IPv4地址冲突发现，当一台主机需要使用一个IPv4地址时，可以广播ARP probe包确认该IP地址是否正在被使用。

### ARP announcements
ARP也当简单的消息宣告使用，可以用于更新主机IP地址与MAC地址的映射出现变更。这种ARP类型也叫做gratuitous ARP（GARP）消息。常见的情况是消息发送者通过广播自己的网络地址（SPA）到目标区域网络，并且包的Target Hardware Address(THA)为空。

GARP请求消息和响应消息都是标准类型。ARP宣告并不是为了是对方产生响应消息，而是更新目标主机的的ARP Cache表。其OPeration code既可以是请求类型也可以是响应类型，因为在ARP标准中规定者这种操作只是更新ARP表。许多操作系统在系统启动阶段就可以处理GARP消息。 这有助于及时更新网络中其他设备的ARP-IP的映射表。

	[root@localhost ~]# arp -an
	? (192.168.150.236) at 00:50:56:9d:1f:96 [ether] on eno16780032
	? (192.168.150.246) at 00:17:54:02:07:6e [ether] on eno16780032
	? (192.168.150.57) at 00:50:56:9d:6a:ee [ether] on eno16780032
	? (192.168.150.13) at 00:50:56:9d:46:1e [ether] on eno16780032
	? (192.168.150.247) at 00:17:54:02:c7:f6 [ether] on eno16780032
	? (192.168.150.3) at 00:25:90:7a:a3:63 [ether] on eno16780032
	? (192.168.150.158) at 00:50:56:b9:a7:52 [ether] on eno16780032
	? (192.168.150.155) at 00:50:56:9d:77:05 [ether] on eno16780032
	? (192.168.150.1) at 00:10:f3:5d:fa:38 [ether] on eno16780032
	? (192.168.150.245) at 74:4b:e9:01:03:6c [ether] on eno16780032
	? (192.168.150.156) at <incomplete> on eno16780032


GARP有时也会用于设备网口的负载均衡，在一个借口组中，宣告不同的MAC地址在端口组中能够接受所有的数据包。

### ARP mediation
ARP中继用于解决二层地址需要夸越虚拟网络服务在复杂的网络环境中的问题。

### Reverse ARP
反向ARP解析被用于获得网络层地址（IP地址）通过二层数据链路地址。最开始用户Frame Relay和ATM网络。

主要是正常的网络层数据包转发时，通过ARP表查看IP->MAC决定转发的端口，但是有时数据报需要通过ARP表查看其MAC地址对应的网络地址，然后决定路由情况。

### Proxy ARP（ARP spoofing）
因为ARP协议并没有提供ARP响应的认证方法，所有ARP响应可以不是从真实的物理地址主机产生。ARP Proxy是一个主机响应ARP请求在真实的主机之前，常见是在拨号上网的情况下出现，另外对于ARP spoofing响应，利用ARP协议截获ARP请求包并对请求主机响应消息。黑客可以通过这种使用ARP spoofing这种方法扮演中间人的角色，获取传输过程的数据包。

{% img arp /2018/05/08/arp-protocol/arp-spoofing.png %}