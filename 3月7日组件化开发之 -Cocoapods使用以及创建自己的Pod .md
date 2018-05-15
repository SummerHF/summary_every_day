# 组件化开发之 -Cocoapods使用以及创建自己的Pod 
组件件进行相互通信相互调用: Mediator模式进行业务间相互通信相互调用

* Cocoapods安装及基本用法
* Cocoapods注册Trunk
* 部署一个开源的pod
* 创建自己的私有的pod

## Cocoapods工具的基本用法
什么时候使用`pod install` 什么时候使用`pod update`?

* pod install : 当你在工程中需要安装新的`pods`时;添加或者移除一个`pods`时.
* pod update: 仅仅当你想要更新一个新版本的`pods`时.


## Cocoapods注册Trunk
在你想要创建一个自己私有库或者发布一个共有的`pods`之前,你需要有一个`Trunk`
```
pod trunk register [Your-Email] '[Your-Name]' --description='[Your-Desc]'
> [Your-Email]: 任意邮件，但是我比较推荐你使用github上的Email
> [Your-Name]: 推荐使用github上使用的Name
> [Your-Desc]: 一个简单的描述，往往这个时候我们使用的是自己电脑的一个描述
/// 
pod trunk register 391565521@qq.com 'charles' --description=`SummerHF`

```

##部署一个开源的pod
1. `github`上创建一个`public repository`
2.  `clone`到本地,并`cd`到当前目录下
3. 创建`.podspec`

```
$ pod spec create [NAME]
> [NAME]: podspec 名称，一般与你在git上创建的repository相同
// 如：
$ pod spec create PodTest

```
4.将代码或者资源添加到项目中去一般会先创建一个文件夹,用于放置`{.h/.m/swift Assets}`等
5.添加到`git`并提交

```
git add .
git commit -m "Initial AKExtension"
git push
// 接下来就是需要使用到的一个Tag，这个在你的.podspec中需要配置
git tag -m "Initial Tag" 0.0.1
//将tag添加至你的远程仓库
git push --tags

```
6.修改`.podspec`

```
Pod::Spec.new do |s|

  # ―――  Spec Metadata  ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.name         = "PodTest"
  s.version      = "0.0.1"
  s.summary      = "PodTest for"
  s.homepage     = "https://github.com/SummerHF/PodTest.git"

  # ―――  Spec License  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.license      = { :type => "Apache", :file => "LICENSE" }

  # ――― Author Metadata  ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.author             = { "SummerHF" => "391565521@qq.com" }

  # ――― Platform Specifics ――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.platform     = :ios
  s.ios.deployment_target = "9.0"

  # ――― Source Location ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.source       = { :git => "https://github.com/SummerHF/PodTest.git", :tag => "#{s.version}" }

  # ――― Source Code ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

  s.source_files  = "Source/*.swift"

  # ――― Project Linking ―――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #

   s.framework  = "Foundation", "UIKit"

  # ――― Project Settings ――――――――――――――――――――――――――――――――――――――――――――――――――――――――― #
   s.requires_arc = true
end

```
7. 通过手动方式检验一下你的`.podspec`是否正确

```
pod spec lint // 如果出现错误,需要修改到提示无误后方可执行下一步
```
8. 提交公有`library`

```
pod trunk push [name].podspec 
```

9.验证是否成功

```
// 先更新一下repo
pod repo update
// 查找一下你提交的pod
pod search 'PodTest'

```

[URL](https://www.jianshu.com/p/37fa98220678)
[为自己的库创建pod](https://www.jianshu.com/p/6e701a55247e)


