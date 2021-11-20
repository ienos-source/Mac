# Macros & Variable & Const

### Static

- static 修饰符的变量只会初始化一次，会存储在静态全局区，且生命周期为程序和程序的生命周期相同

- static 可修饰局部变量、全局变量

- 修饰全局变量时，使用范围为只能在本类中使用，即 ClassA.m 和 ClassB.m 均可以声明并定义相同名字的变量

- 没有用 static 修饰的变量，全局可用

- static 修饰局部变量，程序运行过程中始终使用同一块内存空间，局部变量存在栈区，而用 static 修饰的局部变量，存在静态区

### Const

- const 用来修饰右边的基本变量或指针变量

- 被修饰的变量只读，不能被修改

< 使用 const 修饰的几种方式，*p 表示指针， p 表示变量>

| 表达式 | 结果 |
|:---:|:---:|
| int const *p | const 修饰 *p 只读，p 变量 |
| int *const p | const 修饰 p 只读， *p 变量 |
| const int *const p | const 修饰 p 和 *p 都只读 |
| int const *const p | const 此时 p 和 *p 都只读 |

### Extern

在 .m 中定义，在 .h 用 extern 修饰，用于外部引用

举例，常见我们会定义一个常量，但是具体值不会暴露在 .h 文件

在 ClassA.h 文件中声明 ```extern NSString *const variable;```，在 ClassA.m 文件中定义 ```NSString *const variable = @"";```，定义好后我们将在 ClassB 中导入 ClassA，就可以有引用该常量，除了在 ClassB 中导入 ClassA 的方法外，也可以在 ClassB.h 中声明相同的变量，然后在 ClassB.m 中引用该常量，上面两种方法都会指向 ClassA.m 中定义的变量

### Define

一般定义常量 const 会比宏更加安全，宏不做类型检查，只是替换；值的替换，需要注意表达式中优先级问题。例如 ```#define SUM(x, y) x+y```，1 * SUM(1, 2) 等价于 1 * 1 + 2

常见宏定义的使用

```
#define NAME @"SUM"
#define Log(x) NSLog(@"This is a log: x = %@", x)
```

宏定义中的操作符

|符号 | 别名 | 例子 |
|:---:|:---:|:---:|
| ## | 连接操作符 | ```#define single(name) + (instance)share##name;```
| #@ | 字符化操作符 | (字符串转字符): 例如: "1" -> '1' |
| # | 字符串化 | #代表 "", 即 #x 等价于 "x" |
| \ | 行操作符 | 宏定义的回车，在末尾添加，最后一行可不加 |
| __VA_ARGS__ | 接收不定数量的参数 | ```#define eprintf(...) fprintf(stderr, __VA_ARGS__); 在 __VA_ARGS__ 前面加 ## 时，可以省略参数输入``` |

宏的 if-else

```
#ifdef DEBUG
#elif
#else
#endif
```

不同 Target 或者不同 Environment 下不同的宏定义设置

- OC: Build Setting -> Preprocessor macros `添加对应的宏` MODE=1

- Swift: Build Setting -> Other Swift Flags `添加宏` -D MODE
