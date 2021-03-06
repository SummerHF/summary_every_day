[TOC]
#Linux常见命令
##ls(list)
查看当前文件夹下的内容
##pwd(print work directory)
查看当前所在文件夹
##cd(change directory)
修改目录

```
cd 空格 [目录] enter
```

##touch
如果文件不存在 新建文件

```
touch 空格 文件名
```
##mkdir(make directory)
创建目录

```
mkdir 目录名
```
##rm(remove) 
删除指定的文件名
无法删除目录 需要额外的设置(rm -r 目录名)
```
rm 文件名
```
##clear
清屏

#Linux终端命令格式
command [-options] [param]
* command: 命令名
* [-options]: 选项
* params: 传递给命令的参数
* []: 代表可选

#查阅命令帮助信息
* man + [command] 例如 man ls 

#ls 命令
* -a:显示隐藏
* -h:显示文件大小
* -l:列表显示

字节  B(Byte) 一个字节 一般为8位二进制数
千    K      1KB = 1024B

##ls与通配符
* `*`代表任何字符
* `?`代表一个字符
* `[]`定义一个要匹配的字符组

##cd命令
* `~` : 回到家目录
* `cd`: 回到家目录
* `.` : 当前工作目录
* `..`: 表示上级目录
* `-` : 最近工作目录切换

##路径
* 绝对路径: 一般以斜线开始, 最前面的以跟目录/家目录开始的具体位置 
* 相对路径: 不以斜线开始

## 创建和删除操作
### touch
如果文件已经存在,那么可以修改文件的末次修改日期
### mkdir
创建一个新的目录
`-p`: 可以连续创建多个目录

在`linux`下 文件和目录不能同名

### rm
既可以删除一个文件 也可以删除一个目录 直接从磁盘删除

`-r`: 一次性删除多个目录
`-f`: 强制删除文件 没有提示

##拷贝和移动文件
###tree 
只显示目录

##mv命令
mv 命令可以用来移动文件或者 目录
`-i`: 是否覆盖

##cat命令 
查看文件内容 创建文件 文件合并 追加文件内容 
一次性显示所有内容 
`-b`: 对非空输出行编号
`-n`: 对输出的所有号编号

##more命令
查看更多内容 `whiteSpace`
可以分屏显示 

##grep命令
`-n`: 显示名称以及行号
`-v`: 与搜索内容相反
`-i`: 忽略大小写
`grep`允许对文本内容进行`模式查找`,又被称为`正则表达式`.

```
^a 行首,搜寻以a开头的行
a$ 行尾,搜寻以a结束的行
grep -ni ^t test
```
##echo命令
echo会在终端中显示参数指定的文字,通常会和重定向联合使用
可以使用`echo`命令创建文件，类似于`touch`,不同的是`echo`可以创建初始内容

##重定向>和>>
本应显示在终端的内容 输出/追加到指定文件中
其中
`>`: 表示输出,会覆盖文件原有的内容
`>>`: 表示追加,会将内容追加到已有文件的末位

```
ls -lh > a: 将输出内容输出到指定	a文件中
```
可以将终端命令的输出保存到指定的文件中

##管道
`Linux`允许将`一个命令的输出`可以`通过管道`做为另`一个命令的输入`
`|`:左侧塞东西,右侧取东西

常用的管道命令有:
`more`: 分屏显示内容，空格下一页 
`grep`: 在命令执行结果的基础上查询指定的内容

#远程管理常用命令
##关机/重启
* `shutdown` 选项 时间: 关机/重新启动
可以安全关闭或者重启系统
`shutdown`: 一分钟后关机
`-r`: 重新启动

```
shutdown -r now: 立刻重启
shutdown -c: 取消关机
```

##查看或配置网卡信息
* 网卡: 硬件设备 负责网络通讯的硬件设备
* ip地址: 设置在网卡上的地址信息

```
可以把电脑看作手机, 网卡相当于sim卡，ip地址相当于电话号码
每台电脑都有ip地址,是保证电脑之间正常通讯的重要设置
```

* ifconfig
可以查看或配置计算机当前的网卡配置信息

```
ifconfig | grep inet: 查看ip地址
```
提示: 一台计算级中可能会有一个`物理网卡`和`多个虚拟网卡`.
`127.0.0.1`:被称为 `本地回环`或`环回地址`,一般用来检测本机网卡是否正常

* ping 
检测到目标ip地址的连接是否正常
`control c`: 停止`ping`

```
Linux中想要终止终端命令: control + c 
```
##SSH(基础)
通过`ssh客户端`我们可以连接到运行了`ssh服务器`的远程机器上
`ssh`是目前较可靠,专为远程登录会话和其他网络服务提供安全性的协议

* 域名
	通过`ip`地址找到网络上的计算机, `ip`地址的别名,方便用户记忆
* 端口号
	通过`端口号`可以找到计算机上运行的应用程序
	
```
服务              端口号
SSH服务器         22
Web服务器         80
HTTPS            443
FTP              21
```


##SSH客户端的基础使用

```
ssh [-p port] user@remote
```
* `user`: 是在远程机器上的用户名 如果不指定那么就是当前用户名
* `port`: `SSH Server监听的端口`,如果不指定,就为默认值`22`
* `remote`: 是远程机器的地址
* 提示: 使用`exit`退出当前用户的登录


