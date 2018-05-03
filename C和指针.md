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
int *api[10]  /// 指针数组
int (*api) [10] /// 指向数组的指针 
```

##总结
在绝大多数表达式中, 数组名的值是指向数组第一个元素的指针. 这个规则只有两个例外,sizeof返回
整个数组所占用的字节而不是一个指针所占用的字节. 单目操作符合&返回一个指向数组的指针.而不是一个指向数组第一个元素的指针的指针.

静态变量包括数组在程序载入到内存时的到初始值, 自动变量没次当执行流进入到它们声明所在的代码块时都要 使用隐式的赋值语句重新进行初始化.

#字符串， 字符和字节
所有其他字符串都必须存储于字符数组或动态分配的内存中.
字符串就是一串零或者多个字符串, 并且以一个位模式为全0的`NUL(终止符)`字节结尾.
`NUL`字节是字符串的终止符, 但他本身并不是字符串的一部分, 所以字符串的长度并不包括`NUL`字节.


### 长度受限的字符串函数

```
#include <string.h>

strcat: 拼接字符串(要确保目标字符串有足够的存储空间， 避免溢出)
strcmp: 字符串的比较(>0, <0)
strcpy: 字符串的复制(要确保目标字符串有足够的存储空间， 避免溢出)

```

strncat(<#char *__dst#>, <#const char *__src#>, <#size_t __len#>)
如果strlen(src)的值大于或者等于等于len, 那么只有len个字符被复制到dst中, 注意, 它的结果将不会以NUL字节结尾.

在使用不受限制的函数之前， 你首先必须确定字符串实际上是以`NUL`字节结尾的.

```
char buffer[BSIZE];
strncpy(buffer, name, BSIZE);
buffer[BSIZE - 1] = '\0';
如果name的长度可以容纳于buffer中, 最后那个赋值语句没有任何效果.
但是, 如果name太长, 这条赋值语句可以保证buffer中的字符是以`NUL`结尾的.以后对这个数组使用`strlen`或其他不受限制的字符串函数可以保证其正常工作.
```

###字符串查找基础

查找特定的字符最容易的方式是使用`strchr`和`strrchr`

```
	 char string[20] = "hello there honey.";
    char *ans;
    /// 指向字符串中该字符第一次出现的位置
    ans = strchr(string, 'h');
    printf("%c\n", *ans);
    /// 指向字符串中该字符最后一次出现的位置
    ans = strrchr(string, 'h');
    printf("%c\n", *ans);
    /// 如果未找到 则返回一个`NULL`指针
