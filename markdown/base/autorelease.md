# Autorelease

[Apple-Autorelease](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html#//apple_ref/doc/uid/20000047-CJBFBEDI)

### 结构

是以栈为节点，通过双向链表的形式组合而成，是和线程一一对应的

```
{
    id *next;
    AutoreleasePool* const parent;
    AutoreleasePool* child;
    pthread_t const thread;  
}
```

每个 AutoreleasePoolPage 中包含一个栈，从高地址向低地址开始对每个对象进行入栈操作

每次进行 AutoreleasePoolPage::push 就是在栈中 push 一个哨兵对象，这个哨兵对象的值为 nil，next 指针指向下一个对象

AutoreleasePoolPage::pop 就是将，距离哨兵对象位置（也就是上次 push 的节点）的所有对象依次发送 release 消息，并回退 next 指针

```
- (void)viewDidLoad {
  [super viewDidLoad];

  /// array 什么时候释放，
  /// 当每次 runLoop 将要结束的时候调用 AutoreleasePoolPage::pop()
  /// for alloc 图片数组等内存消耗较大的场景手动插入 autoreleasePool
  NSMutableArray *array = [NSMutableArray array];
}
```

### AutoreleasePool 的实现原理是怎样的

编译器会将 @autoreleasepool{} 改写为:

```
void *ctx = objc_autoreleasePoolPush();
{
    // code
}
objc_autoreleasePoolPop(ctx);

void* objc_autoreleasePoolPush(void) {
  void* AutoreleasePoolPage::push(void)
}

void objc_autoreleasePoolPop(void *ctx) {
  // 一次 pop 实际上相当于一次批量的 pop 操作，针对的对象是所有 {} 中的对象进行 release 操作
  AutoreleasePoolPage::pop(void* ctxt)
}

```

### AutoreleasePool 为何可以嵌套使用

多层嵌套就是多次插入哨兵对象，系统哨兵对象插入

### 分析中所使用的方法

- Clang
- 汇编: Debug -> Debug workflow ->
- 源码

### Clang

生成 .cpp

```
xcrun -sdk iphonesimulator clang -arch x86_64 -rewrite-objc main.m
```

__At


### C++ 文件

mm， Source Code Type Objective C++
