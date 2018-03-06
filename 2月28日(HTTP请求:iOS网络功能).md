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



### 异步请求与运行循环

异步请求需要运行循环.当数据传递到服务器或是被客户端接收时,运行循环用于实现事件与委托对象之间的通信.异步请求在发出时,会在当前线程的运行循环上操作.这个细节很重要,因为在`GCD块`或者是通过`NSOperationQueue`创建的线程并没有运行循环.因此,如果在后台线程上发出了异步请求,那么还需要确定线程是有运行循环还是使用了别的运行循环.如果不想在主运行循环中执行异步请求,那么需要在另一个线程上创建运行循环,然后针对这个新创建的运行循环进行循环调度连接

#### 异步请求的最佳实践
![](https://ws2.sinaimg.cn/large/006tNc79ly1foxoqvy0z7j30ka072go8.jpg)

##高级HTTP操作
![](https://ws1.sinaimg.cn/large/006tNc79ly1foxowycah4j30le05zjug.jpg)

###使用请求方法
根据定义,`GET`请求不应该包含`HTTP`体,而只应该包含请求行与请求头.
使用`Post`请求向服务器发送`XML`或`JSON`数据.
使用`HEAD`请求会指示`HTTP`服务器只返回关于所请求资源的`HTTP`头信息.`HEAD`请求通常没有请求体,也没有响应体返回.它们常常用于验证缓存的数据与服务器上的数据,同时又不必获取缓存资源的整个内容.

### 操纵Cookie
![](https://ws2.sinaimg.cn/large/006tNc79gy1foxpf62fpzj30l2038dho.jpg)

从服务器发送的`Cookie`有几个属性用于确定`Cookie`值,何时返回到服务器以及客户端应该保留`Cookie`的时间.

这些属性有:
![](https://ws1.sinaimg.cn/large/006tNc79gy1foxpqszdntj30kl0ikwmg.jpg)

应用中经常要用到`Cookie`的3个地方:
* 检索`Cookie`
* 显示删除`Cookie`
* 以及手工将`Cookie`添加到请求中.
![](https://ws4.sinaimg.cn/large/006tNc79ly1foxpwbnk2dj30kx04r770.jpg)

`URL`加载系统提供了两个重要对象以管理`Cookie`:`NSHTTPCookie`与`NSHTTPCookieStorage`.
`NSHTTPCookie`通过所有必要以及可选属性来表示`Cookie`.`NSHTTPCookieStorage`则是单例对象,用于管理应用的`Cookie`.注意,与所有其他的应用数据一样,`NSHTTPCookieStorage Cookie`也是沙箱的,无法在应用间共享.
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fp2t3inidpj30h709aabm.jpg)

#### 从响应中获取Cookie 
从响应中获取`Cookie`,然后根据名字查找特定的`Cookie`是很常见的事情.

```
-(void)getCookie {
    NSURL * url = [NSURL URLWithString:@"http://www.baidu.com"];
    NSMutableURLRequest * request = [[NSMutableURLRequest alloc] initWithURL:url];
    NSHTTPURLResponse * response;
    NSError * error;
    NSData * data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:&error];
    NSDictionary * headers = [response allHeaderFields];
    NSArray * cookies = [NSHTTPCookie cookiesWithResponseHeaderFields:headers forURL:url];
    for (NSHTTPCookie * cookie in cookies) {
        NSLog(@"Cookie: %@", cookie);
        if ([cookie.name isEqualToString:@"JSEES"]) {
            NSLog(@"Found the session id");
        }
    }
}
```

####删除Cookie
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fp3favr2eqj30l603ktap.jpg)


```
-(void)deleteAllCookies {
    NSHTTPCookieStorage * jar = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    NSArray * storedCookies = jar.cookies;
    for (NSHTTPCookie * cookie in storedCookies) {
        [jar deleteCookie:cookie];
    }
    [[NSUserDefaults standardUserDefaults] synchronize];
}
```
代码可以组合使用`Cookie`属性,有选择的删除`Cookie`. 对于目标域来说,`Cookie`的名字是唯一的

```
-(void)deleteCookie:(NSString *)cookieName url:(NSURL *)url {
    NSHTTPCookieStorage * jar = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    NSArray * storedCookie = [jar cookiesForURL:url];
    for (NSHTTPCookie * cookie in storedCookie) {
        if ([cookieName isEqualToString:cookie.name]) {
            [jar deleteCookie:cookie];
        }
    }
}
```

确定针对某个域返回哪些`Cookie`会考虑该域的全局`cookies`以及特定主机的`Cookie`,下表展示了域与子域的映射规则.
![]()

###创建Cookie
我们可以创建`Cookie`并以编程的方式添加到请求或`Cookie`存储中.如果想要手工创建`Cookie`以遵循`Web`应用的独有协议,那么这就十分必要.有时还需要接收域的`Cookie`,然后创建相同的`Cookie`并发送给另一个域.

#### 创建Cookie并添加到请求中

```
-(void)createCookie {
    NSURL * url = [NSURL URLWithString:@"www.Foo.com"];
    NSDictionary * properties = [NSDictionary
                                 dictionaryWithObjectsAndKeys:
                                 @"Foo",NSHTTPCookieName,
                                 @"this is foo",NSHTTPCookieValue,
                                 @"/",NSHTTPCookiePath,
                                 url,NSHTTPCookieOriginURL
                                 ,nil];
    NSHTTPCookie * cookie = [NSHTTPCookie cookieWithProperties:properties];
    NSMutableURLRequest * request = [NSMutableURLRequest requestWithURL:url];
    NSArray * newCookies = [NSArray arrayWithObject:cookie];
    NSDictionary * newHeaders = [NSHTTPCookie requestHeaderFieldsWithCookies:newCookies];
    [request setAllHTTPHeaderFields:newHeaders];
}
```

这种方式设置的`Cookie`,会将`Cookie`添加到随后所有的请求中去 

```
-(void)createCookie {
    NSURL * url = [NSURL URLWithString:@"www.Foo.com"];
    NSDictionary * properties = [NSDictionary
                                 dictionaryWithObjectsAndKeys:
                                 @"Foo",NSHTTPCookieName,
                                 @"this is foo",NSHTTPCookieValue,
                                 @"/",NSHTTPCookiePath,
                                 url,NSHTTPCookieOriginURL
                                 ,nil];
    NSHTTPCookie * cookie = [NSHTTPCookie cookieWithProperties:properties];    
    [[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:cookie];
}
```

请记住,如果`Cookie`存储接收策略有限制,那么`Cookie`可能不会被存储进去.
存储完`Cookie`之后,新的请求在开始执行时会自动将其从`Cookie`存储中取出来/

###头信息操作进阶
除了操纵请求体与随请求头发送的`Cookie`外,我们常常还会向请求添加头,或是从请求中删除头以及查看响应中的头信息.

####添加请求头
当代码需要修改请求头时,需要创建`NSMutableURLRequest`对象而不是`NSURLRequest`对象.`NSMutableURLRequest`提供了两种修改请求头:一次一个头以及替换所有头.之前的`Cookie`示例就通过该方法向请求添加`Cookie`.如果代码需要根据动态的数据来添加头,那么该方法就很有用了.还可以将头逐个添加到请求中.

```
    NSMutableURLRequest * request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"www.baidu.com"]];
    /// HTTP协议规定头的名字要以冒号结束,不过如果省略的话,该方法会附加一个冒号
    [request addValue:@"en" forHTTPHeaderField:@"Content-Language"];
```

#### 删除请求头
有时需要从请求中删除头,比如,如果应用想要禁用对返回数据的压缩,那么可以覆写默认的`iOS`头(默认的头支持`gzip`或`DEFLATE压缩`) 
`DEFLATE`是同时使用了`LZ77算法`与`哈夫曼编码（Huffman Coding）`的一个无损数据压缩算法

```
    NSMutableURLRequest * request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"www.baidu.com"]];
    /// Accept-Encoding: gzip, deflate 这会告诉服务器,可以对返回的响应体进行压缩
    [request setValue:@"" forHTTPHeaderField:@"Accept-Encoding"];
```
`URL`加载系统`API`并未提供完全删除请求中标准头的方式,最好的方式就是使用空值覆写默认值.
#### 查看响应头
当`HTTP`请求完成且没有错误时,可能不包含头,也可能包含多个头.

```
    NSURLRequest * request = [NSURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalAndRemoteCacheData timeoutInterval:30.0];
    NSHTTPURLResponse * response;
    NSError * error = nil;
    NSData * data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:&error];
    if (error != nil) {
        NSLog(@"Error on load = %@",error.localizedDescription);
        return  nil;
    }
    NSDictionary * headers = [response allHeaderFields];
    NSString * contentType = [headers objectForKey:@"Content-Type"];
```
如果响应中包含了相同类型的多个头,那么只会返回一个值.这个返回值是由所有响应值拼接而成的,并使用逗号分隔.

###主要的请求头
* Accept头: 检查`Accept`头来确定对返回负载的数据编码.
* Authorization: 可以与认证证书一起来避免对来自服务器的响应进行认证检查. `Basic`认证的安全性不如`Base64`编码的密码,这意味着只能在`HTTPS`连接上使用这种方式.
* User-Agent: 创建它的目的是为`HTTP`服务器识别出浏览器类型.在很多企业网络中,`user-agent`值用于根据客户端类型将访问转向特定的服务器.












[TOC]

