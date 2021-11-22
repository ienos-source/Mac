# Runtime

```
|- 数据结构
|- 类对象与元类对象
|- 消息传递
|- 方法缓存
|- 消息转发
|- [Method-Swizzling](markdown/base/method-swizling.md)
|- 动态添加方法
|- 动态方法解析
```

### 数据结构

```
objc_object {
  isa {
    isa
    raw isa
    nonpointer_isa 共用体，利用位域存储信息 {
      nonpointer // 是否开启指针优化
      has_assoc // 是否有关联对象
      has_cxx_dtor // 是否有析构函数
      shiftcls // 存储类指针的值
      magic // 判断当前对象是对象还是未被初始化的空间
      weakly_reference // 是否有被弱引用
      deallocating // 是否正在释放
      has_sidetable_rc // 是否使用 SideTable 进行引用计数
      extra_rc // 表示该对象的引用计数值
    }
  }
}
```

### alloc & init & new

```
{
  alloc
  _objc_rootAlloc
  callAlloc {
    objc_msgSend
  }, {
    _objc_rootAllocWithZone
    _class_createInstanceFromZone {
      /// 计算出需要的内存空间
      /// 向系统申请开启内存，返回地址指针
      /// 关联到相应的类
    }
  }

}

init 直接返回 self

new 相当于 alloc + init

````

#### objc_object

```
id -> objc_object {
  isa_t // 共用体
  关于 isa 操作相关
  弱引用相关
  关联对象相关
  内存管理相关
}
```

#### objc_class

```
Class -> objc_class: objc_object {

  Class superClass

  cache_t cache

  class_data_bits_t bits {

    class_rw_t { // 可读可写

      class_ro_t, { // 只读
        name,
        ivars,
        properties, // 一维数组
        protocols, // 一维数组
        methodList [
          { // method_t
            SEL name
            const char* types
            IMP imp
          }
        ] // 一维数组
      }

      protocols, // list_array_tt 二维数组
      properties, // list_array_tt 二维数组
      methods // list_array_tt 二维数组 {{method_t}}
    }

  }

}
```

### isa 指针

指针型 isa、非指针型 isa

共用体 isa_t

```

```

### cache_t

```
cache_t {
  _buckets [
    bucket_t {
      key(SEL) : IMP
    },
    bucket_t {

    }, ...
  ]
  _mask   // 掩码
  _flags
  _occupied // 已缓存的方法数
}

{

  实例方法缓存在类上面，类方法缓存在元类上面

  利用 SEL 和 mask 掩码计算索引下标

  运用可增量扩展的哈希表结构，提前扩容防止溢出，如果缓存容量超过原来的 3/4，进行内容扩容

  开放寻址法解决哈希冲突，从发生冲突的单元起，尝试选择另外单元，直到找出空的单元为止 {
    cache 中使用线性探查法, 即 index ++，寻找下一个
    再散列
    平方探测法？
  }

}

```

### 介绍 Runtime 的内存模型

isa、对象、类、metaclass、结构体的存储信息等等

对象和类都是 objc_object 结构体，都有 isa 指针

```
实例 --isa--> Class
Class --isa--> MetaClass
```

元类的 isa 指针指向 NSObject 元类，NSObject 元类的 isa 指针指向自己

NSObject 元类的 superClass 指针指向 NSObject 类，也就是说当

对象的 isa 指针指向类，类保存着实例对象的属性列表、方法列表、成员列表等相关信息

类的 isa 指针指向元类，元类保存着类对象的方法列表及其他相关信息

相关信息包括成员列表 objc_ivar_list、方法列表 objc_method_list、协议列表 objc_protocol_list

### 消息查找过程

编译后的代码最终都会转成 objc_msgSend(id, SEL, ...)方法进行调用，runtime 通过这个 id 对象的 isa 指针找到这个对象的类对象，从类对象中的 cache 中查找是否存在 SEL 对应的 IMP，若不存在，则在类对象中查找方法列表，找到对应的方法实现，如果没有找到则顺次查找 superClass 的方法列表，直到 superClass = nil

class -> isa 指针找到元类对象，在元类对象中查找方法列表，找到对应的方法实现，如果没有找到则顺次查找 superClass 的方法列表，当找到 Root Class 元类时，Root Meta Class 的 superClass 为 Root Class，Root Class 的 superClass = nil

![isa & superClass](/resource/runtime-isa.jpeg)

### 消息传递

```
void objc_msgSend(id self, SEL op, ...)
```

```
[super class] objc_msgSendSuper(struct objc_super *super, SEL op, ...)

