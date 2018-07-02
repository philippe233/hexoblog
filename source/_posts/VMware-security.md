---
title: VMware security
date: 2018-05-02 16:33:39
tags:
	- security
	- vmware
category:
	- security
	- vmware
---
*VMware虚拟化之后，典型的部署场景是这样的,对于下面的架构也会存在安全隐患*
{% img vmware /2018/05/02/VMware-security/vmware.png %}

### 风险点一

**管理员可能没有修改带外管理接口的默认密码，或者设置了弱密码、企业内众所周知的通用密码。**

1、首先，要有效管理和监控大量的物理服务器，管理员必须借助服务器提供的硬件管理接口和带外管理网络才能实现。例如，惠普的iLO接口，Dell和浪潮的IPMI接口，通过一个Web或Ssh界面，都能实现服务器硬件健康状态的监控、电源和开关、操作系统的安装、远程控制台等功能。

### 风险点二

**某些ESXi Server可能使用了弱密码，后来忘了修改。所有的ESXi Server使用相同的密码，也许写在了运维手册里。**

2、其次，在虚拟主机操作系统层面，管理员需要管理大量的ESXi Server，可能需要借助外包或驻场才能完成系统的安装和初始设置，然后才能投入使用。
关于弱密码实际是非常容易遇到的。

### 风险点三

**ESXi Server从来没有打过补丁，可能存在安全漏洞。**

3、再次，因为每个虚拟主机上都跑着几十上百个的虚拟客户机，使得管理员轻易做不敢对虚拟主机任何变更操作。

### 风险点四

**不同管理员，操作权限的控制**

4、再往更高的层走，到达vCenter这里
拿到vCenter的管理权限，便可以统治成百上千的虚拟机了。而管理成百上千台的虚拟机，肯定不是一两个人可以做得来的。也许需要按照功能区域划分给不同的人去管理，日常的变更操作也许会交给驻场团队去进行。这便涉及到账号和权限的安全问题。

在主要的vCenter上，也许域控服务器就在其中，你现在可以对它进行一个热克隆操作，克隆一个离线的虚拟机，然后用vCenter的控制台去登录它，导出域数据库，通过vCenter拷贝到其它你控制的虚拟机中（例如，通过共享虚拟磁盘），再把克隆的机器删除。这个过程对于域控管理员来说，一点感知都没有，域控服务器自身也不会有任何异常的系统事件产生。

### 风险点五

**外部web portal的安全问题**

5、让我们把目光投向更远的地方，落在那个称为“云”管理平台的系统上。实际上，它可能有其它的名字，叫“云”只是时髦一点。功能是类似的，就是通过Web门户，向内部IT用户提供便捷的通道去申请、维护和销毁虚拟机资源。这是一个很自然的需求，也有很多第三方厂家去做这样的平台。这样的平台也可能存在各种安全问题。

它的Web Portal账号是如何创建并管理的？它有多少个管理员权限的用户？它有没有默认密码？它的管理员账号日常是交给谁管理的？Web Portal有没有常见的Web漏洞，如SQL注入等。它后台的服务器包括数据库服务器有没有弱密码？它与vCenter、vSphere的联动是通过vCenter账号还是API Key来进行的？账号或API Key有没有加密存储？等等。

### 风险点六

**通过端口扫描获取虚拟机信息**

6、补充：VMware产品的扫描和发现
作为一个内部渗透人员，如果对企业环境中的VMware产品（包括vCenter、ESXi等）进行发现和识别呢？这个也是有技巧的。首先VMware产品有特定的服务端口，例如22,80,427,443,902,9875等。其次服务的banner信息，或者ssl证书信息中包含有VMware或vSphere等关键字。这样就可以使用zmap等扫描器+banner获取快速地发现网络中VMware产品。那么，如何确定vCenter与它所纳管的ESXi之间的逻辑关系呢？诀窍就是SLP协议与vpxa的API。SLP协议可以获取目标IP地址的VMware主机名、ESXi版本，例如：

	~# /usr/bin/slptool 'unicastfindsrvs'  10.1.12.135 'service:VMwareInfrastructure' 
                            
	service:VMwareInfrastructure://10.1.12.135,65535
                            
	~# /usr/bin/slptool 'unicastfindattrs'  10.1.12.135 'service:VMwareInfrastructure'
                            
	(product="VMware ESXi 6.0.0build-1921158"),(hardwareUuid="32393735-3733-4E43-4731-313954385050")

而vpxa API可以查询到ESXi所纳管的vCenter地址：
URL为：url_fmt = ‘https://%s/vpxa‘ %(ip)
两个SOAP请求如下：
	apixml1='''<?xml version="1.0"encoding="UTF-8"?>
	                            
	<soapenv:Envelopexmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="                                http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
                            
	<soapenv:Body><QueryVpxaStatusxmlns="urn:vpxa3"><_this
 
	type="VpxapiVpxaService">vpxa</_this></QueryVpxaStatus></soapenv:Body></soapenv:Envelope>'''
                            
                            
	apixml2='''<?xml version="1.0"encoding="UTF-8"?>
                            
	<soapenv:Envelopexmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"                                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
                            
	<soapenv:Body><GetVpxaInfoxmlns="urn:vpxa3"><_thistype="VpxapiVpxaService">vpxa</_this></GetVpxaInfo></soapenv:Body></soapenv:Envelope>'''