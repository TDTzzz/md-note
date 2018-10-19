# TCP/UDP/IP/HTTP

[参考文章](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C.md#tcp-%E9%A6%96%E9%83%A8%E6%A0%BC%E5%BC%8F)

## TCP 首部格式

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/55dc4e84-573d-4c13-a765-52ed1dd251f9.png)](https://github.com/CyC2018/CS-Notes/blob/master/pics/55dc4e84-573d-4c13-a765-52ed1dd251f9.png)

- **序号** ：用于对字节流进行编号，例如序号为 301，表示第一个字节的编号为 301，如果携带的数据长度为 100 字节，那么下一个报文段的序号应为 401。
- **确认号** ：期望收到的下一个报文段的序号。例如 B 正确收到 A 发送来的一个报文段，序号为 501，携带的数据长度为 200 字节，因此 B 期望下一个报文段的序号为 701，B 发送给 A 的确认报文段中确认号就为 701。
- **数据偏移** ：指的是数据部分距离报文段起始处的偏移量，实际上指的是首部的长度。
- **确认 ACK** ：当 ACK=1 时确认号字段有效，否则无效。TCP 规定，在连接建立后所有传送的报文段都必须把 ACK 置 1。
- **同步 SYN** ：在连接建立时用来同步序号。当 SYN=1，ACK=0 时表示这是一个连接请求报文段。若对方同意建立连接，则响应报文中 SYN=1，ACK=1。
- **终止 FIN** ：用来释放一个连接，当 FIN=1 时，表示此报文段的发送方的数据已发送完毕，并要求释放连接。
- **窗口** ：窗口值作为接收方让发送方设置其发送窗口的依据。之所以要有这个限制，是因为接收方的数据缓存空间是有限的。



### 三次握手

---



[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/e92d0ebc-7d46-413b-aec1-34a39602f787.png)](https://github.com/CyC2018/CS-Notes/blob/master/pics/e92d0ebc-7d46-413b-aec1-34a39602f787.png)

假设 A 为客户端，B 为服务器端。

- 首先 B 处于 LISTEN（监听）状态，等待客户的连接请求。

- A 向 B 发送连接请求报文，SYN=1，ACK=0，选择一个初始的序号 x。

- B 收到连接请求报文，如果同意建立连接，则向 A 发送连接确认报文，SYN=1，ACK=1，确认号为 x+1，同时也选择一个初始的序号 y。

- A 收到 B 的连接确认报文后，还要向 B 发出确认，确认号为 y+1，序号为 x+1。

- B 收到 A 的确认后，连接建立。


**三次握手的原因**

第三次握手是为了防止失效的连接请求到达服务器，让服务器错误打开连接。

客户端发送的连接请求如果在网络中滞留，那么就会隔很长一段时间才能收到服务器端发回的连接确认。客户端等待一个超时重传时间之后，就会重新请求连接。但是这个滞留的连接请求最后还是会到达服务器，如果不进行三次握手，那么服务器就会打开两个连接。如果有第三次握手，客户端会忽略服务器之后发送的对滞留连接请求的连接确认，不进行第三次握手，因此就不会再次打开连接。



### 四次挥手

---



[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/f87afe72-c2df-4c12-ac03-9b8d581a8af8.jpg)](https://github.com/CyC2018/CS-Notes/blob/master/pics/f87afe72-c2df-4c12-ac03-9b8d581a8af8.jpg)

以下描述不讨论序号和确认号，因为序号和确认号的规则比较简单。并且不讨论 ACK，因为 ACK 在连接建立之后都为 1。

- A 发送连接释放报文，FIN=1。
- B 收到之后发出确认，此时 TCP 属于半关闭状态，B 能向 A 发送数据但是 A 不能向 B 发送数据。
- 当 B 不再需要连接时，发送连接释放报文，FIN=1。
- A 收到后发出确认，进入 TIME-WAIT 状态，等待 2 MSL（最大报文存活时间）后释放连接。
- B 收到 A 的确认后释放连接。

**四次挥手的原因**

客户端发送了 FIN 连接释放报文之后，服务器收到了这个报文，就进入了 CLOSE-WAIT 状态。这个状态是为了让服务器端发送还未传送完毕的数据，传送完毕之后，服务器会发送 **FIN** 连接释放报文。

**TIME_WAIT**

客户端接收到服务器端的 FIN 报文后进入此状态，此时并不是直接进入 CLOSED 状态，还需要等待一个时间计时器设置的时间 2MSL。这么做有两个理由：

- 确保最后一个确认报文能够到达。如果 B 没收到 A 发送来的确认报文，那么就会重新发送连接释放请求报文，A 等待一段时间就是为了处理这种情况的发生。
- 等待一段时间是为了让本连接持续时间内所产生的所有报文都从网络中消失，使得下一个新的连接不会出现旧的连接请求报文。





## UDP

### UDP 和 TCP 的特点

- 用户数据报协议 UDP（User Datagram Protocol）是无连接的，尽最大可能交付，没有拥塞控制，面向报文（对于应用程序传下来的报文不合并也不拆分，只是添加 UDP 首部），支持一对一、一对多、多对一和多对多的交互通信。

  **UDP面向报文**，面向报文的传输方式是应用层交给UDP多长的报文，UDP就照样发送，即一次发送一个报文。

- 传输控制协议 TCP（Transmission Control Protocol）是面向连接的，提供可靠交付，有流量控制，拥塞控制，提供全双工通信，面向字节流（把应用层传下来的报文看成字节流，把字节流组织成大小不等的数据块），每一条 TCP 连接只能是点对点的（一对一）

  **TCP面向字节**，面向字节流的话，虽然应用程序和TCP的交互是一次一个数据块（大小不等），但TCP把应用程序看成是一连串的无结构的字节流。TCP有一个缓冲，当应用程序传送的数据块太长，TCP就可以把它划分短一些再传送。如果应用程序一次只发送一个字节，TCP也可以等待积累有足够多的字节后再构成报文段发送出去。



## UDP 首部格式

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/d4c3a4a1-0846-46ec-9cc3-eaddfca71254.jpg)](https://github.com/CyC2018/CS-Notes/blob/master/pics/d4c3a4a1-0846-46ec-9cc3-eaddfca71254.jpg)

首部字段只有 8 个字节，包括**源端口、目的端口、长度、检验和**。12 字节的伪首部是为了计算检验和临时添加的。

## 



## IP

### IP 数据报格式

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/85c05fb1-5546-4c50-9221-21f231cdc8c5.jpg)](https://github.com/CyC2018/CS-Notes/blob/master/pics/85c05fb1-5546-4c50-9221-21f231cdc8c5.jpg)

- **版本** : 有 4（IPv4）和 6（IPv6）两个值；
- **首部长度** : 占 4 位，因此最大值为 15。值为 1 表示的是 1 个 32 位字的长度，也就是 4 字节。因为首部固定长度为 20 字节，因此该值最小为 5。如果可选字段的长度不是 4 字节的整数倍，就用尾部的填充部分来填充。
- **区分服务** : 用来获得更好的服务，一般情况下不使用。
- **总长度** : 包括首部长度和数据部分长度。
- **生存时间** ：TTL，它的存在是为了防止无法交付的数据报在互联网中不断兜圈子。以路由器跳数为单位，当 TTL 为 0 时就丢弃数据报。
- **协议** ：指出携带的数据应该上交给哪个协议进行处理，例如 ICMP、TCP、UDP 等。
- **首部检验和** ：因为数据报每经过一个路由器，都要重新计算检验和，因此检验和不包含数据部分可以减少计算的工作量。
- **标识** : 在数据报长度过长从而发生分片的情况下，相同数据报的不同分片具有相同的标识符。
- **片偏移** : 和标识符一起，用于发生分片的情况。片偏移的单位为 8 字节。

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/23ba890e-e11c-45e2-a20c-64d217f83430.png)](https://github.com/CyC2018/CS-Notes/blob/master/pics/23ba890e-e11c-45e2-a20c-64d217f83430.png)

## 

### 子网的划分

未做子网划分的ip地址：网络地址＋主机地址
做子网划分后的ip地址：网络地址＋（子网网络地址＋子网主机地址）





## HTTP

[参看文章](https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md)

#### 状态码

![img](https://user-gold-cdn.xitu.io/2018/1/15/160f9fc4bb3700a0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





#### HTTP的实现

HTTP是基于**TCP**协议的一种应用层协议，那我们就可通过系统提供的Socket来实现，实现步骤如下：

1. 建立一条TCP连接；
2. 按照HTTP请求报文的格式构造数据，构造完成之后发送到服务器端；
3. 接收服务器端的数据，根据HTTP的应答报文的格式，解析数据。
4. 断开TCP连接；



## URL

URI 包含 URL 和 URN，目前 WEB 只有 URL 比较流行，所以见到的基本都是 URL。

- URI（Uniform Resource Identifier，统一资源标识符）
- URL（Uniform Resource Locator，统一资源定位符）
- URN（Uniform Resource Name，统一资源名称）

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/urlnuri.jpg)](https://github.com/CyC2018/CS-Notes/blob/master/pics/urlnuri.jpg)



URL(Uniform Resource Locator) 地址用于描述一个网络上的资源, 基本格式如下:
 `schema://host[:port#]/path/.../[?query-string][#anchor]`

- scheme 指定低层使用的协议(例如：http, https, ftp)
- host HTTP服务器的IP地址或者域名
- port# HTTP服务器的默认端口是80，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如`http://www.bkjia.com:8080/`
- path 访问资源的路径
- query-string 发送给http服务器的数据
- anchor 锚

#### 例子

```
http://www.bkjia.com/sj/test/test.aspx?name=sviergn&x=true#stuff
```

- Schema: `http` 
- host: `www.mywebsite.com` 
- path: `/sj/test/test.aspx` 
- Query String: `name=sviergn&x=true` 
- Anchor: `stuff`



#### Request Headers

1. Host:客户端指定自己访问的web服务器的域名或者IP地址和端口号。localhost:3000

2. Accept: 用来告知客户端可以处理的内容类型。 */*：所有类型
3. Accept-Language:请求头允许客户端声明它可以理解的自然语言，并使用Content-Language 应答头通知客户端它的选择。zh-CN,zh;q=0.9,en;q=0.8 （;q=值代表优先顺序）
4. Accept-Encoding:gzip, deflate, br
5. User-Agent:用来告诉服务器，客户端使用的操作系统和浏览器的名称和版本信息。Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36


####  请求报文--Request Headers

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/HTTP_RequestMessageExample.png)](https://github.com/CyC2018/CS-Notes/blob/master/pics/HTTP_RequestMessageExample.png)



#### 响应报文--Response Headers

### 

[![img](https://github.com/CyC2018/CS-Notes/raw/master/pics/HTTP_ResponseMessageExample.png)](https://github.com/CyC2018/CS-Notes/blob/master/pics/HTTP_ResponseMessageExample.png)





### 1. 短连接与长连接

当浏览器访问一个包含多张图片的 HTML 页面时，除了请求访问 HTML 页面资源，还会请求图片资源。如果每进行一次 HTTP 通信就要新建一个 TCP 连接，那么开销会很大。

长连接只需要建立一次 TCP 连接就能进行多次 HTTP 通信。

- 从 HTTP/1.1 开始默认是长连接的，如果要断开连接，需要由客户端或者服务器端提出断开，使用 `Connection : close`；
- 在 HTTP/1.1 之前默认是短连接的，如果需要使用长连接，则使用 `Connection : Keep-Alive`。

### 2. 流水线

默认情况下，HTTP 请求是按顺序发出的，下一个请求只有在当前请求收到响应之后才会被发出。由于会受到网络延迟和带宽的限制，在下一个请求被发送到服务器之前，可能需要等待很长时间。

流水线是在同一条长连接上发出连续的请求，而不用等待响应返回，这样可以避免连接延迟。



### HTTPS