```
ssh root@47.97.222.102 -i /Users/hero/Desktop/阿里云密钥/summer.pem 

```

###Windows下使用putty xsheel

###scp
* `scp`就是一个`secure copy`,是一个在`linux`下用来进行`远程拷贝文件`的命令
* 它的地址格式与`ssh`基本相同 需要注意的是 指定端口的是用大写`P`, `-P`


```
# 把本地当前目录下的01.py文件复制到远程家目录
scp [-P port] [源文件] user@remote:[远程目录] .
scp 01.py root@47.97.222.102:/test/01.py
# 把远程文件复制到本地
scp [-P port] user@remote:[远程目录] [本地文件]. 
scp root@47.97.222.102:/test/01.py /Users/hero/Desktop/alibaba.py
```
`-r`: 复制文件夹

```
复制本地目录到远程
scp -r demo user@remote:[远程目录]  

```

```
复制本地文件到
```
scp -P 22 -r root@47.97.222.102:/test demo


###SSH高级
* 免密码登录
1.配置公钥
执行`ssh-keygen`即可生成`ssh`钥匙,一路回车即可
上传公钥到服务器

```
known_hosts: 已知主机
summer.pub: 公钥
```
2.上传公钥到服务器
执行`ssh-copy-id -p port user@remoter`,可以让远程服务器记住我们的公钥.

非对称加密算法
使用私钥加密数据并上传到服务器,服务器使用公钥解密数据，服务器处理完数据后,使用公钥加密数据并回传到客户端,客户端使用私钥解密

* 配置别名
在`~/.ssh/`下新建`config`

```
Host summer    /// 别名
     HostName 47.97.222.102    /// 服务器ip
     User root  /// 服务器用户
     Port 22    /// 端口
```

提示：有关`SSH`配置信息都保存在用户家目录下的`.ssh`目录下

##用户权限相关命令
* 用户和权限的概念
* 用户管理终端命令
* 组管理终端命令
* 修改权限终端命令

###用户和权限的概念
用户是`Linux`系统工作中重要的一环,用户管理包括`用户`与`组`管理
对文件/目录的权限包括:

```
读:
写:
执行:
```
为了方便用户管理,提出了组的`概念`在实际应用中,可以预先针对`组`设置权限,然后将不同的用户添加到对应的组中,从而不用依次为每一个用户设置权限.

###ls-l命令拓展
以列表的形式查看文件夹下的文件.


```
r: 可读
w: 可xie
x: 可执行

drwxr-xr-x  29 hero  staff   986 Nov  8 09:08 Moya
d|rwx|r-x|r-x  (29) hero  staff   986 Nov  8 09:08 Moya
可分三组 
d: 表示目录
第一组是当前用户的权限 这里是hero
第二组是当前组的权限 这里是staff,组名可以和用户名相同
第三组是其他用户的权限 
(29): 硬链接数: 就是有多少种方式可以访问到目录或者文件(包括绝对路径, 在当前目录下使用`cd .`到达等),通俗的说,一个目录的子目录数越多,那么其对应的硬链接数越多.
```
###chmod修改用户/组对文件/目录的权限
修改文件的权限,命令格式如下
```
chmod +/- rwx 文件名
```
修改目录的权限,命令格式如下
```
chmod +/- rwx 目录名
x: 可执行权限 如果禁用 那么像ls等命令会被禁止
r: 读目录内容
w: 添加文件到目录
```
###超级用户(root)
`Linux`系统中的`root`账号通常用于系统的`维护和管理`,对操作系统的所有资源`具有所有的访问权限`
在大多数版本的`Linux`中,都不推荐直接使用`root`账号登录系统
在`Linux`安装的过程中,系统会自动创建一个用户账号,而这个默认的用户账号就称为`标准账号`

####sudo 
当标准用户想要执行一些系统维护和管理的工作，例如添加一个用户时,可以使用`sudo`.

```
su是substitute user的缩写,表示使用另外一个用户的身份
sudo命令用来以其他身份来执行命令,预设的身份为root
```

##组管理终端命令(非重点)

```
创建组/删除组的终端命令 都需要使用`sudo`命令
Linux下
groupadd 组名				添加组
groupdel 组名	         删除组
cat /etc/gruop         确认组信
sudo chgrp -R 组名 文件/目录名 递归修改文件/目录的所属组
Mac下
sudo dscl . -create /Users/<user>
dscl . list /users  /// 查看用户
sudo dscl . -delete /Users/redis
sudo dscl . -create /Users/<user> UserShell /bin/bash
sudo dscl . -create /Users/<user> RealName "<realName>"
sudo dscl . -create /Users/<user> UniqueID "<userID>"
sudo dscl . -create /Users/<user> PrimaryGroupID <groupID>
sudo dscl . -create /Users/<user> NFSHomeDirectory /Users/<user>
sudo dscl . -passwd /Users/<user> <passwd>
sudo dscl . -append /Groups/admin GroupMembership <user>
```
群组默认对家目录没有写权限

创建用户/设置密码/删除用户
```
useradd -m -g         建立新用户           -m:自动建立用户家目录, -g: 指定用户所在的组,否则会建立一个同名的组 
passwd用户名         设置用户密码         如果是普通用户直接可以使用password可以修改自己的账户密码	
userdel -r <用户名>                     删除用户		
```
## 查看用户信息

```
id[用户名]: 查看用户UID(用户代号)和GID(组代号)信息
```

##72

