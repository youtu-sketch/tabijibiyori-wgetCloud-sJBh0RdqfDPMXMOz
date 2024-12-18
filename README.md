[合集 \- Java成长计划(5\)](https://github.com)[1\.Java面试必考题之线程的生命周期，结合源码，透彻讲解!03\-10](https://github.com/JavaBuild/p/18064848)[2\.当面试官问出“Unsafe”类时，我就知道这场面试废了，祖坟都能给你问出来！05\-25](https://github.com/JavaBuild/p/18211950)[3\.NIO实现聊天室之：一切都要从网络编程的基础开始聊起！10\-07](https://github.com/JavaBuild/p/18449792):[wgetcloud加速器官网下载](https://longdu.org)[4\.《一篇就够系列》之HTTP详解，覆盖高频面试考点！10\-28](https://github.com/JavaBuild/p/18510763)5\.面试官：DNS解析都整不明白，敢说你懂网络？我：嘤嘤嘤!12\-18收起
## 一、写在开头


在OSI七层协议模型中应用层是距离我们最近，且日后开发使用到最多的一层，在上一篇博文中我们已经学习了应用层中的HTTP协议，在本文中我们再一起来学一下DNS。啥？DNS不是很了解？那还不快往下看！



> ❝
> 其实早几年和一个朋友聊天，当时，他是一个网络条线的技术经理，向我吐槽说，现在来面试的很多都是学不好编程语言的，转来搞网络了，结果问一个DNS，他都整不明白，就这还好意思干网络？我当时吓得不敢说话，因为我自己对于网络编程也是那种浅尝辄止的心态，生怕他回头问我一个问题，哈哈。
> 
> 
> ❞


## 二、DNS的定义


DNS（Domain Name System）域名管理系统，是当用户使用浏览器访问网址之后，使用的第一个重要协议。DNS 要解决的是域名和 IP 地址的映射问题。


![image](https://img2024.cnblogs.com/blog/3271023/202412/3271023-20241218082646374-1388425058.png)



`DNS是一个联机的分布式数据库系统，它具有分布式、层次化、模块化等特点，它属于应用层的协议，基于UDP传输，端口为53。`


## 三、DNS服务器类别


我们上面说到DNS具有层次化结构，而这个层次化主要体现在它的服务器部署之上，DNS的域名层次结构是一棵树，从**「根域名服务器」**到**「顶级域名服务器」**、**「权威域名服务器」**，再到**「本地域名服务器」**，而我们所有遇到的DNS服务器，都囊括在这四类之中。


* **「根域名DNS服务器」**：是为提供TLD服务器的IP地址，目前世界上仅有13组根服务器，我国境内仍然没有，不过后来任播应用后，DNS分解出1089个根域名服务器节点，而在我国便有了26个根域名服务器节点。
* **「顶级域名服务器（TLD服务器）」**：顶级域名指的域名的后缀，像com、org、net等都是，不同国家也有自己的域名，如uk、ca、fr这些，我国的是cn。而顶级域名系统则是提供权威DNS服务器IP地址的。
![image](https://img2024.cnblogs.com/blog/3271023/202412/3271023-20241218082804888-151587763.png)
* **「权威域名DNS服务器」**：所有在因特网上具有公共可访问主机的组织机构，必须提供可访问的DNS记录，而这些记录里则保存着域名与IP映射等信息，而这些记录则存储在权威DNS服务器上。
* **「本地域名服务器」**：每个 ISP（互联网服务提供商）都有一个自己的本地 DNS 服务器。当主机发出 DNS 请求时，该请求被发往本地 DNS 服务器，它起着代理的作用，并将该请求转发到 DNS 层次结构中。


那么了解了DNS的底层服务器结构，它们是如何合作的，或者说是通过怎样的机制，达到解析域名，响应到对应的IP上的呢？继续往下看！


## 四、DNS的解析过程


开局先上一张图：


![image](https://img2024.cnblogs.com/blog/3271023/202412/3271023-20241218082835485-884395342.png)

现在假设我们用浏览器去访问“ [www.baidu.com.cn” 的IP地址,那么DNS解析系统的整个解析过程大致可以分为如下这几步](https://github.com):


1. 第一步：客户端向本地DNS服务器发送DNS请求报文，报文中包含域名 www.baidu.com.cn ,若本地DNS中有相应的记录,直接反馈客户端,若没有则访问根DNS服务器,继续处理请求;
2. 第二步:本地DNS服务器向根域名服务器发送请求,根域名服务器是域名系统的最顶层，它负责管理所有顶级域名（如.com、.net、.org以及国家和地区代码如.cn）。根域名服务器会返回所查询域（在这个例子中是“.cn”域）的主域名服务器的地址;
3. 第三步:本地DNS在接收到根服务器返回的地址后,便向cn顶级域名服务器发送请求,一般到这里是有可能找到了对应的域名映射的IP地址的,但若还没有,它会查询自己的记录以找到“.com.cn”域的主域名服务器(权威DNS服务器)地址返回;
4. 第四步:本地DNS服务器只得继续向权威DNS服务器发去请求,终于,[www.baidu.com.cn已经向权威域名DNS备案过了,在这里找到了它对应的记录,便把记录返回给本地DNS；](https://github.com)
5. 第五步:本地dns服务器将收到的返回地址发给客户端，同时写入自己的缓存，以便下次查询;


**「需要注意的是:」** 从请求主机到本地 DNS 服务器的查询是递归的，其余的查询时迭代的;当找不到相应记录，会返回空结果，而不是超时信息。


## 五、DNS报文格式


DNS报文格式是DNS协议中用于客户端和服务器之间通信的一种结构化数据格式。DNS报文主要分为两种：查询报文和回答报文，它们具有相同的格式。


![image](https://img2024.cnblogs.com/blog/3271023/202412/3271023-20241218082941388-1759776536.png)



DNS（Domain Name System，域名系统）报文是在域名解析过程中，DNS客户端和DNS服务器之间传输的消息，其报文格式主要包括以下几个部分：


**「我们可以将其分成三个部分来看：」**


### 报文头部


* **「标识」**：16位的标识符，用于标识该DNS报文，客户端发送请求时会生成一个随机的标识，服务器在响应时会使用相同的标识，以便客户端能够将响应与请求对应起来。
* **「标志」**：16位的标志字段，包含多个标志位，用于表示报文的类型、操作码、响应状态等信息。例如，QR位表示是查询报文（0）还是响应报文（1）；OPCODE位表示操作码，通常为标准查询（0）等。
* **「问题数量」**：16位的字段，指明报文中包含的问题记录数量。
* **「回答数量」**：16位的字段，指明报文中包含的回答记录数量。
* **「权威机构数量」**：16位的字段，指明报文中包含的权威机构记录数量。
* **「附加信息数量」**：16位的字段，指明报文中包含的附加信息记录数量。


### 问题部分


* **「查询名称」**：可变长度字段，包含要查询的域名，域名采用一种特殊的编码方式，将域名的各个部分以标签的形式表示，每个标签前面有一个字节表示该标签的长度，最后以一个字节的0结束。
* **「查询类型」**：16位的字段，指定查询的资源记录类型，如A记录（表示主机地址）、NS记录（表示域名服务器）、CNAME记录（表示别名）等。
* **「查询类」**：通常为1，表示Internet类。


### 回答部分、权威机构部分和附加信息部分


这三个部分的格式基本相同，每个部分都可以包含多个资源记录，每个资源记录的格式如下：


* **「域名」**：与问题部分的查询名称格式相同，通常是被查询域名或相关域名。
* **「类型」**：16位的字段，与问题部分的查询类型相对应，表示该资源记录的类型。
* **「类」**：通常为1，表示Internet类。
* **「生存时间」**：32位的字段，以秒为单位，表示该资源记录在缓存中的有效时间。
* **「资源数据长度」**：16位的字段，指明后面的资源数据的长度。
* **「资源数据」**：可变长度字段，包含与该资源记录类型相关的具体数据，如A记录的IP地址、NS记录的域名服务器名称等。


## 总结


以上就是DNS域名管理系统涉及到的知识点啦，虽然很多后端程序员吗，在日后的工作中几乎不再深追DNS的底层原理，但初期，我们还是有必要了解一些网络上的内容的，毕竟企业内转岗比较常见。


