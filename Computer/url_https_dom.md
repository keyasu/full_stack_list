从输入url到页面加载完成发生了什么？

这是一道经典的面试题. 这道题没有标准的答案，它涉及很多的知识点 主页步骤如下.

##### 主页步骤

从前端的角度出发，我觉得首先回答必须包括以下几个基本的点：

1. 浏览器查找当前 URL 是否存在缓存，并比较缓存是否过期。
2. DNS 解析 URL 对应的 IP 。
3. 根据 IP 建立 TCP 连接（三次握手）。
4. HTTP 发起请求。
5. 服务器处理请求，浏览器接收 HTTP 响应。
6. 渲染页面，构建 `DOM` 树。
7. 关闭 TCP 连接（四次挥手）。

##### 一、URL

 我们常见的URL是这样的: http://www.baidu.com. 这个域名由三部分组成：协议名、域名、端口号，这里端口是默认所以隐藏。除此之外URL还会包含一些路径、查询或其他片段，例如：https://baike.baidu.com/search?word=你好
 我们最常见的的协议是 HTTP 协议，除此之外还有加密的 HTTPS 协议、FTP 协议等等。 URL 的中间部分为域名或者是 IP，之后是端口号。
通常端口号不常见是因为大部分都是使用默认端口，如HTTP默认端口80，HTTPS默认端口443。

##### 二、缓存

##### 三、DNS域名解析

首先我们应该要知道的是，在地址栏输入的域名并不是最后资源所在的真实位置，域名只是与IP地址的一个映射。网络服务器的IP地址那么多，我们不可能去记一串串的数字，因此域名就产生了，域名解析的过程实际是将域名还原为IP地址的过程。

DNS 域名解析有两种方法，分别是迭代查询和递归查询

DNS 域名解析（域名解析）, DNS 实际上是一个域名和IP对应的数据库。

IP地址往都难以记住，但机器间互相只认IP地址，于是人们发明了域名，让域名与IP地址之间一一对应，它们之间的转换工作称为域名解析，域名解析需要由专门的域名解析服务器来完成，整个过程是自动进行的。

首先浏览器先检查本地hosts文件是否有这个网址映射关系，如果有就调用这个IP地址映射，完成域名解析。
如果没找到则会查找本地DNS解析器缓存，如果找到则返回。
如果还是没有找到则会查找本地DNS服务器，如果找到则返回。

最后迭代查询，按根域服务器库（.com,.cn,.vip,.top...）->顶级域（.com），然后根据顶级域（.com）->第二层域子域（baidu.com），最后根据baidu.com的域名找到相应的IP，返回给浏览器。

##### 四、TCP连接
在通过上一步的DNS域名解析后，获取到了服务器的IP地址后，便会开始建立一次连接，这是由 TCP 协议完成的，主要通过三次握手进行连接。

第一次握手： 建立连接时，客户端发送 syn 包（syn=j）到服务器，并进入等待服务器确认的状态；

第二次握手： 服务器收到 syn 包，必须确认客户端的 syn（ack=j+1），同时自己根据 syn 生成一个 ACK 包，此时服务器进入等待状态；

第三次握手： 客户端收到服务器的 ACK 包，向服务器发送确认，此包发送完毕，客户端和服务器进入 ESTABLISHED（ TCP 连接成功）状态，完成三次握手。
##### 五、浏览器向服务器发送 HTTP 请求
完整的 HTTP 请求包含请求起始行、请求主体和请求头部三部分。
```
请求行的内容一般类似于：GET index.html HTTP/1.1
```
```
请求体一般包含请求的参数
prefixsug: %E4%BD%A0%E5%A5%BD
rsp: 4
bs: 你好
```
```
Request Headers
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Connection: keep-alive
Cookie: BIDjUf7ori1RhsHrKF; sugstore=1; BA_HECTOR=8g8085ag8lal2k04ej1gf592b0r; WWW_ST=1626514647729
Host: www.baidu.com
is_pbs: %E4%BD%A0%E5%A5%BD (这个是url编码的)
is_referer: https://www.baidu.com/s?wd=%E4%BD%A0%E5%A5%BD&rsv_spt=1&rsv_iqid=0xb3dea01200161c5b&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=0&rsv_dl=tb&rsv_sug3=7&rsv_sug1=4&rsv_sug7=101&rsv_btype=i&prefixsug=%25E4%25BD%25A0%25E5%25A5%25BD&rsp=4&inputT=3716&rsv_sug4=4613
is_xhr: 1
Referer: https://www.baidu.com/s?wd=%E4%BD%A0%E5%A5%BD&rsv_spt=1&rsv_iqid=0xb3dea01200161c5b&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&rqlang=cn&tn=baiduhome_pg&rsv_enter=0&rsv_dl=tb&oq=%25E4%25BD%25A0%25E5%25A5%25BD&rsv_btype=t&rsv_t=35cafjUtycecfgMCnOtPYUd9gvi%2Bc7C%2F1AgJQBqaoRf7ori1RMS%2BvW7Si2eHdthsHrKF&rsv_pq=da458e510014df33&prefixsug=%25E4%25BD%25A0%25E5%25A5%25BD&rsp=4
sec-ch-ua: " Not;A Brand";v="99", "Google Chrome";v="91", "Chromium";v="91"
sec-ch-ua-mobile: ?0
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36
X-Requested-With: XMLHttpRequest
```
##### 六、浏览器接收响应
服务器在收到浏览器发送的HTTP请求之后，会将收到的HTTP报文封装成HTTP的Request对象，并通过不同的Web服务器进行处理，处理完的结果以HTTP的Response对象返回，主要包括状态码，响应头，响应报文三个部分。

状态码：

##### 七、页面渲染
浏览器接收到 http 数据包后后, 向服务器发起请求下载资源, 然后解析流程(涉及到：html 的词法分析，然后解析成 DOm 树, 同时解析 css 生成 css 规则树, 合并生成render 树. 然后 layout 布局、painting 渲染、复合图层的合成、GPU绘制、外链接处理、loaded 和 documentloaded 等). 若在下载js文件，解析器会停止对 html 的解析，这就出现了 js 阻塞问题。

**预加载器**
当浏览器被脚本文件阻塞时, 预加载器（一个轻量级的解析器）会继续解析后面的html，寻找需要下载的资源。如果发现有需要下载的资源，预加载器在开始接收这些资源。预加载器只能检索HTML标签中的URL，无法检测到使用脚本添加的 URL，这些资源要等脚本代码执行时才会获取。

##### 八、关闭TCP连接或继续保持连接
通过四次挥手关闭连接
第一次握手是浏览器发完数据，然后发送FIN请求断开连接。
第二次握手是服务器向客户端发送ACK，表示同意。
第三次握手是服务器可能还有数据向浏览器发送，所以向浏览器发送 ACK 同时也发送 FIN 请求，是第三次握手。
第四次握手是浏览器接受返回的 ACK，表示数据传输完成。
至此, 浏览器和服务端 都知道了对方要断开链接了

---
End
---

