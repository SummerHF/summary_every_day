
#箭指Offer

##面试题2: 实现Singleton
设计模式相关, 要求只能生成一个实例.

**解法一: GCD**
```
/ GCD方式
+(instancetype)shared {
    static dispatch_once_t onceToken;
    static Person *person = nil;
    dispatch_once(&onceToken, ^{
        person = [[Person alloc] init];
    });
    return person;
}

```
**解放二: 同步锁(synchronized)**

```
+(instancetype)shared {
    static Person *person = nil;
    /// 加锁操作耗时操作, 所以不需要一进来就加锁, 可以先判断是否有该对象
//    @synchronized(self) {
//        if (person == nil) {
//            person = [[Person alloc] init];
//        }
//    }
//    return self;
    
    /// 优化写法
    /// 加同步锁前后两次判断实例是否存在
    if (person == nil) {
        @synchronized(self){
            if(person == nil) {
                person = [[Person alloc] init];
            }
        }
    }
    return  person;
}
```

**解法三: swift的静态构造函数**

```
class Singleton {
    /// 测试单例
    /// 调用静态构造函数时初始化静态变量
    /// 实例instance 并不是第一次调用属性Singleton.shared的时候被创建, 而是第一次用到Singleton的时候被创建, 例如向该对象添加一个静态方法, 调用一个静态方法是不需要创建一个实例的,但如果按照下述方式实现单例,仍然会过早的创建单例.
    
    static let shared = Singleton()
    static func test() {
        print("Test")
    }
}

```

**解法四: 实现安需创建实例**

```
class Test {
    static let shared = Nested.shared
    private class Nested {
        static let shared = Test()
    }
    static func test() {
        print("Test")
    }
}
/// 当我们手动调用Test.test()的时候, 不会自动创建实例, 控制台输出:
(lldb) po shared
 <uninitialized>
(lldb) 

在内部定义一个私有类nested, 只要我们不通过属性shared的创建实例, 就不会触发swift运行时调用Nested,也就不会像解法三那样因为用到了Singleton的静态方法而自动创建实例了,做到了真正的按需创建,从而避免降低内存的实用效率. 通俗的一点说就是你都还没有用到实例, 因为调用了一个该类的静态方法,就自动的创建了该类的实例,造成了内存的浪费。
```

##面试题3: 二维数组中的查找
在一个二维数组中， 每一行都按照从左到右递增的顺序排序, 每一列都按照从上到下递增的顺序排序.请完成一个函数，输入这样的一个二维数组, 判断数组中是否含有该整数.

```
#define FAILURE 0 
#define SUCCESS 1 
#define Error 2

int Find(int *matrix, int rows, int columns, int value) {
    int find = FAILURE;
    if (matrix != NULL && rows > 0 && columns > 0) {
        int row = 0;
        int column = columns -1;
        /// 限定条件
        while (row < rows && column >= 0) {
            int compare_value = matrix[row * columns + column];
            if (compare_value == value) {
                find = SUCCESS;
                break;
            } else if (compare_value > value) {
                column--;
            } else {
                row++;
            }
        }
    }
    return find;
} 
```


