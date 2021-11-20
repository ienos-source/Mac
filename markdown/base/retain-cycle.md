# 循环引用


### 分类

```

/// 自循环引用
{
   id __strong obj -> self  
}

/// 相互循环引用
{
    A { id __strong obj -> B }
    B { id __strong obj -> A }
}

/// 多重循环引用
{
    A { id __strong obj -> B }
    B { id __strong obj -> C }
    C { id __strong obj -> A }
}

```

### 破除循环引用

- 避免产生循环引用
- 在合适的时候手动断环

### 具体解决方案

- __weak
- __block
- __unsafe_unretained

```
/// __weak
{
    A { id __weak obj -> B }
    B { id __strong obj -> A }
}

/// __block 破解
MRC 下，__block 修饰对象不会增加引用计数，避免了循环引用
ARC 下，__block 修饰对象会被强引用，无法避免循环引用，需手动解环

/// __unsafe_unretained
修饰对象不会增加其引用指针，避免了循环引用
如果被修饰对象在某一时机被释放，会产生悬垂指针

```

### 场景

##### 代理
##### Block
##### NSTimer

- RunLoop 强持有 NSTimer
- NSTimer 强持有 objc_A(视图)，objc_A 强持有 NSTimer
- objc_A 被 VC 持有

当 NSTimer 是一个循环定时器时，NSTimer 会一直持有 objc_A，造成循环引用

需要添加一个中间对象，中间对象弱持有 objc_A
然后每次定时器循环时对 objc_A 是否为 nil，如果 objc_A == nil，timer invaild， 并且置空

##### 大环引用
