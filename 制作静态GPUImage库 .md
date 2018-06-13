#制作静态GPUImage库 

##Error1: xcodebuild: error: SDK "iphoneos9.0" cannot be located.

```
xcodebuild -showsdks后，出现自己的sdk的版本，然后打开build.sh，将IOSSDK_VER改成相应的版本就行了。
```
##Error2: run build.sh

```
cd 到当前目录的上一级目录然后输入
./build.sh
运行脚本
```


 if HMUserAccount.shared.getUType() == .visitor {
            let login = LoginViewController(needPopToHome: true)
            self.navigationController?.pushViewController(login, animated: true)
            return
        }


