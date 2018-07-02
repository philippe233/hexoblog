---
title: web缓存之：基础知识
date: 2018-04-10 17:52:49
tags:
	- web
	- cache
	- HTTP
category:
	- web
---
>Caching is a technique that stores a copy of a given resource and serves it back when requested. When a web cache has a requested resource in its store, it intercepts the request and returns its copy instead of re-downloading from the originating server.
 
*Web缓存是指一个Web资源（如html页面，图片，js，数据等）存在于Web服务器和客户端（浏览器）之间的副本。缓存会根据进来的请求保存输出内容的副本；当下一个请求来到的时候，如果是相同的URL，缓存会根据缓存机制决定是直接使用副本响应访问请求，还是向源服务器再次发送请求。*

### Advantagement of Web Cache
* Improving the performance by reusing previously fetched resources. 
* Reducing latency and network traffic.
* Web sites become more responsive.

### Different kinds of caches
Cache从作用和部署位置来说有好几种类型：gateway caches, CDN, reverse proxy caches and load balancers 。这些有利于Web Server提高可用性，提升性能和横向扩展* 
{% img CacheCategory /2018/04/10/web-cache-basic-kb/CacheCategory.png %}  

#### Reverse Proxy Cache
代理服务器是浏览器和源服务器之间的中间服务器，浏览器先向这个中间服务器发起Web请求，经过处理后（比如权限验证，缓存匹配等），再将请求转发到源服务器。代理服务器缓存的运作原理跟浏览器的运作原理差不多，只是规模更大。可以把它理解为一个共享缓存，不只为一个用户服务，一般为大量用户提供服务，因此在减少相应时间和带宽使用方面很有效，同一个副本会被重用多次。常见代理服务器缓存解决方案有Squid等，这里不再详述。

#### CDN
CDN（Content delivery networks）缓存，也叫网关缓存、反向代理缓存。CDN缓存一般是由网站管理员自己部署，为了让他们的网站更容易扩展并获得更好的性能。浏览器先向CDN网关发起Web请求，网关服务器后面对应着一台或多台负载均衡源服务器，会根据它们的负载请求，动态将请求转发到合适的源服务器上。虽然这种架构负载均衡源服务器之间的缓存没法共享，但却拥有更好的处扩展性。从浏览器角度来看，整个CDN就是一个源服务器，从这个层面来说，本文讨论浏览器和服务器之间的缓存机制，在这种架构下同样适用。

#### Web应用层缓存
应用层缓存指的是从代码层面上，通过代码逻辑和缓存策略，实现对数据，页面，图片等资源的缓存，可以根据实际情况选择将数据存在文件系统或者内存中，减少数据库查询或者读写瓶颈，提高响应效率。

#### 浏览器端缓存
浏览器缓存根据一套与服务器约定的规则进行工作，在同一个会话过程中会检查一次并确定缓存的副本足够新。如果你浏览过程中，比如前进或后退，访问到同一个图片，这些图片可以从浏览器缓存中调出而即时显现。

#### SQL Cache
Web应用，特别是SNS类型的应用，往往关系比较复杂，数据库表繁多，如果频繁进行数据库查询，很容易导致数据库不堪重荷。为了提供查询的性能，会将查询后的数据放到内存中进行缓存，下次查询时，直接从内存缓存直接返回，提供响应效率。比如常用的缓存方案有memcached等。


### Controlling caching
The header: **Cache-control** 
>The Cache-Control HTTP/1.1 general-header field is used to specify directives for caching mechanisms in both requests and responses. Use this header to define your caching policies with the variety of directives it provides.
  
*HTTP1.1版本才添加的缓存控制机制，其在请求报文或响应报文首部添加一个cache-control的首部，用于定义资源的缓存最大时长，是相对于响应报文首部中的date首部定义的时间。一般响应报文首部会同时有Expires首部和Cache-control首部*

#### No cache storage at all
>The cache should not store anything about the client request or server response. A request is sent to the server and a full response is downloaded each and every time.  
*完全不缓存*

	Cache-Control: no-store
	Cache-Control: no-cache, no-store, must-revalidate

#### No caching
>A cache will send the request to the origin server for validation before releasing a cached copy.  
*会缓存，但是每次请求都会确认*

	Cache-Control: no-cache

#### Private and public caches
Private Cache 只能被单个用户使用。Public Cache可以被多个用户复用。

	Cache-Control: private
	Cache-Control: public