```
查找一个字串

为了在字符串中查找一个子串, 我们可以使用`strstr`函数, 原型如下:
如果第二个参数是一个空字符串, 函数就返回s1
```
strstr(<#const char *__s1#>, <#const char *__s2#>)


    char string[20] = "hello there honey.";
    char string1[20] = "";
    char *ans;
//    ans = strchr(string, 'h');
//    printf("%c\n", *ans);
//    ans = strrchr(string, 'h');
//    printf("%c\n", *ans);


    ans = strstr(string, string1);
    if (ans == NULL) {
        printf("not find\n");
    } else {
        printf("%c\n", *ans);
    }
```
[Understanding “register” keyword in C
](https://www.geeksforgeeks.org/understanding-register-keyword/)

##register 变量
寄存器访问要比内存访问快的多， 如果需要频繁的访问某一个变量, 可以将其存储在寄存器中.
```
1. 试图访问寄存器变量的地址程序会报错.

There is no good example of register usage when using modern compilers (read: last 15+ years) because it almost never does any good and can do some bad. When you use register, you are telling the compiler "I know how to optimize my code better than you do" which is almost never the case. One of three things can happen when you use register:(即使一个编译器在使用寄存器时产生更好的代码，也没有理由相信另一个编译器也会这样做。如果你有一些关键代码，编译器没有充分优化，那么你最好的选择可能是使用汇编器，但是当然，要先验证生成的代码是一个真正的问题。)

	•	The compiler ignores it, this is most likely. In this case the only harm is that you cannot take the address of the variable in the code.
	•	The compiler honors your request and as a result the code runs slower.
	•	The compiler honors your request and the code runs faster, this is the least likely scenario.

```
##结构体
###结构体的声明

```
struct tag {member-list} variable-list;
```


```
/// 结构体变量x
    struct {
        int a;
        char b;
        float c;
    } x;
    
    /// y是一个数组, 包含了20个结构.z是一个指针,它指向这个类型的结构
    struct {
        int a;
        char b;
        float c;
    } y[20], *z;
```
标签允许多个声明使用同一个成员列表, 并且创建同一种类型的结构.

```c
/// 这个声明把标签SIMPLE和这个成员列表联系在一起.该声明并没有提供变量列表, 所以它并未创建任何变量. 标签标识了一种模式, 用于声明未来的变量,但无论是标签还是模式本身都不是变量.

struct SIMPLE {
    int a;
    char b;
    float c;
};

///这些声明使用标签来创建变量, 现在x，y，z都是同一种类型的结构变量.
struct SIMPLE x;
struct SIMPLE y[20], *z;
```


```c
typedef struct {
    int a;
    char b;
    float c;
} SIMPLE;

/// 这个技巧和声明一个结构标签的效果几乎相同, 区别在于SIMPLE现在是一个类型名, 而不是一个标签名.
SIMPLE x;
SIMPLE y[20], *z;

```
### 结构成员的直接访问

```c
struct SIMPLE {
    int a;
    char b;
    float c;
};

struct COMPLEX {
    float f;
    int a[20];
    long *lp;
    struct SIMPLE s;
    struct SIMPLE sa[10];
    struct SIMPLE *sp;
};
```

结构变量的成员是通过`点(.)`操作符来访问的.左操作数就是结构变量的名字, 右操作数就是需要访问的成员的名字.点操作符的结合性是从左到右.

```
 struct COMPLEX a;
 a.s.a = 1;
 a.sa[4].c
```

###结构成员的间接访问
如果你拥有一个指向结构体的指针，那么你该怎么访问结构体的成员咧？
你可以使用间接访问操作符`*`来访问这个结构体, 然后再使用`.`操作符来访问这个结构体的成员。因为`.`操作符的优先级要高于间接访问操作符的优先级,所以你得使用`()`，例如如下的函数

```c
struct COMPLEX {
    float f;
    int a[20];
    long *lp;
    struct SIMPLE s;
    struct SIMPLE sa[10];
    struct SIMPLE *sp;
};

/// 函数的参数是一个指向结构体的指针
void func1(struct COMPLEX * const p) {
    struct COMPLEX * temp = p;
    /// 向下面这样来访问结构体的成员
    (*temp).f
}
```
更好的一种方式是使用箭头操作符, 箭头操作符接收两个操作数, 但是左操作数必须是一个指向结构的指针。箭头操作符对左操作数执行间接访问以获取结构体,右操作数来访问相应的成员.

```c
struct COMPLEX {
    float f;
    int a[20];
    long *lp;
    struct SIMPLE s;
    struct SIMPLE sa[10];
    struct SIMPLE *sp;
};

/// 函数的参数是一个指向结构体的指针
void func1(struct COMPLEX * const p) {
    struct COMPLEX * temp = p;
    /// 使用箭头操作符来访问成员,间接访问操作内建于箭头操作符中，所以不需要显示的执行间接访问或者使用括号.
    temp->f
}
```
###结构的自引用

```c
/// 非常声明 结构体b递归声明, 无止境
struct SELF_REF1 {
    int a;
    struct SELF_REF1 b;
    int c;
};
/// 合法声明
/// 包含一个指向结构体类型的指针
struct SELF_REF1 {
    int a;
    struct SELF_REF1 *b;
    int c;
};
```
如果你觉得一个结构体内部包含一个指向该结构本身的指针有些奇怪, 请记住它实际上指向的是`同一种类型的不同结构`.

警惕下面的陷阱

```c

/// Unknown type name 'SELF_REF1'
/// 类型名直到声明的末尾才定义, 所以在结构体内部它尚未定义
typedef struct {
    int a;
    SELF_REF1 *b;
    int c;
} SELF_REF1;

/// 解决方法添加标签名
typedef struct SELF_REF1 {
    int a;
    struct SELF_REF1 *b;
    int c;
} SELF_REF;

```
###不完整的声明(WHY
```
```)

```c
struct B;

struct A {
    struct B * partner;
};

struct B {
    struct A * partner;
};
/// 在A的成员列表中需要标签B的不完整的声明, 一旦A被声明之后, B的成员列表也可以被声明.
```

##结构体的初始化

```c
typedef struct {
    int a;
    char b;
    float c;
} SIMPLE;

typedef struct {
    int a;
    short b[10];
    SIMPLE c;
} VALUE;

/// 初始化方式类似于多维数组
 VALUE a = {
        1,
        {1, 2, 3, 4, 5, 6},
        {1, 'c', 1.0}
    };
    printf("%d\n", a.a);
```

###结构，指针和成员

```c
typedef struct {
    int a;
    short b[2];
} EX2;

typedef struct EX{
    int a;
    char b[3];
    /// 结构体成员
    EX2 c;
    /// 指向结构体的指针
    struct EX *d;
}Ex;

/// 初始化
    Ex x = {
      10,
      "h1",
        {1, {-1,25}},
       0
    };
    Ex *px = &x;
    /// 类型不同 不能赋值
    /// int *pi = px;
    /// 箭头操作符的优先级要高于&操作符的优先级
    /// pi和px拥有相同的值，但是它们的类型是不同的
    int *p1 = &px->a;
    printf("%p\n",p1);
    printf("%p\n",px);
    /// 访问嵌套的结构
    printf("%d\n",px->c.a);
    /// p1 0x7ffeefbff5c8
    /// px 0x7ffeefbff5c8
     /// 1
    printf("%d\n",px->c.a);
    /// -1 
    printf("%d\n",*px->c.b);
```

###访问指针成员

```c
typedef struct {
    int a;
    short b[2];
} EX2;

typedef struct EX{
    int a;
    char b[3];
    /// 结构体成员
    EX2 c;
    /// 指向结构体的指针
    struct EX *d;
}Ex;

/// 初始化
    Ex x = {
      10,
      "h1",
        {1, {-1,25}},
       0
    };
    Ex y;
    x.d = &y;
    Ex *px = &x;
    px->d->a = 1;
```

###结构的存储分配
编译器按照成员列表的顺序一个接一个的给每个成员分配内存, 只有当存储成员时需要满足正确的边界对其要求时,成员之间才可能出现用于填充的额外空间.例如

```c
struct ALIGH {
    char a;
    int b;
    char c;
};
```
它的内存布局,假设整型长度为4个字节
![](https://ws1.sinaimg.cn/large/006tNc79gy1fqxeb7456wj30b001zmx1.jpg)

使用`offsetof`计算偏移量

```c
#include <stddef.h>

struct ALIGH {
    int b;
    char a;
    char c;
};
/// 会偏移5个字节
    printf("%lu\n", offsetof(struct ALIGH, c));
    
    
    struct ALIGH {
    char a;
    int b;
    char c;
};
/// 会偏移8个字节
    printf("%lu\n", offsetof(struct ALIGH, c));
```
![](https://ws2.sinaimg.cn/large/006tNc79gy1fqxekmec8ij30n103874k.jpg)

###作为函数参数的结构
结构变量是一个标量, 它可以用于其他标量可以使用的任何场合. 因此, 把结构作为参数传递给一个函数是合法的, 但这种做法往往并不适宜.

```c
/// 这个结构占据32个字节的空间
typedef struct {
    char  product[20];
    int   quantity;
    float price;
} Transaciton;

void print_receipt( Transaciton trans) {
     printf("%f\n", trans.price);
}

void print_receiptLoc( Transaciton *trans ) {
     printf("%f\n", trans->price);
}

/// 使用register修饰, 用于把堆栈中的参数(参数首先传递到堆栈)复制到寄存器,以提高指针传递的效率
/// 使用const修饰结构体, 防止其值被修改
void print_receiptLocation( register Transaciton const *trans ) {
    printf("%f\n", trans->price);
}


 Transaciton trans = {
        {'a', 'b', 'v'},
        2,
        1.2
    };
  /// 这个方法可以产生正确的结果, 但是效率比较低下. 因为传值调用需要将参数的拷贝传递给函数.
  print_receipt(trans);
  /// 传址调用效率能提高很多, 指针比整个结构要小的多, 所以把它压到堆栈上效率能提高很多. 结构越大,把指向它的指针传递给函数的效率也就越高.
  print_receiptLoc(&trans);
```



```c 
/// 计算总和
Transaciton compute_total_amout( Transaciton trans) {
    trans.totalAmount = trans.quantity * trans.price;
    return trans;
}

void compute_total_amouts( Transaciton *trans ) {
    trans->totalAmount = trans->price * trans->quantity;
}

```
什么时候你应该向函数传递一个结构而不是指针？
`answer`: 很少有这种情况, 只有当一个结构特别的小(长度和指针相同或更小), `结构传递方案`的效率才不会输给`指针传递方案`.

##位段

```c   
有符号数就是用最高位表示符号（正或负），其余位表示数值大小，无符号数则所有位都用于表示数的大小

/// 位段的声明
/// 位段可以将长度为奇数的数据包装在一起, 节省存储空间.
struct CHAR {
    /// 128个不同的字符值
    unsigned ch     :7;
    /// 64种不同的字体
    unsigned font   :6;
    /// 以及0到524287个单位的长度
    unsigned size   : 19;
};

```
![](https://ws1.sinaimg.cn/large/006tKfTcgy1fqxuxw4em6j30kw05t3yq.jpg)
结构实现位段的能力.

使用位段只是基于方便的目的, 任何可以用位段实现的任务都可以使用移位和`屏蔽`来实现.

位段的移植性较弱 优点是简化了源代码.

##联合
联合的声明和结构类似, 但它的行为方式却和结构不同. 联合的所有成员引用的是内存中的相同位置. 当你想在不同的时刻把不同的东西存储于同一个位置时, 就可以使用联合.

```c
struct VARIABLE {
    /// 一个变量被创建时, 解释器就创建一个这样的结构并记录变量的类型.然后根据变量的类型将值存储在这三个值字段的其中一个.
    enum { INT, FLOAT, STRING} type;
    int int_value;
    float float_value;
    char *string_value;
    /// 效率低下的原因在于它所占用的内存. 每个VARIABLE变量存在两个未使用的值字段。联合就可以减少这种浪费.
};

struct VARIABLEUNUNION {
    enum { INT, FLOAT, STRING } type;
    /// 同一个位置可以用于存储这三种不同类型的值.
    /// 如果联合的各个成员具有不同的长度, 联合的长度就是它最长成员的长度.
    union {
        int i;
        float f;
        char *s;
    }value;
};
```

##变体记录

```
struct PARTNFO {};
struct SUBASSYINFO {};
struct INVREC {
    char partno[10];
    int quan;
    enum {PART, SUBASSY} type;
    /// 在一个成员长度不同的联合里, 分配给联合的内存数量取决于它的最长长度. 如果成员的长度相差悬殊, 当存储较短的成员时, 其浪费的空间是相当可观的. 更好的方法是在联合中存储指向成员的指针而不是直接存储成员本身. 因为指针的长度都是相同的，这样就解决了内存浪费的问题
    union {
        struct PARTNFO part;
        struct SUBASSYINFO subassyinfo;
    } info;
};
```

### 联合的初始化

```
union {
        int a;
        float b;
        char c[4];
    }x = {
        1.999
    };
    /// 联合变量可以被初始化, 但这个初始值必须是联合第一个成员的类型,而且它必须位于一对花括号里面.如果给出的初始值是任何其他类型,它就会尝试如果可以的话转换为一个整数并且赋值给x.a
    printf("%d\n",x.a);
    printf("%f\n",x.b);
```

##总结
位段允许你把长度为奇数的值包裹在一起以节省内存空间.


