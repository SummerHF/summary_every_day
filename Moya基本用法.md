#Moya基本用法
[Moya基本用法](https://github.com/Moya/Moya/tree/master/docs_CN/Examples)

##Swift Generics Where
* 与协议结合
[文章](https://www.jianshu.com/p/b327df80b75f)

```
protocol TestProtocol {}
// 只给遵循某一个协议的类添加拓展
extension TestProtocol where Self: UIViewController {
    func testLog() {
         print("hello test")
    }
}
class ViewController: UIViewController, TestProtocol {
    testLog()
}

```

* 与范型结合

范型where从句要写在类型或函数体的左大括号前边
协议属性相关的定义

```
protocol Vehicle {
    var numberOfWheels: Int { get }     //readonly
    var color: UIColor { get set }      //read-write
}

/// 可选
@objc protocol driveableProtocol {
    optional var unimportantNumber: Int { get set }
}


```

* for in 

```
let array = [1, 2, 3]
for i in array where i > 2 {
    print(i)
}
```

* do catch 

```
enum ExceptionError: Error {
    case httpCode(Int)
}

func throwError() throws {
        throw ExceptionError.httpCode(10)
}

do {
       try throwError()
   } catch ExceptionError.httpCode(let httpCode) where httpCode > 500 {
       print("error")
   } catch {
       print("close")
   }
```

* switch 

```
	let tuple = (1, 2)
	switch tuple {
	case let (x, _) where x < 60:
	  print("不及格")
	default:
	  print("及格")
}
```

