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
**header**其本质上是一些文本键值对, 一个典型的例子如下图所示:
![](https://ws2.sinaimg.cn/large/006tKfTcgy1frcbvapn5rj30em04zt8s.jpg)

每个键值对的形式为: **Key: 空格 Value CRLF**
上面讲述**Request-URI**的时候, 缺失的**Host**就以键值对的形式存在于**header**中,比如:**Host:pan.baidu.com**.

将若干个上述格式的键值对组合起来, 就成了我们HTTP请求完整的**header**.最后一个键值对之后再跟一个**CRLF**, 就表示我们的header结束了.

HTTP本身定义了一些**header key**, 另外也允许开发者添加自己的key,自定义的key一般以X开头,比如定义**X-APP-VERSION**来记录客户端的版本号.

###Request Body
body里面包含请求的实际数据.
对于**Method=Get**的请求来说, body是空的,或者说不存在body体,header最后的两个CRLF就标识着请求的结尾.我们一般调用请求的业务参数就是通过**Request Line**当中的**Request-URI**来传递的,比如上述请求中的**?t=1480992153433**, 也就是URL的**query string**部分. 这部分同样是以键值对的形式存在, 不过是位于**Request Line**当中.
对于**Method=Post**的请求来说, body体一般不为空,我们实际的业务数据都存放于body当中,数据在body中是以何种形式存在的,其实大有门道,后面再细说.至于**Request-URI**的**query string**部分,我们依然可以选择放置一部分数据在其中,但更普遍的做法是使用**body**体.

##HTTP Response
response的结构和request结构大致相同,可以用下图表示:
![](https://ws4.sinaimg.cn/large/006tNc79gy1frcd8o8gzqj308308y74d.jpg)

不过是将**Request Line**换成了**Status Line**

**Status Line**的结构如下:

```c
Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF
```
这里关键在于**Status-Code**的记忆, 记住常见的**Status-Code**值,对于我们平时分析网络错误十分有帮助,不需要记住每个值的含义, 只需要理解每个类别的含义即可...

* 1xx: Informational - Request received, continuing process.(收到请求,处理中...)
* 2xx: Success - The action was successfully received, understood, and accepted.(请求成功接收并被处理)
* 3xx: Redirection - Further action must be taken in order to complete the request.(重定向-需要更进一步的操作才能完成请求)
* 4xx: Client Error - The request contains bad syntax or cannot be fulfilled.(客户端错误-请求包含语法错误或者请求不能实现)
* 5xx: Server Error - The server failed to fulfill an apparently valid request(服务器错误-服务器不能正确的处理合法的请求)

###可以用来携带数据的部分
分析至此, 我们可以总结一个HTTP请求,哪些地方是用来携带数据的.

**Request Line**当中的**Request-URI**是一个选择,也是标准的GET请求用来传递数据的位置,一般以**query string**的格式存在于**URI**当中.一些浏览器或者**Framework**对于**query string**的长度会有一定的限制, 所以此处不适宜于传递较大的数据.

**header**也是一个选择,我们可以选择协议中的一些标准**header key**,比如Host, User-Agent等,将我们的业务数据存放其Value中.或者我们通过自定义key, 比如上面提到的**X-APP-VERSION**,使用X-开头是业界默认的习惯,虽然[RFC 6648](https://tools.ietf.org/html/rfc6648)当中建议大家不要再使用X-作为prefix,但这一习惯至今仍在延续...

**Body**体是我们的第三个选择,**Post**请求可以根据**Header**中的**Content-type**值, 以不同的形式将数据保存在body体中.

###一些隐藏的细节
可以看出http是一种基于文本解析的协议, 上面提到的空格(0x20), 回车换行(0x0D,0x0A)都是HTTP用来做文本解析的辅助符号.

**网络分层: 应用层-->表示层-->会话层-->传输层(TCP, UDP...)-->网络层-->数据链路层-->物理层**

解析HTTP的text流程, 其实也比较好理解.一个简化的流程大概是这样: 当我们从TCP层拿到应用层的buffer之后,以CLRF(\r\n)为分隔符, 将整个buffer分成若干行,第一行自然是**Request Line**,之后每一行代表一个**header**,如果遇到两个CLRF,则表示header结束,如果是**Method=post**,读取Header中的**Content-Length**值，最后根据这个值读取固定长度的body体.这样就完成了我们上述三个主要部分的读取.当然,上述是个简化的流程,实际解析场景会更多一些.

####我们再深入看下Request Line的解析
我们从TCP层拿到的实际上是一个字节流, 要将字节流解析成我们能够阅读交流的形式,我们需要将字节码进行编码和解码.**Request Line**使用的编码格式是**US-ASCLL**,也就是我们平时接触的**ASCLL码**中的一种.

**Request Line**通过**ASCLL码**做还原之后,我们得到的类似这样的结果:

```c
GET /res/static/thirdparty/connect.jpg?a=1&b=2 HTTP/1.1
```
URL的解析也自有一套规范,我们需要特别注意的是**query string**部分.我们平时编写业务代码的时候,可能会在**query string**当中塞入自己的数据,这些数据可能是任意形式的字节流,而**Request Line**和**URI**的解析都依赖于一些特殊字符来做分割,比如空格,/;?等等,所以为了能正确,安全的解析整个**Request Line**和**URI**,我们需要对**query string**中的字节流做进一步的编码约束,只允许其中出现安全的**ASCLL**码,这也是我们为甚么**UriEncode**的原因.

**UriEncode**的过程也比较简单, 它将字节流中的所有字节, 对照ASCLL码表分为, 安全的ASCLL码和不安全的ASCLL码. 安全的ASCLL码不用做任何处理, 不安全的ASCLL码(比如空格0X20)则做进一步的编码处理, 编码的思路也简单: 用安全的ASCLL码来代替不安全的ASCLL码.比如空格(0x20)被编码为%20, 由一个ASCLL码(空格)变成了三个ASCLL码(%, 2, 0).对于原本就不是ASCLL码的内容来说,比如中文,则先以UTF-8编码成字节流,再对照ASCLL码做编码. 比如中文[高], 其UTF-8的形式为**\xE9\xAB\x98**, 再进一步做ASCLL编码, 最后URIEncode的结果就是:**%E9%AB%98**.

由此可见, URIEncode是出于uri安全解析的需要, Encode的结果是由%和一部分安全的ASCll码所组成.URIEncode的缺点也比较明显, Encode非ASCLL码的时候, 比如中文, 一个字节会被Encode成3个字节,长度整整是用来的3倍,造成流量的浪费.

####Header的解析
对于Header的解析可以先按CRLF分隔成一个个的键值对, 键值对里面的值, 也就是我们所说的**field content**其实也有编码要求. [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.2.4)中有阐述:

```c
Historically, HTTP has allowed field content with text in the ISO-8859-1 charset [ISO-8859-1], supporting other charsets only through use of [RFC2047] encoding. In practice, most HTTP header field values use only a subset of the US-ASCII charset [USASCII]. Newly defined header fields SHOULD limit their field values to US-ASCII octets. A recipient SHOULD treat other octets in field content (obs-text) as opaque data.
```
简单来说, 我们在实际使用当中使用ASCll码来限制field content.我们常用几个field, 诸如Host, User-Agent等, 使用ASCll码字符也已绰绰有余. 一般不会对值做进一步encode处理.

####Body的解析
body的解析是我们平时打交道最多的部分, 不是说我们需要知道如何去解析body, 而是要了解body里的数据格式.

body的解析本身比较简单, 从header中知道**Content-length**之后, 读取固定长度的字节流即完成了body的获取, 关键的环节是获取之后, 如何读取其中的数据并递交给应用层, 所以HTTP协议本身并没有对Body中的内容编码做约束, 而是把它交给协议的使用者去决定, 我们甚至可以在body里存放二进制流, 对应的Content-Type为**application/octet-stream**.

我们来看看平时发送HTTP请求时, 以**AFNetworking**为例, 使用最频繁的几种**Content-type**:

* multipart/form-data
* application/x-www-form-urlencoded
* application/json

当我们向Server发送数据的时候, 需要和Server约定好所使用的**Content-type**, 客户端在发送Request的时候也要注意API的差别, 以**AFNetworking**为例

* 发送json则使用:

```c
AFJSONRequestSerializer* jsonSerializer = [AFJSONRequestSerializer serializer];
request = [jsonSerializer requestWithMethod:@"POST" URLString:requestUrl parameters:requestParams error:nil];
```

* 发送multipart/form-data:

```c
request = [self.requestSerializer multipartFormRequestWithMethod:@"POST" URLString:requestUrl parameters:requestParams constructingBodyWithBlock:nil error:nil];
```

* 发送x-www-form-urlencoded:

```c
request = [self.requestSerializer requestWithMethod:@"POST" URLString:requestUrl parameters:requestParams error:nil];

```
json不用多说, 大家都非常熟悉的数据交换格式. **multipart/form-data** 和 **x-www-form-urlencoded** 比较容易引起混淆.

AFNetworking中有这样一段代码：

```c
//AFURLRequestSerialization
if (![mutableRequest valueForHTTPHeaderField:@"Content-Type"]) {
    [mutableRequest setValue:@"application/x-www-form-urlencoded" forHTTPHeaderField:@"Content-Type"];
}
```
可见当我们的Request没有设置Content-Type的时候, 默认使用的就是**application/x-www-form-urlencoded**. 这里的**URIEncode**和前面的**Request-URI**中的URIEncode是一回事, 只不过encode的是body体当中的内容.

那我们什么时候应该使用**application/x-www-form-urlencoded**,什么时候使用**multipart/form-data**？

先来看下使用**Content-type**为**multipart/form-data**时, 我们的request有什么变化, 下图是使用**mitmproxy**抓包一个文件上传Request的header示意图:

![](https://ws1.sinaimg.cn/large/006tNc79gy1frcwash2xwj30he05a3yp.jpg)

Content-type的完整值是：

```c
multipart/form-data; boundary=Boundary+2BBBEA582E48968C
```

multipart将body体分成多个块, 多个块之间依赖于boundary值做分隔, 所以生成的boundary要足够长,长到在字节流当中出现重复的概率几乎为0, 否则就会导致传输的错误， AFNetworking中生成boundary的方法如下:

```c
static NSString * AFCreateMultipartFormBoundary() {
    return [NSString stringWithFormat:@"Boundary+%08X%08X", arc4random(), arc4random()];
}
```

我们可以看下一个例子, 如果使用**multipart/form-data**, body中具体的数据格式:

```
Boundary+2BBBEA582E48968C
Content-Disposition: form-data; name="text1"
text
Boundary+2BBBEA582E48968C
Content-Disposition: form-data; name="text2"
another text
```
可以看到在body中多出**Boundary+2BBBEA582E48968C**和**Content-Disposition**， 这些会增加body的传输大小.

假设我们有一个大文件需要上传, 如果我们使用**application/x-www-form-urlencoded**作为**Content-type** ,由于字节流当中存在非常多的非ASCLL码, 文件的长度会变为原来的2-3倍,所以此时
**multipart/form-data**更合适.

假设我们只有少量的键值对需要上传, 如果使用**multipart/form-data**作为**Content-type**, 由于**Boundary和Content-Disposition**带来的额外流量,又显的得不偿失,所以此时使用**application/x-www-form-urlencoded**更为合适.

这也是为什么我们使用**multipart/form-data**作为文件类Request的Content-type, 而对于普通业务数据,则使用**application/x-www-form-urlencoded**或者**application/json**.

##总结
上述的分析, 更多的是站在客户端的角度去看的, 实际HTTP协议的构成细节非常之多, 需要旷日持久的深入学习和积累. **功夫越深, 坑越少**

