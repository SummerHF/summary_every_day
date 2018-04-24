#C和指针
##移位操作符
* 左移位操作
* 右移位操作(被操作数有正负之分,这就带来了符号位的填充到底是1还是0)
	* 逻辑移位 (用0填充)
	* 算术移位 (左边移入的位由原先该值的符号位决定,符号为1则移入的位均为1,符号为0则移入的位均为0，这样就能保持原数的正负形式不变).
	* 标准说明无符号值执行的所有移位都是逻辑移位,但对于有符号值,到底是采用逻辑移位还是算术移位取决于编译器.

	[为什么int整型(32位)的范围是-32768到32767？](https://my.oschina.net/lcniuren33/blog/63762)

##字和字节的概念
`边界对其`: 在要求边界对其的机器上, 整形值存储的起始位值只能是某些特定的字节,通常是2或4的倍数。

* 一个字节8位
* 许多机器以字为单位存储整数,每个字一般由二个或四个字节组成.
* 内存中的每个位置由一个独一无二的地址标识
* 内存中的每个位置都包含一个值.
* 不能简单的通过检查一个值的位来判断它的类型,为了判断值的类型(以及它的值),你必须观察程序中这个值的使用方式。


```
 	  /// 如果a存储在位置10211 那么这条语句就把值20存储于a
    /// 将整型常量转换为指向整形的指针
    /// 这个技巧唯一可能的用武之地是偶尔需要通过地址访问内存中某个特定的位置.访问硬件本身
    *(int *)10211 = 20;
```

##指向指针的指针

```
    int a = 12;
    int *b = &a;
    /// 变量b是一个指向整型的指针
    /// 变量c是一个`指向整型的指针`的指针
    int **c = &b;
    * 操作符具有从右向左的结合性
    * int *(*c): 从里向外逐个求值
```

#函数
##函数的参数
`C`函数的所有参数均以`传值调用`方式进行传递, 这意味着函数将获得参数值的一份拷贝.
如果被传递的参数是一个数组名, 并且在函数中使用下标引用该数组的参数,那么对数组的修改实际上修改的是调用程序中的数组元素. `传址调用`

##ADT和黑盒
`ADT`: 抽象数据类型, 因为它可以限制函数和数据定义的作用域(这个技巧也被称为黑盒设计).
`抽象数据类型的基本想法`: 模块具有功能说明盒和接口说明, 前者说明模块所执行的任务, 后者定义模块的使用.

###Const
A pointer can be declared as a const pointer to writable value, or a writable pointer to a const value, or const pointer to const value.

```
A const pointer cannot be reassigned to point to a different object from the one it is initially assigned, but it can be used to modify the value that it points to (called the pointee). Reference variables are thus an alternate syntax for const pointers. A pointer to a const object, on the other hand, can be reassigned to point to another memory location (which should be an object of the same type or of a convertible type), but it cannot be used to modify the memory that it is pointing to. A const pointer to a const object can also be declared and can neither be used to modify the pointee nor be reassigned to point to another object.


``` 

```
/// 深入理解const
/// 指针和引用
/// ptr 指向int类型的指针
/// const 左边修饰的是值 右边修饰的是指针 
 
void Foo ( int * ptr ,
           int const * ptrToConst,
           int * const constPrt,
           int const * const constPtrToConst
          )
{
    *ptr = 0;   // OK: modifies the 'pointee' data 修改指向的数据
    ptr = NULL; // OK: modifies the pointer 修改指针

//    *ptrToConst = 0; // Error！ Cannot modify the `pointee` data const
    ptrToConst = NULL; // OK: modifies the pointer

    *constPrt = 0; // OK: modifies the `pointee` data
//    constPrt = NULL; // Error! Cannot modify the pointer

//    *constPtrToConst = 0; // Error! Cannot modify the "pointee" data
//    constPtrToConst = NULL; // Error! Cannot modify the pointer
}

```
黑盒的概念使实现细节与外界隔绝, 这就消除了用户试图直接访问这些实现细节的诱惑. 这样，访问模块唯一可能的方法就是通过模块所定义的接口.

###递归
`C`通过运行时堆栈支持递归函数的实现. 递归函数就是直接或者间接调用自身的函数.
`迭代`
递归的开销必须要考虑其中

###可变参数列表
####stdarg宏
`va_list`和三个宏-------`va_start`, `va_arg`, `va_end`
可变参数必须从头到尾按照顺序逐个访问,如果你在访问了几个可变参数后想半途中止, 这是可以的.
但是, 如果你想一开始就访问参数列表中间的参数,那是不行的.

```
/**
 计算指定数值的平均值
 */
#include <stdarg.h>

float
average( int n_values, ... )
{
    va_list var_arg;
    int count;
    float sum = 0;
    /// 准备访问可变参数
    va_start( var_arg, n_values );
    /// 添加取自可变参数列表的值
    for ( count = 0; count < n_values; count += 1 ) {
        sum += va_arg(var_arg, int);
    }
    /// 完成处理可变参数
    va_end(var_arg);
    printf("%d\n", n_values);
    printf("%d\n", count);
    return sum / n_values;
}
```
##数组
声明一个数组时, 编译器将根据声明所指定的元素数量为数组保留内存空间, 然后再创建数组名, 它的值是一个常量, 指向这段空间的起始位置.
声明一个指针变量时, 编译器只为指针本身保留内存空间, 它并不为任何整型值分配内存空间.而且指针变量未被初始化为指向任何现有的内存空间. 如果它是一个自动变量, 它甚至根本不会被初始化.


```
传址调用是通过传递一个指向所需元素的指针, 然后在函数中对该指针执行间接访问操作实现对数据的访问.作为参数的数组名是个指针, 下标引用实际执行的就是间接访问.
```


```
void
copy ( char *buffer, char *string ) {
    /// 重复复制string 到 buffer中 直到遇到NUL字节
    while ((*buffer++ = *string++) != '\0');
}
```

###声明数组参数

```
int strlen ( char *string )
int strlen ( char string[] )
这两个声明确实相等, 但只是在当前这个`上下文环境中`. 如果他们出现在别处, 就可能完全不同.
for more precise, prefer to use pointer
如果函数需要知道数组的长度， 它必须作为一个显示的参数传递给函数.
```

###数组的初始化
####静态和自动初始化
静态内存(static),  自动变量位于运行时堆栈中.


```
    // 字符数组初始化
    char message[] = {'a', 'v', 'c'};
    char message[] = "Hello";
    
    char message1[] = "hello";
    char *message = "hello";
    /// 上述两个初始化看上去很像, 但他们具有不同的含义.
    /// 前者初始化一个字符数组的元素, 而后者则是一个真正的字符串常量 这个指针变量被初始化为指向这个字符串常量的存储位置.
    
```

