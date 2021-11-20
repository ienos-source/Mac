# Weak

Weak 修饰的释放自动被置为 nil 的实现原理

```
// 编译前
id __weak obj1 = obj;

// 编译后
id obj1;
objc_initWeak(&obj1, obj);

objc_initWeak()
storeWeak()
weak_register_no_lock()

{
  weak_table_t {
    // 存储方式分为两个部分: inline_referrers 定长数组、
    referrers 动态数组、索引为 referrer 的散列值，当 referrer 的个数不超过 WEAK_INLINE_COUNT 4 时，使用 inline_referrers，否则使用 referrers
    weak_entry_t [
      index = hash_ptr(referent)
    ]
  }
}

```

- Runtime 维护着一个 Weak 表，用于存储指向某个对象的所有 Weak 指针

- Weak 表是 Hash 表，Key 是所指对象的地址，Value 是 Weak 指针地址的数组

- 在对象被回收的时候，经过层层调用，最终会触发下面的方法将所有 Weak 指针的设置为 nil。runtime 源码，objc-weak.m 的 arr_clear_deallocating 函数

- Weak 指针的使用涉及到 Hash 表的增删改查，有一定的性能开销

### 置 nil 过程 

```
dealloc

weak_clear_no_lock


```
