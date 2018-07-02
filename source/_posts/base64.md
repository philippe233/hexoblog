---
title: base64编码
date: 2018-04-24 15:02:02
tags:
	- encode
category:
	- encode
---
*为什么当数据采用base64位编码传输时，传输的数据大小比真实文件大33%？这个在邮件传输时非常明显，经常碰到用户问为什么附件大小只有15M多一点，我服务器允许最大接收邮件时20M，但是邮件因为大小原因被拒绝。*


### 为什么需要base64

ASCII码一共规定了128个字符的编码,这128个符号,范围在[0,127]之间.其中,[0,31],及127, 33个属于不可打印的控制字符.

在电子邮件传输信息时,有些邮件网关会把[0,31]这些控制字符给悄悄清除.还有的早期程序,收到[128,255]之间的国际字符时,甚至会发生错误.

如何在不同邮件网关之间安全的传输控制字符,国际字符,甚至二进制文件?于是作为MIME多媒体电子邮件标准的一部分—base64被开发出来.

### 什么是base64

Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法。可查看RFC2045～RFC2049，上面有MIME的详细规范。

在[encoding文章](https://www.hopeline.cn/2018/04/23/encoding/#more)中我提到每个国家读通过不同的方式在ASCII基础上扩展自己的文字编码。既然每个国家都有自己的编码表了，问题也就来了。现在都国际化了，我要用一个支持本国语言的编码系统，打开另一个编码系统编码的文本，会出现什么情况呢？这就是乱码了… 更为严重的是，随着互联网的出现，各个国家的电脑都需要通信，而通信的一种方式就是使用URL地址。每个国家都希望把这个地址写成自己国家的语言。但这会导致其他国家根本没法访问地址，因为打不出这个字符嘛。所以，人类迫切需要一种中间编码形式，既能够兼容ASCII码，又能够把任意一种编码形式转换成只使用可读字符就能表示的编码。

其中一种编码形式，就是Base64编码。

Base64编码，顾名思义，用64个可读字符进行编码。与Hex的16个字符（0-9，A-F）相比多了很多，但是比ASCII码又少了一倍，去除了不可读字符。标准Base64编码中，这些字符是：

	数字（10个）：0,1,2,3,4,5,6,7,8,9
	小写字母（26个）：a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z
	大写字母（26个）：A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z
	加号以及斜杠（2个）：+，/

有的时候，根据不同的需要，Base64还有很多变种。比如，如果浏览器地址中用“+”和“/”的话，浏览器会将其转换为%XX的形式，又多了一步。因此可以将“+”和“/”换成“-”和“_”。

这种编码形式长度也短，效率也高。这样一来，数据通信的时候，不管来的是什么语言，都转化成Base64后再发送和接收。要是别国地址什么的打不出来，就直接打Base64编码形式就好了。

### 原理
对传输8Bit字节码的二进制数据进行处理，每3个字节一组，一共是3x8=24bit，划为4组，每组正好6个bit：

{% img 8to6bit /2018/04/24/base64/8to6bit.png %}

这样我们得到4个6bit数字作为索引，然后计算机是一个字节（8bit）存数，6bit不够，自动就补两个高位0了。 然后查下面表，获得相应的4个字符，就是base64编码后的字符串。

{% img 8to6bit /2018/04/24/base64/base64.png %}

__所以，Base64编码会把3字节的二进制数据编码为4字节的文本数据，长度增加33%，好处是编码后的文本数据可以在邮件正文、网页等直接显示。__

如果要编码的二进制数据不是3的倍数，最后会剩下1个或2个字节怎么办？Base64用\x00字节在末尾补足后，再在编码的末尾加上1个或2个=号，表示补了多少字节，解码的时候，会自动去掉。

转码过程例子：将字符s13编码成base64

	字符：s 1 3

	ascii：115 49 51

	2进制： 01110011 00110001 00110011

	6位一组（4组）： **011100**110011**000100**110011

	然后才有后面的： 011100 110011 000100 110011

	高位补0： 00011100 00110011 00000100 00110011

	得到： 28 51 4 51

	查对下照表： c z E z

### base64在线编码和解码

<http://www.webatic.com/run/convert/base64.php>


### 应用

* 用作HTTP表单和HTTP GET URL中的参数。

* 用作MIME格式邮件SMTP传输

* 用Base64来保密电子邮件密码 


### 代码实现

#### JavaScript

	if (!Shotgun)
		var Shotgun = {};
	if (!Shotgun.Js)
		Shotgun.Js = {};
	Shotgun.Js.Base64 = {
		_table: [
			'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P',
			'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', 'a', 'b', 'c', 'd', 'e', 'f',
			'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v',
			'w', 'x', 'y', 'z', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '/'
		],
	  
		encode: function (bin) {
			var codes = [];
			var un = 0;
			un = bin.length % 3;
			if (un == 1)
				bin.push(0, 0);
			else if (un == 2)
				bin.push(0);
			for (var i = 2; i < bin.length; i += 3) {
				var c = bin[i - 2] << 16;
				c |= bin[i - 1] << 8;
				c |= bin[i];
				codes.push(this._table[c >> 18 & 0x3f]);
				codes.push(this._table[c >> 12 & 0x3f]);
				codes.push(this._table[c >> 6 & 0x3f]);
				codes.push(this._table[c & 0x3f]);
			}
			if (un >= 1) {
				codes[codes.length - 1] = "=";
				bin.pop();
			}
			if (un == 1) {
				codes[codes.length - 2] = "=";
				bin.pop();
			}
			return codes.join("");
		},
		decode: function (base64Str) {
			var i = 0;
			var bin = [];
			var x = 0, code = 0, eq = 0;
			while (i < base64Str.length) {
				var c = base64Str.charAt(i++);
				var idx = this._table.indexOf(c);
				if (idx == -1) {
					switch (c) {
						case '=': idx = 0; eq++; break;
						case ' ':
						case '\n':
						case "\r":
						case '\t':
							continue;
						default:
							throw { "message": "\u0062\u0061\u0073\u0065\u0036\u0034\u002E\u0074\u0068\u0065\u002D\u0078\u002E\u0063\u006E\u0020\u0045\u0072\u0072\u006F\u0072\u003A\u65E0\u6548\u7F16\u7801\uFF1A" + c };
					}
				}
				if (eq > 0 && idx != 0)
					throw { "message": "\u0062\u0061\u0073\u0065\u0036\u0034\u002E\u0074\u0068\u0065\u002D\u0078\u002E\u0063\u006E\u0020\u0045\u0072\u0072\u006F\u0072\u003A\u7F16\u7801\u683C\u5F0F\u9519\u8BEF\uFF01" };
	  
				code = code << 6 | idx;
				if (++x != 4)
					continue;
				bin.push(code >> 16);
				bin.push(code >> 8 & 0xff);
				bin.push(code & 0xff)
				code = x = 0;
			}
			if (code != 0)
				throw { "message": "\u0062\u0061\u0073\u0065\u0036\u0034\u002E\u0074\u0068\u0065\u002D\u0078\u002E\u0063\u006E\u0020\u0045\u0072\u0072\u006F\u0072\u003A\u7F16\u7801\u6570\u636E\u957F\u5EA6\u9519\u8BEF" };
			if (eq == 1)
				bin.pop();
			else if (eq == 2) {
				bin.pop();
				bin.pop();
			} else if (eq > 2)
				throw { "message": "\u0062\u0061\u0073\u0065\u0036\u0034\u002E\u0074\u0068\u0065\u002D\u0078\u002E\u0063\u006E\u0020\u0045\u0072\u0072\u006F\u0072\u003A\u7F16\u7801\u683C\u5F0F\u9519\u8BEF\uFF01" };
	  
			return bin;
		}
	};


#### BASH

	base64Table=(A B C D E F G H I J K L M N O P Q R S T U V W X Y Z a b c d e f g h i j k l m n o p q r s t u v w x y z 0 1 2 3 4 5 6 7 8 9 + /);
	 
	function str2binary() {
		idx=0;
		for((i=0; i<${#str}; i++)); do
			dividend=$(printf "%d" "'${str:i:1}");
			for((j=0;j<8;j++)); do
				let idx=8*i+7-j;
				let bin[$idx]=$dividend%2;
				dividend=$dividend/2;
			done;
		done;
		let idx=${#str}*8;
		for((i=0; i<appendEqualCnt*2; i++)); do
			let bin[$idx]=0;
			let idx++;
		done;
	}
	function calcBase64() {
		for((i=0; i<${#bin[*]}/6; i++)); do
			sum=0;
			for((j=0; j<6; j++)); do
				let idx=i*6+j;
				let n=6-1-j;
				let sum=sum+${bin[$idx]}*2**n;
			done;
			echo -n ${base64Table[$sum]};
		done
	}
	 
	declare -a bin
	function base64Encode() {
		read -p "please enter ASCII string:" str;
		let appendZero=${#str}*8%6;
		let bits=${#str}*8;
		appendEqualCnt=0;
		if [[ $appendZero -ne 0 ]]; then
			let appendEqualCnt=(6-$appendZero)/2;
		fi
		str2binary;
		calcBase64;
		if [[ $appendEqualCnt -eq 2 ]]; then
			echo -n "==";
		elif [[ $appendEqualCnt -eq 1 ]]; then
			echo -n "=";
		fi
		echo;
		 
	}


#### Java

	import java.util.Base64;
	对于标准的Base64：
	加密为字符串使用Base64.getEncoder().encodeToString();
	加密为字节数组使用Base64.getEncoder().encode();
	解密使用Base64.getDecoder().decode();
	对于URL安全或MIME的Base64，只需将上述getEncoder()getDecoder()更换为getUrlEncoder()getUrlDecoder()
	或getMimeEncoder()和getMimeDecoder()即可。
