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

###多维数组

```
int vector[10], *vp = vector;
    /// 指向二维数组的指针 下标引用的优先级要高于间接访问 但是由于括号的存在 首先执行的还是间接访问
    /// matrix是一个指向整型数组的指针

    /// int (*p)[10]
    /// p 是一个指向拥有10个整型元素的数组的指针
    int matrix[3][19], (*mp)[19] = matrix;
    
```

###作为函数参数的多维数组

```

int matrix[3][19];

/// 函数原型
void test(int matrix[][19])
{

}

void testTwo(int (*p)[19]) {

}

void func2( int ** mat)
/// 指向整型的指针的指针与指向数组的指针并不是一回事.

/// 关键在于编译器必须知道第2个及以后各维的长度才能对各下标进行求值,因此在原型中必须声明这些维的长度. 第一维的长度并不需要, 因为在计算下标时用不到它.
```

###初始化
初始化多维数组时, 数组元素的存储顺序就变得非常重要
编写初始化列表有两种形式. 第一种是只给出一个长长的初始值列表

```
int a[3][2] = {100, 101, 102, 103, 104, 105};
or 
int a[3][2] = {
	{100, 101}, 
	{102, 103},
	{104, 104}
}
```
###数组长度自动计算

```
/// 在多维数组中, 只有第一维才能根据初始化列表缺省的提供. 剩余的几个维必须显示的写出, 这样编译器就能推断出每个字数组维数的长度.

int two_dim[][5] = {
	{00, 01, 02},
	{10, 11},
	{20, 21, 22, 23}
}
```

###指针数组

```
int *api[10]
```

##总结
在绝大多数表达式中, 数组名的值是指向数组第一个元素的指针. 这个规则只有两个例外,sizeof返回
整个数组所占用的字节而不是一个指针所占用的字节. 单目操作符合&返回一个指向数组的指针.而不是一个指向数组第一个元素的指针的指针.

静态变量包括数组在程序载入到内存时的到初始值, 自动变量没次当执行流进入到它们声明所在的代码块时都要 使用隐式的赋值语句重新进行初始化.

#字符串， 字符和字节
所有其他字符串都必须存储于字符数组或动态分配的内存中.
字符串就是一串零或者多个字符串, 并且以一个位模式为全0的`NUL(终止符)`字节结尾.
`NUL`字节是字符串的终止符, 但他本身并不是字符串的一部分, 所以字符串的长度并不包括`NUL`字节.