/// 编译器关键字
struct objc_super {
    __unsafe_unretained id receiver; // self
}

[self class] objc_msgSend

```

- super 是编译器的指示符，不是指针，只是一个标识符，代表调用父类的方法，调用者还是自己本身
- superClass 获取该类的父类方法

例如 Person 继承 NSObject

```
@interface Person: NSObject
@end
```

在 Person 中调用实现下面几个方法

```
/// Person
[self class]

/// NSObject
[self superClass]

/// Person
[super class]

/// NSObject
[super superClass]

/// NSObject
[[super class] superclass]

```

```
{
  缓存是否命中 invoke
  当前类方法列表是否命中
  逐类父类方法列表是否命中
}
```

### 消息转发

```
{
  // 参数为 Selector，判断是否处理 Selector 方法，类方法，这里可以动态添加方法实现
  - (BOOL)resolveInstanceMothod:(SEL)
   {
     // 动态添加方法实现
      class_addMethod(self, @selector(test), testImp, "v@:")
  }

  // 转发目标
  - (id)forwardingTargetForSelector:(SEL)

  // 返回方法签名 [NSMethodSignature
  - (NSMethodSignature)methodSignatureForSelector:(SEL)  signatureWithObjCType:"v@:"]

  // 判断是否能处理该方法
  - forwardInvocation:

  - 消息无法处理
}
```

### 缓存查找

给定值是 SEL，目标值是对应 bucket_t 中的 IMP

cache_key_t -> f(key) -> bucket_t

f(key) = key & mask

### 当前类中查找

- 对于已排序好的列表，采用二分查找算法查找方法对应执行函数
- 对于没有排序的列表，采用一般遍历查找方法对应执行函数

### 父类逐级查找

开始 -> curClass = curClass -> superclass -> curClass == nil -> 结束

curClass != nil -> 缓存是否命中 -> 方法列表是否命中 -> curClass -> superclass


### 为什么要设计 metaclass

metaclass 是元类，存储类的方法，目的是将它与实例方法及构建信息区分开

### 动态添加方法

performSelector: 在运行时使用，配合 respondsToSelector: 判断该对象是否响应该方法，该方法可以指定线程，但是使用时要确保 RunLoop 是否是开启状态，原理是添加一个 Timer，在 RunLoop 中执行

其他: 利用 performSelector 防止多次点击的应用
```
cancelPreviousPerformRequestsWithTarget:
performSelector:afterDelay:0.2s
```

相对于 NSMethodSignature 和 NSInvocation，performSelector 只能传递一个参数

```
{
  NSMethodSignature alloc]initWithSelector];
  NSInvocation alloc]initWithMethodSignature]; {
    target
    selector
    setArgument:atIndex
    invoke
    getReturnValue
  }

}
```


### 动态方法解析

@dynamic

- 动态运行时语言将函数决议推迟到运行时
- 编译时语言在编译期间进行函数决议

### load

当类被加载的时候，被调用，且只调用一次；调用的方法并不是采用 runtime 的 objc_msgSend 方法调用，而是采用函数地址直接调用多个类的 load 调用顺序。load 调用顺序是依赖于 Complile Sources 的文件顺序决定的。父类方法优先于子类方法，Category 优先于本类的 load 方法，所有 load 方法都会被调用

调用判断规则如下: 如果当前类没有父类，则调用该类的 load 方法。如果当前类还有父类，则从该类的父类开始调用，直到当前类，然后开始下一个文件 Complile Sources 文件判断

### initalize

当类或子类第一次收到消息时被调用，即静态方法或实例方法第一次被调用，也就是这个类第一次被用到的时候，只调用一次。调用方式是通过 runtime 的 objc_msgSend 的方式调用的，此时所有的类已经装载完毕。子类和父类同时实现 initialize，category 会覆盖本类的方法，只调用 category 的，在类或其子类的第一个方法被调用前调用，由于是系统调用，不再需要调用 [super initalize]

- 父类的 initalize 方法会比子类先进行
- 当子类未实现 initalize 方法时，会调用父类 initalize 方法，子类实现 initalize 方法时，会覆盖父类的 initalize
- 当有多个 Category 都实现了 initalize 方法，会覆盖类中的方法，只执行一个（会执行 Complile Sources 列表中最后一个 Category 的 initalize）

### class_copyIvarList & class_copyPropertyList

- class_copyIvarList 获取的是成员变量列表，@interface {} 中声明的变量
- class_copyPropertyList 获取的是属性列表，@property 声明的变量

###

编译后的类不能新增实例变量，动态添加的类可以新增实例变量
