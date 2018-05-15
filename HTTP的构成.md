#HTTP的构成

**HTTP**全称为**HyperText Transfor protocol(超文本传输协议)**, 从名字不难看出这是一种基于文本的网络协议, 对于初学者来说比较友好, 容易上手. 各平台上的一些第三方库都对**HTTP**做了进一步的封装, 让**HTTP**变的更加亲民.但是往往拿来就用的技术, 很容易忽视其背后隐藏的细节. 今天一起来扒一扒**HTTP**到底是如何构成的.

##请求与响应
要想高效使用**HTTP**进行客户端与服务器之间的通信, 应该理解该协议的原理.对于计算机通信来说, **HTTP**请求遵循着客户端-服务器范式.下图展示了一个简单的**HTTP**请求的步骤序列.
![](https://ws3.sinaimg.cn/large/006tKfTcgy1frc2ly0zvbj309r0ao3ym.jpg)

* 客户端建立一个到服务器的**TCP**连接.
* 然后发送**HTTP**请求.
* 服务器随后通过在同一个**TCP**连接上发送一个**HTTP**响应来响应该请求.
* 接下来, 客户端可以重用这个**TCP**连接,发送另一个请求或是将连接关闭.

**HTTP**与**HTTPS**之间的最重要差别在于会话的连接建立阶段.在**TCP**连接建立好, **HTTP**请求发送前, 客户端与服务器之间必须建立**SSL**会话. **SSL**会话建立包含多个阶段: 

* 客户端与服务器协商使用何种密码
* 交换公钥
* 验证协商以及验证身份(可选)
* 当**SSL**会话建立完毕后, 在**TCP**连接上传输的所有数据都将是加密的.

##初窥全貌
**HTTP**第一眼看上去非常简单, 先来看看**Request**部分:
![](https://ws3.sinaimg.cn/large/006tKfTcgy1frc2hfhuf5j309d09p3yn.jpg)

上图主要分为三部分: request line, header和body, 中间的**CRLF**为换行符. 如果能将我们平常发送的**HTTP**请求对应到上述三个部分, 就能形成初步的印象了.

以一个实际的**HTTP request**例子, 假设我们的请求**URL**为:

```c
http://www.baidu.com/res/static/thirdparty/connect.jpg?t=1480992153433
```
后续的分析都是以此请求为基础.

###request line

**request line**结构为

```c
Request-Line   = Method SP Request-URI SP HTTP-Version CRLF
```
* **Method**也就是我们平常谈论最多的**Post**和**GET**所处的部分.(除了上述两种method, 还有其他类型的method, 例如delete...)
* **SP**是个分隔符, 我用**Wireshark**抓包看了下, 就一个字节大小, 值为0x20, 对应**ASCLL**码中的空格.
* **Request-URI**我们就更熟悉了, 上述请求对应为: res/static/thirdparty/connect.jpg?t=1480992153433. 这里值得注意的一点是:实际传输的时候**Request-URI**有两种可能的形式, 一种是完整的**absoulteURI**, 包含**Scheme**和**Host**.另一种是**abs_path**,并没有包含**Scheme(http)和Host(baidu.com)部分**, host部分被移交到了**Header**当中.所以我们平时抓包, 有时看到的是完整的URI有时则只有路径信息.
* **HTTP-Version**协议的版本, 文本展示的形式为: **HTTP/1.1**
* **CRLF**由两个字节组成. CR值为16进制的0x0D, 对应ASCLL码的回车键, LF值为0x0A,对应ASCLL种的换行键.CRLF合起来就是我们平常所说的**\r\n**.
*
所以上述请求request-line的文本展示为: 

```c
Get 空格 res/static/thirdparty/connect.jpg?t=1480992153433 空格 HTTP/1.1\r\n
```

###Request header

[URL](http://mrpeak.cn/blog/http-constitution/)


