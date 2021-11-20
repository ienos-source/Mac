# 内存管理篇

```
|- 内存布局
|- 内存管理方案
|- 数据结构
|- ARC & MRC
|- 引用计数
|- 弱引用
|- 自动释放池
|- 循环引用
```

### 什么是野指针
指的是指针指向已经被释放了的内存地址

一般情况下会使用 Zombie Object 进行调试，hook 到 `object_dispose(id objc)` 函数中，在 `free` 前调用 `object_setClass(self, zombieClass)`，修改对象的 isa 指针，指向一个名为 `__NSZombie` + 类名 的结构体。当该对象再次收到 `objc_msgSend`，调用 abort() 崩溃并打印出调用的方法

### Objective-C 主要有三种方式

ARC(自动内存计数)、手动内存计数、内存池

### 内存管理的几条原则

### autorelease

### 数据持久化


### 内存分区

为了让内存最大程度被利用、堆和栈分别一个从高位到地位，一个从低位到高位开始分配，直至最后内存发生溢出

栈是从高到低的一块连续内存空间，先进后出
堆是从低到高且不连续的内存空间，会存在内存碎片，链表结构

```
内核区            高 0xc0000000

栈 Stack 🔽


堆 Heap 🔼

程序

  未初始化数据 .bss
  未初始化的全局变量

  已初始化数据 .data
  已初始化的全局变量

  代码段      .text 低 0x08048000
  程序代码

保留


```

- 堆
- 栈: 自动变量
- 静态区（全局区）
- 常量区
- 代码区

### 内存管理方案

```
- TaggedPoint 小对象 NSNumber

- nonpointer_isa 64bit，内存管理的数据内容 {

  indexed // 是否为 nonpointer_isa
  has_assoc // 关联对象
  has_cxx_dtor //
  shiftcls // 指针地址
  magic //
  weakly_reference // 弱引用
  deallocating //
  has_sidetable_rc // 是否用 SideTable
  extra_rc // 额外引用计数

}

- 散列表 引用计数表，弱引用计数表 {

  SideTables() 哈希表
  [
    64 个 SideTable（非嵌入式）
  ]

  SideTable {
    spinlock_t // 自旋锁
    RefcountMap // 引用计数表  
    weak_table_t // 弱引用表
  }

  /// 为什么不是一个 SideTable，考虑到多线程访问需要加锁处理，如果所有对象都操作同一张表会存在效率问题

  /// 分离锁
  当一个资源可以分成很多部分的时候，如果对每个部分使用不同的锁，那么资源的争用就大大降低

  /// 如何实现快速分流
  SideTables 本质是一张 Hash 表，如何快速定位某个对象的引用计数在哪个 SideTable

  ptr % array.count

}
```

### 自旋锁

spinlock_t 是一种"忙等"的锁。对这个线程进行阻塞休眠，适用于轻量访问（适用 SideTable，只需要引用计数 +1/-1）

### 引用计数表

RefcountMap

ptr -- DisguisedPtr(objc_object) --> size_t

```
{ // 数据结构 size_t
  weakly_reference
  deallocating
  RC
}
```

### 弱引用表

weak_table_t

```
weak_table_t hash 函数后得到 weak_entry_t {
  weak_entry_t 是 weakPtr 数组
}
```

### 自动释放池 AutoreleasePool

### MRC

手动引用计数

alloc、retain、release、retainCount、autorelease、dealloc

### ARC

自动引用计数

- ARC 是 LLVM 和 Runtime 协作的结果
- ARC 中禁止手动调用 retain/release/reatinCount/dealloc
- ARC 中新增 weak、strong 属性关键字

### 引用计数管理

实现原理分析


```
- alloc: 经过一系列调用，最终调用了 C 函数 calloc，此时并没有设置引用计数为 1

- retain:

// 经过两次哈希查找
SideTable& table = SideTables()[this];
// size_t 无符号 long 型
size_t& refcntStorage = table.refcnts[this];
refcntStorage += SIDE_TABLE_RC_ONE;

- release

SideTable& table = SideTables()[this];
RefcountMap::iterator it = table.refcnts.find(this);
it->second -= SIDE_TABLE_RC_ONE;

- retainCount

SideTable& table =  = SideTables()[this];
size_t refcnt_result = 1;
RefcountMap::iterator it = table.refcnts.find(this);
refcnt_result += it->second >> SIDE_TABLE_RC_ONE;

- dealloc
_objc_rootAlloc() -> rootDealloc {

  是否可以直接释放，如果是调用 C 函数 free
  - nonpointer_isa
  - weakly_reference
  - has_assoc // 是否有关联对象
  - has_cxx_dtor // 是否有 C++ 关联对象
  - has_sidetable_rc // 引用计数表是否通过 SideTable 控制

  否则需要调用 object_dispose {
    objc_destructInstance() {
      当前对象是否有 C++ 相关内容, object_cxxDestruct()
      当前对象是否有关联对象, _object_remove_assocations()
      clearDeallocating {
        sidetable_clearDeallocating()
        weak_clear_no_lock // 将指向该对象的弱引用指针置 nil
        table.refcnts.erase() // 从引用计数表中擦除该对象的引用计数
      }
    }
    C 函数 free
  }

}

```


### 修饰词

- atomic
- weak
- strong
- copy

### 循环引用

- block
- delegate
- timer
- WKWebView
- delegate
