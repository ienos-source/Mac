# 底层原理篇

### 对象、类的区别
对象可以调用成员变量和实例方法，类方法只可以调用类方法

### 成员变量、实例变量、属性变量的区别
用 Class 类实例化后的对象称为实例变量
```Objective-C
Person *person = [[Person alloc]init];
```
成员变量指的是在类中 `{}` 声明的成员，实例变量是一种特殊的成员变量
```Objective-C
@interface Person: NSObject
{
    NSString *name;
}
@end
```
为成员变量添加 setter 和 getter 方法的变量称为属性变量
```Objective-C
@interface Person: NSObject

@property (nonatomic, strong) NSString *name;

@end
```
### nonatomic 、atomic
在属性修饰词中默认使用 atomic，atomic 能够允许多线程访问，但是不能确保数据的安全性；例如当在线程 A 在调用 getter 方法时，线程 B 和 C 中使用不同的值调用 setter 方法，A 有可能得到三种不同的值，包括调用 setter 方法之前的值 和 setter 方法之后的两个值
-  原子性 atomic
- 非原子性 nonatomic

atomic 的内部实现

```
id objc_getProperty(id self, SEL _cmd, ptrdiff_t offset, BOOL atomic) {
     ...
     id *slot = (id*) ((char*)self + offset);
     if (!atomic) return *slot;  
     // Atomic retain release world
     spinlock_t& slotlock = PropertyLocks[slot];
     slotlock.lock();
     id value = objc_retain(*slot);
     slotlock.unlock();
     return objc_autoreleaseReturnValue(value);
}

static inline void reallySetProperty(id self, SEL _cmd, id newValue, ptrdiff_t offset, bool atomic, bool copy, bool mutableCopy) {
    ...
    if (!atomic) {
      oldValue = *slot;
      *slot = newValue;
    } else {
      spinlock_t& slotlock = PropertyLocks[slot];
      slotlock.lock();
      oldValue = *slot;
      *slot = newValue;        
      slotlock.unlock();
    }
    objc_release(oldValue);
}
```

property 的 atomic 是采用 spinlock_t 自旋锁实现的

### 深拷贝、浅拷贝

区别是是否创建一个新的对象，还是只是指针拷贝


### Objective-C 是否可以实现多继承

Objective-C 的类不可以多继承；可以实现多接口，通过实现多个接口完成 C++ 的多重继承； Category 是类别，一般情况下用分类好，用 Category 去重写类的方法，仅对本 Category 有效，不会对其他类和原有类造成影响

### #import、#include、@class、#import<>、#import ""

- #import 导入 Objective-C 头文件时使用，防止重复导入
- #include 导入 C/C++ 头文件的关键字
- @class 告诉编译器某个类的声明，当执行时，才会去看类的实现文件，可以解决头文件的相互包含
- #import <> 用来包含系统的头文件
- #import "" 用来包含用户头文件

### 属性关键字的作用

- readwrite 可读可写属性, getter 和 setter
- readonly 只读属性, getter
- assign 赋值属性, setter
- retain 持有属性, setter, retainCount + 1
- copy 拷贝属性, setter, 需要完全一份新的变量
- nonatomic 非原子属性, 决定 setter 和 getter 是否是原子操作
- atomic 表示多线程安全, 系统默认 atomic, 与 nonatomic 相反

### id 声明的对象

id 声明的对象具有运行时的特性，即可以指向任意类型 Objective-C 的对象

### +load & +initialize 的区别

### Category 和 Extension

Extension 在编译的时候，它的数据就已经包含在类信息里面了，而 Category 是在运行时才将数据合并到类当中的
