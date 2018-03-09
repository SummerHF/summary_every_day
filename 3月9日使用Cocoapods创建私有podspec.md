#使用Cocoapods创建私有podspec

[私有podspec] (https://www.jianshu.com/p/f8bcee994413)
[使用Cocoapods创建私有podspec] (http://blog.wtlucky.com/blog/2015/02/26/create-private-podspec/)

* 创建并设置一个私有的Spec Repo
* 创建Pod所需要的项目工程文件,并配置对应的podspec
* 本地测试配置好的podspec文件是否可用
* 向私有的Spec Repo中提交podspec
* 在个人项目中的Podfile中增加刚刚制作好的Pod并使用
* 更新维护podspec

##创建私有Spec Repo

```
pod repo add 项目名称 项目路径
pod repo add SummerPodTest https://gitee.com/SummerHF/SummerPodTest.git
```
成功创建后在 ～/.cocoapods/repos 目录下就可以看到SummerPodTest这个项目

##创建Pod项目工程文件
使用`Cocoapods`提供的一个工具将第二步与第三步结合起来做.
[Using Pod Lib Create](http://guides.cocoapods.org/making/using-pod-lib-create)

* `cd`到工程目录下 命令行输入

```
pod lib create 项目名称
```
##测试无误后需要将项目添加并推送到远程仓库,并编辑`podspec`文件
通过`Cocoapods`创建出来的目录本身就在本地的`git`管理下,我们需要做的就是给它添加远程仓库.
`cd`到项目目录
```
git add .
git commit -s -m "init project"
git remote add origin [项目地址] /// 添加远程仓库
git push origin master /// 提交到远端仓库
因为podspec文件中获取Git版本控制的项目还需要tag号,所以我们要打上一个tag
git tag -m "first releast" 0.1.0
git push --tags 
```

##编辑podspec文件 
[官方文档](http://guides.cocoapods.org/syntax/podspec.html)
![TOC](https://ws3.sinaimg.cn/large/006tNc79gy1fp6cbob0enj30gu0brq3a.jpg)
![TOC](https://ws1.sinaimg.cn/large/006tNc79gy1fp6cbpyrl1j30gj0i6wfq.jpg)

```
Pod::Spec.new do |s|
s.name = 'podTestLibrary'  #名称
s.version = '0.1.0'  #版本号
s.summary = 'A short description of podTestLibrary.'  #简短介绍，下面是详细介绍
s.description = <<-DESC
TODO: Add long description of the pod here.
DESC
s.homepage = 'https://github.com/王佳佳/podTestLibrary'   #主页
s.screenshots = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'  #截图
s.license = { :type => 'MIT', :file => 'LICENSE' }  #开源协议
s.author = { '王佳佳' => 'wangjiajia@hz-health.cn' }  #作者信息
s.source = { :git => 'http://gitlab.ihaozhuo.com/iOS/YJKTest.git', :tag => "0.1.0" } #项目地址，这里不支持ssh的地址，验证不通过，只支持HTTP和HTTPS，最好使用HTTPS
s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'  #多媒体介绍地址
s.ios.deployment_target = '8.0'  #支持的平台及版本
s.source_files = 'podTestLibrary/Classes/**/*'   #代码源文件地址，**/*表示Classes目录及其子目录下所有文件，如果有多个目录下则用逗号分开，如果需要在项目中分组显示，这里也要做相应的设置
# s.resource_bundles = {
'podTestLibrary' => ['podTestLibrary/Assets/.png']
} #资源文件地址
# s.public_header_files = 'Pod/Classes/**/.h'  #公开头文件地址
# s.frameworks = 'UIKit', 'MapKit'  #所需的framework，多个用逗号隔开
# s.dependency 'AFNetworking', '~> 2.3'  #依赖关系，该项目所依赖的其他库，如果有多个需要填写多个s.dependency
end
```
##验证文件是否可用
* `pod lib lint`
存在警告可以通过下面命令忽略
* `pod lib lint --allow-warnings`

##向Spec Repo提交podspec 
向`Spec Repo`提交`podspec`需要确定的是`podspec`必须通过验证无误(若存在警告错误同样可用 `--allow-warnings`来忽略)。 向我们的私有`Spec Repo`提交`podspec`只需要一个命令

```
pod repo push YJKSpecs podTestLibrary.podspec #前面是本地Repo名字 后面是podspec名字
```


