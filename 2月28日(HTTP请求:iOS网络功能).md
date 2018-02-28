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

[TOC]

