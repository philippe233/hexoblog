---
title: Linux look up route
date: 2018-04-11 11:56:01
tags:
	- linux
	- route
category:
	- linux
	- command
---

本文介绍几种获取linux当前系统路由表的命令
### route
	[root@localhost ~]# route
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
	default         192.168.150.1   0.0.0.0         UG    1024   0        0 eno16780032
	192.168.150.0   0.0.0.0         255.255.255.0   U     0      0        0 eno16780032

### ip rule
	[root@localhost ~]# ip rule
	0:	from all lookup local 
	32766:	from all lookup main 
	32767:	from all lookup default

### ip route list table
    [root@localhost ~]# ip route list table all
    default via 192.168.150.1 dev eno16780032  proto static  metric 1024 
    192.168.150.0/24 dev eno16780032  proto kernel  scope link  src 192.168.150.151 
    broadcast 127.0.0.0 dev lo  table local  proto kernel  scope link  src 127.0.0.1 
    local 127.0.0.0/8 dev lo  table local  proto kernel  scope host  src 127.0.0.1 
    local 127.0.0.1 dev lo  table local  proto kernel  scope host  src 127.0.0.1 
    broadcast 127.255.255.255 dev lo  table local  proto kernel  scope link  src 127.0.0.1 
    broadcast 192.168.150.0 dev eno16780032  table local  proto kernel  scope link  src 192.168.150.151 
    local 192.168.150.151 dev eno16780032  table local  proto kernel  scope host  src 192.168.150.151 
    broadcast 192.168.150.255 dev eno16780032  table local  proto kernel  scope link  src 192.168.150.151 
    local ::1 dev lo  proto kernel  metric 256 
    unreachable ::/96 dev lo  metric 1024  error -101
    unreachable ::ffff:0.0.0.0/96 dev lo  metric 1024  error -101
    unreachable 2002:a00::/24 dev lo  metric 1024  error -101
    unreachable 2002:7f00::/24 dev lo  metric 1024  error -101
    unreachable 2002:a9fe::/32 dev lo  metric 1024  error -101
    unreachable 2002:ac10::/28 dev lo  metric 1024  error -101
    unreachable 2002:c0a8::/32 dev lo  metric 1024  error -101
    unreachable 2002:e000::/19 dev lo  metric 1024  error -101
    unreachable 3ffe:ffff::/32 dev lo  metric 1024  error -101
    fe80::/64 dev eno16780032  proto kernel  metric 256 
    unreachable default dev lo  table unspec  proto kernel  metric 4294967295  error -101
    local ::1 dev lo  table local  proto none  metric 0 
    local fe80::20c:29ff:fe9f:7ea6 dev lo  table local  proto none  metric 0 
    ff00::/8 dev eno16780032  table local  metric 256 
    unreachable default dev lo  table unspec  proto kernel  metric 4294967295  error -101

### ip route get
	[root@localhost ~]# ip route get 114.114.114.114
	114.114.114.114 via 192.168.150.1 dev eno16780032  src 192.168.150.151 


