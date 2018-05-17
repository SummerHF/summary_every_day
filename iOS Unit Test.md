#iOS Unit Test

##单元测试的好处

* 单元测试可以为公司节省成本
* 单元测试可以让用户开心, 因为潜在的问题可能被测试排除了
* 单元测试让我们重构代码的时候更有自信
* 单元测试可以帮助我们更好的理解自己的代码
* 单元测试可以帮助我们在已有的代码基础上更好的创作

###如何进行单元测试
如何使用**swift4**和**XCTest**编写测试代码?

#### 1.A 创建一个全新的项目的时候添加单元测试

![](https://ws3.sinaimg.cn/large/006tNc79gy1frebikpw80j30k90epgln.jpg)

#### 1.B 在已有项目的基础上添加单元测试
* 先选择 **Test Navigator**, 快捷键(⌘6)
* 点击左下角的➕号按钮, 选择新建 **New Unit Test Target...**
* 然后选择完成
![](https://ws1.sinaimg.cn/large/006tNc79gy1freb01vcu0j308c0973yp.jpg)

#### 2 运行单元测试
* 菜单栏**Product**->**Test**
* 快捷键**⌘U**

测试结束并通过后, 可以在测试的导航版上看到绿色的标记, 像这样

![](https://ws1.sinaimg.cn/large/006tNc79gy1freb7pag5mj307j047t8k.jpg)

#### 3 编写第一个自己的单元测试实例
测试什么？ 当然是需求. 我们应该测试自己的代码是否满足需求, 我们不需要去测试所有的需求, 而只是测试其中的一小部分即可. 当测试失败的时候, 我们就知道自己的代码不满足需求.

现在我们来测试这样的一个需求:

```
登录页显示"Podcaster"标题
```
打开**TestTests**文件夹, 找到**TestTests.swift**, 你将在这里编写你的单元测试代码.
先来看下面的代码 它是什么意思?

```
import XCTest
@testable import Test

class TestTests: XCTestCase {
	````
	省略
	````
}
```

我们导入了**XCTest Framework**并且让我们的类继承**XCTestCase**, 然后我们import 自己的app的模块 **Test**, **@testable**关键字让我们可以访问自己app内的类, 属性和方法.

现在我们来编写测试代码
```
import XCTest
@testable import Test

class TestTests: XCTestCase {

func test_title_is_Podcaster() {
        let storyboard = UIStoryboard(name: "Main", bundle: nil)
        let login = storyboard.instantiateInitialViewController() as! ViewController
        /// 确保view被加载, 正常情况下它会自动加载, 但是目前处于测试状态, 所以需要确保它被正确的加载了，要不然会引发问题
        let _ = login.view
        XCTAssertEqual("Podcaster", login.titleLable.text)
    }
}
```

为了**Xcode**可以发现并且执行测试代码, 你的方法名称的前缀应该是**test**. 剩下的命名取决于你.

我们的ViewController代码如下, 它只有一个懒加载属性**titleLable**. 推挤使用上述代码中的下划线方式在测试中进行命名, 因为它便与阅读。

```
import UIKit

class ViewController: UIViewController {

    lazy var titleLable: UILabel = {
        let lable = UILabel()
        lable.text = "Podcaster"
        return lable
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

在上述的代码中, 我们通过故事板获得**ViewController**的实例,就像我们正常编写业务逻辑代码一样. 在生产环境下不建议强制拆包, 但是这里是测试环境,影响不大.

最后我们判断**ViewController**的**titleLable属性**的**text**是不是**Podcaster**

运行你的测试代码**⌘U**, 如果它们不相等, 你会看到下面的错误提示:

![](https://ws1.sinaimg.cn/large/006tKfTcgy1frecq5i933j30pd071t90.jpg)

-------------

###总结

单元测试看似简单没有太大的意义, 但是它可以帮助我们发现那些容易被忽视的问题. 将潜在的bug尽早的扼杀在摇篮里... 前期花点时间来照顾好自己的代码， 好过问题出现的时候在费时费力的去定位问题.

#####参考
[Roadfire software](https://roadfiresoftware.com/)