#### Expiration
The most important directive here is "max-age=<seconds\>" which is the maximum amount of time a resource will be considered fresh. Contrary to Expires, this directive is relative to the time of the request. For the files in the application that will not change, you can usually add aggressive caching. This includes static files such as images, CSS files and JavaScript files, for example.

For more details, see also the **Freshness** section below.

	Cache-Control: max-age=31536000

#### Validation
When using the "must-revalidate" directive, the cache must verify the status of the stale resources before using it and expired ones should not be used. For more details, see the **Validation** section below.

	Cache-Control: must-revalidate


对Cache-Control头不同的值归纳 

		cache-request-directive=no-cache 不接受缓存响应
                                no-store 不缓存在本地
                                max-age  缓存最大有效时长       
                                min-fresh 
        
		cache-response-directive=public
                                 private
                                 no-cache 
                                 no-store
                                 must-revalidate
                                 max-age
        
		// no-cache：可缓存，但用户每次请求都需要先到上游服务器做缓存检验

#### The Pragma header
Pragma 是一个 HTTP/1.0 header，在HTTP/1.1中并没有定义它为一个HTTP response头，因为我们已经有了 Cache-Control header。这个只是为了兼容HTTP/1.0的客户端。

	<META HTTP-EQUIV="Pragma" CONTENT="no-cache">

上述代码的作用是告诉浏览器当前页面不被缓存，每次访问都需要去服务器拉取。使用上很简单，但只有部分浏览器可以支持，而且所有缓存代理服务器都不支持，因为代理不解析HTML内容本身。

