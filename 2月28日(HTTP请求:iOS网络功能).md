#HTTP请求:iOS网络功能
* 构建请求
* 生成与解析负载
* 错误处理

##构建请求
* 理解`HTTP`请求的结构
* 从iOS应用中发出HTTP请求
* 使用HTTP请求的高级操作

### HTTP介绍
`HTML`:定义了向文本添加样式的一种方式
`HTTP`:定义了在服务端与客户端之间传输数据的一种方式
`URL`:定义了在网络机器中定位唯一资源的一种方式

### 理解HTTP请求与响应

![HTTP请求步骤序列](https://ws4.sinaimg.cn/large/006tNc79gy1fow6b8ck1bj30hn0gbjsq.jpg)

*`HTTP`请求与`HTTPS`之间的最重要差别在于会话的连接建立阶段.在`TCO`连接建立好,`HTTP`请求发送前,客户端与服务器之间必须建立`SSL`会话.`SSL`会话建立包含多个阶段:客户端与服务器协商使用何种密码,交换公钥,验证协商以及验证身份(可选).当`SSL`会话建立完毕后,在`TCP`连接之上传输的数据都是加密的.

### URL结构
![URL结构](https://ws2.sinaimg.cn/large/006tNc79gy1fow6i19hi9j30h30mtjw1.jpg)
### 请求内容
![实例](https://ws3.sinaimg.cn/large/006tNc79gy1fow6uby7gej30fs04kmxk.jpg)

HTTP请求包含3部分:请求行,请求头与请求体.
* 请求行是发送给服务器的第一行数据.请求行包含3方面主要信息:HTTP请求方法,请求URL与HTTP版本.
* 请求方法标识了客户端请求的动作.
![常见的请求方法](https://ws1.sinaimg.cn/large/006tNc79gy1fow6tf3eqwj30gw0h5gnd.jpg)

* 请求头

![请求头](https://ws4.sinaimg.cn/large/006tNc79gy1fow71tky7oj30gz067jsd.jpg)

* 请求体

![请求体](https://ws4.sinaimg.cn/large/006tNc79gy1fow73dtrwtj30gq08u755.jpg)


### 响应内容
状态行 响应头 响应体
![](https://ws2.sinaimg.cn/large/006tNc79gy1fow87sml14j30h20a7q3z.jpg)

* 状态行包含3个域,域之间通过空格分隔.第一个域是响应的HTTP版本,接下来的两个域提供了表示请求结果的状态值.
* 响应头:响应头之间通过回车/换行符进行分割.每个头都包含了关于响应的元数据,包括数据的上一次修改时间,客户端可以缓存数据多长时间,数据的编码方式以及在随后的请求中提交的状态信息.
* 响应体:是通过空行与响应头凤隔开来的.响应体可以包含任意数量的二进制字符.

## 高层iOS HTTP API

* 同步: 启动线程的代码会阻塞,直到整个响应加载完毕并且返回到调用方法为止.该技术最容易实现，不过局限性也最大.
* 队列式异步:起始代码创建一个请求,并将其放到一个队列中以在后台线程执行.
* 异步:起始代码开启一个请求,该请求运行在起始线程中,不过在请求处理时会调用委托方法.该技术实现最为复杂,不过灵活性也是最大的.

每种请求都有自己的方式和最佳实践,但所有3个请求都由相同的4个对象构成.

### 所有的请求类型共用的对象

这三类请求会共用4类对象:`NSURL`,`NSURLRequest`,`NSURLConnection(NSURLSession)`和`NSURLResponse`.

#### NSURL
可以通过`NSURL`对象轻松管理`URL`值并且访问`URL`指向的内容.可以引用文件或者是网络资源,而代码的行为是一样的.主要的差别在于如果引用的是网络资源,那么就会在后台线程中执行代码,这样在数据加载时用户界面就不会暂停下来.

#### NSURLRequest && NSMutableURLRequest
有两种方式可以向`NSURLRequest`提供`HTTP`体.
* 一种方式是通过`NSMutableURLRequest`的`setHTTPBody`的方式(在内存中)
* 另一种是通过`NSInputStream`输入流的方式提供请求体`HTTPBodyStream`,而无需将整个体加载到内存中.
* 如果发送诸如照片或视频等大量内容,那么使用输入流是最佳选择.


#### NSURLConnection(iOS9 被废弃 使用NSURLSession)
![](https://ws4.sinaimg.cn/large/006tNc79gy1fowjrrwrodj30ko083n1g.jpg)

#### NSURLResponse
`NSURLResponse`对象会在`URL`加载请求完毕后返回.
![](https://ws1.sinaimg.cn/large/006tNc79gy1fowjvt4gy5j30kb0em42w.jpg)

`URL`加载系统提供了一个名为`NSHTTPURLResponse`的`NSURLResponse`子类,它包含特定于`HTTP`请求的属性.该类对于确定`HTTP`请求的结果是必须的.
* 响应头(allHeaderFields):该属性返回响应头值的`NSDictionary`对象.
* HTTP状态码(statusCode).

### 同步请求
![](https://ws1.sinaimg.cn/large/006tNc79gy1fowk6xf95aj30kh036gne.jpg)

同步请求的最佳实践
![](https://ws4.sinaimg.cn/large/006tNc79gy1fowkwapk7cj30ki0kbdml.jpg)

### 队列式异步请求

队列式异步请求类似于同步请求.程序提供`NSURLRequest`对象,`URL`加载系统尝试加载请求而不会与调用代码之间存在任何其他的交互.这两种方式之间的主要区别在于`URL`加载系统执行队列式异步请求位于队列中,可能位于后台线程上.

`iOS`提供了一种叫操作队列的设施,名为`NSOperationQueue`.这些队列可以让程序描述待执行的操作,然后以先进先出的顺序提交操作队列执行.队列框架提供了优先级顺序以及根据操作依赖的顺序,不过`URL`加载系统并没有使用这些设施.

#### 队列式异步请求的最佳实践

![](https://ws2.sinaimg.cn/large/006tNc79gy1fox2phjux6j30g809ftac.jpg)

### 异步请求
异步请求使用与同步和队列式异步请求相同的对象,只不过又增加了另一个对象,即`NSURLConnectionDelegate`
图3-4展示了与`HTTP`请求过程相关的委托调用序列.协议处理器在`HTTP`协议过程中处理时,会在连接的重要阶段调用委托方法.

![](https://ws2.sinaimg.cn/large/006tNc79gy1fox2vl2c9tj30ea0ff74x.jpg)

### 异步请求与运行循环
![](https://ws1.sinaimg.cn/large/006tNc79gy1fox3qju3glj30gw08qjs7.jpg)









[TOC]