可以通过这个页面测试你的浏览器是否支持：[Pragma No-Cache Test](http://www.procata.com/cachetest/tests/pragma/index.php)


#### Varying responses
**Vary** response header从在Client上多个不同的cache副本筛选合适的版本
>The Vary HTTP response header determines how to match future request headers to decide whether a cached response can be used rather than requesting a fresh one from the origin server.

>When a cache receives a request that can be satisfied by a cached response that has a Vary header field, it must not use that cached response unless all header fields as nominated by the Vary header match in both the original (cached) request and the new request.

>The Vary header leads cache to use more HTTP headers as key for the cache.

>This can be useful for serving content dynamically, for example. When using the Vary: User-Agent header, caching servers should consider the user agent when deciding whether to serve the page from cache. If you are serving different content to mobile users, it can help you to avoid that a cache may mistakenly serve a desktop version of your site to your mobile users. In addition, it can help Google and other search engines to discover the mobile version of a page, and might also tell them that no Cloaking is intended.

>Because the User-Agent header value is different ("varies") for mobile and desktop clients, caches will not be used to serve mobile content mistakenly to desktop users or vice versa.

### Freshness
新鲜度:资源被存储到缓存后，必须要有回收机制（cache eviction）以释放占用的存储空间；另外因为Web资源可能会不停地更新，缓存也需要过期机制（expiration time），也就是缓存副本有效期。

#### Cache eviction  
1. 缓存项过期：缓存资源往往会被设置有效时长，过期自动清理或失效
2. 缓存空间用尽：缓存空间用尽时，会根据LRU（最近最小使用）算法清理缓存
3. 清理策略设置过长过短都不好，过长数据容易陈旧，过短起不到缓存效果

#### Lifetime
Show how a proxy cache acts when a doc is not cache, in the cache and fresh, in the cache and stale. Here is an example of this process with a shared cache proxy:

{% img  HTTPStaleness /2018/04/10/web-cache-basic-kb/HTTPStaleness.png %}


flow as follows:
{% img FreshnessLifetime /2018/04/10/web-cache-basic-kb/FreshnessLifetime.png %}

1. 是否过期（expeired）通过"**Cache-control: max-age=N**" header 或者 **Expires** header 判断。**max-age**根据**Date**header和**N**判读是否expired；**Expires**则会直接记录expiration time.
2. **Etag** header记录的是resource文件的MD5值，通过MD5判断server上该文件是否有改动。
3. **Last-Modified**记录resource文件最后update时间，精确到秒。  


**Cache-Control与Expires**
Cache-Control与Expires的作用一致，都是指明当前资源的有效期，控制浏览器是否直接从浏览器缓存取数据还是重新发请求到服务器取数据。只不过Cache-Control的选择更多，设置更细致，如果同时设置的话，其优先级高于Expires。

**Last-Modified/ETag与Cache-Control/Expires**
配置Last-Modified/ETag的情况下，浏览器再次访问统一URI的资源，还是会发送请求到服务器询问文件是否已经修改，如果没有，服务器会只发送一个304回给浏览器，告诉浏览器直接从自己本地的缓存取数据；如果修改过那就整个数据重新发给浏览器；

Cache-Control/Expires则不同，如果检测到本地的缓存还是有效的时间范围内，浏览器直接使用本地副本，不会发送任何请求。两者一起使用时，Cache-Control/Expires的优先级要高于Last-Modified/ETag。即当本地副本根据Cache-Control/Expires发现还在有效期内时，则不会再次发送请求去服务器询问修改时间（Last-Modified）或实体标识（Etag）了。

一般情况下，使用Cache-Control/Expires会配合Last-Modified/ETag一起使用，因为即使服务器设置缓存时间, 当用户点击“刷新”按钮时，浏览器会忽略缓存继续向服务器发送请求，这时Last-Modified/ETag将能够很好利用304，从而减少响应开销。

**Last-Modified与ETag**
你可能会觉得使用Last-Modified已经足以让浏览器知道本地的缓存副本是否足够新，为什么还需要Etag（实体标识）呢？HTTP1.1中Etag的出现主要是为了解决几个Last-Modified比较难解决的问题：

* Last-Modified标注的最后修改只能精确到秒级，如果某些文件在1秒钟以内，被修改多次的话，它将不能准确标注文件的新鲜度,属于弱检验（weak validator）
* 如果某些文件会被定期生成，当有时内容并没有任何变化，但Last-Modified却改变了，导致文件没法使用缓存
* 有可能存在服务器没有准确获取文件修改时间，或者与代理服务器时间不一致等情形
* Etag是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，能够更加准确的控制缓存。属于强检验（strong validator）
* Last-Modified与ETag是可以一起使用的，服务器会优先验证ETag，一致的情况下，才会继续比对Last-Modified，最后才决定是否返回304。
* Etag的服务器生成规则和强弱Etag的相关内容可以参考[《HTTP Header definition》](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)，	


### Reused resources
并非所有的数据被缓存或需要缓存，缓存是为了解决20%数据被80%的人频繁访问的问题而生。所有我们必须要考虑缓存的复用率。

#### The data we cache
数据如希望被缓存往往具备变化缓慢的特征。被缓存的数据往往具备如下特性：

**时间局部性**
缓存的数据往往被打有时间缀，具有定期失效的特征，过期后会从源服务器检验请求验证是否需要重新拉取数据。 
某数据被访问后，该数据往往会再次在短时间内被访问到。

**空间局部性**
被访问数据的周边数据被访问的概率会比其它常规数据访问大很多，所以这些访问数据和其它周边有可能被访问的数据通过某种方式集中在一起，以提高数据的被访问速度，减少数据查找时长。 
完成这类功能的工具往往称为Cache。

**热（区）数据**
所谓热（区）数据就是指经常被访问到的数据，这类数据被缓存最有价值，缓存命中率高

#### The data we do not cache
用户账号密码信息等数据，该类数据不仅不应该被缓存，反而要被着重保护，这些年发生的撞库，密码破解等恶性事件，往往都是因为用户个人不当心或企业安全意味不足，导致用户敏感信息流失。

#### Cache hit
缓存命中率=hit/(hit+mixx) 
hit表示缓存被命中，miss表示没有命中，也就是缓存项中没有对应的资源 
文档命中率：从文档命中的个数进行衡量 
字节命中率：从内容命中的大小(字节)进行衡量

 This is very important when web sites have CSS stylesheets or JS scripts that have mutual dependencies, i.e., they depend on each other because they refer to the same HTML elements.

{% img HTTPRevved /2018/04/10/web-cache-basic-kb/HTTPRevved.png %}



### Can NOT Caches
HTTP信息头中包含Cache-Control:no-cache，pragma:no-cache，或Cache-Control:max-age=0等告诉浏览器不用缓存的请求
需要根据Cookie，认证信息等决定输入内容的动态请求是不能被缓存的
经过HTTPS安全加密的请求（有人也经过测试发现，ie其实在头部加入Cache-Control：max-age信息，firefox在头部加入Cache-Control:Public之后，能够对HTTPS的资源进行缓存，参考《HTTPS的七个误解》）
POST请求无法被缓存
HTTP响应头中不包含Last-Modified/Etag，也不包含Cache-Control/Expires的请求无法被缓存