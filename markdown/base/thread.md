# 多线程

### 知识树

```
|- GCD

    |- 线程组
        |- 多请求任务后统一刷新 UI
        |- enter group / leave group

    |- 栅栏

        |- 多读单写
            |- 同步读取、异步栅栏写入
            |- 读读并发且及时，读写、写写互斥

        |- 控制任务顺序执行

    |- 信号量
        |- 锁
        |- 最大并发数

    |- 延迟函数
        |- NSTimer

|- NSOperation

    |- 子类
        |- NSBlockOperation
            |- 并发 blocks

    |- 状态（KVO）
        |- isPend
        |- isReady
        |- isExcuting
        |- isFinished

    |- 方法
        |- mian
        |- start 重写并控制状态

    |- NSOperationQueue

        |- 原则
            |- 已经 ready 的 operation 会优先执行

        |- 最大并发数
            |- 串行
            |- 并行

        |- 方法
            |- cancelAllOperation
            |- resume
            |- addOperation

        |- 依赖
            |- 控制任务顺序
            |- isPend 前判断是否已经依赖，是否已经 isFinished

        |- 优先级
            |- 不能控制任务顺序
            |- ready 后控制优先级排序执行

|- NSThread
    |- 常驻线程

|- 队列

    |- 串行队列
        |- 主队列
            |- 一定在主线程中进行，因为不会开辟新的线程

    |- 并行队列
        |- 全局队列

|- 任务

    |- 并发/单核
    |- 并行/多核

|- 线程锁

    |- 自旋锁
        |- OSSpinLock

    |- 互斥锁
        |- NSLock

    |- 条件锁
        |- @synchorized

|- 进程
    |- 线程 1
    |- 线程 n

|- 源码阅读
    |- GNUStep

```


### 多线程有哪几种，你更倾向于哪一个?

1. NSThread
2. Cocoa NSOperation（使用 NSOperation 和 NSOperationQueue）
3. GCD (Grand Centeral Dispatch)


**NSThread**

```
[NSThread detachNewThreadSelector: @selector(doSomething:) toTarget: self withObject: nil];

NSThread *mThread = [[NSThread alloc] initWithTarget: self selector: @selector(doSomething:) object: nil];

[mThread start];
```

优点: NSThread 比其他两个轻量级
缺点: 需要自己管理线程的声明周期，线程同步，线程同步时对数据的加锁会有一定的系统开销

**Cocoa Operation**

```
NSOperationQueue *operationQueue = [[NSOperationQueue alloc] init];
[operationQueue addOperationWithBlock:^{
    /// 这个 block 语句块在子线程中执行
}];
```
优点: 不需要关心线程管理、数据同步的事情。Cocoa Operation 相关的类是 NSOperation, NSOperationQueue。NSOperation 是抽象类，使用它必须用它的子类，可以实现它或者使用它定义好的两个子类；NSInvocationOperation 和 NSBlockOperation。创建 NSOperation 子类的对象，把对象添加到 NSOperationQueue 队列里面执行，我们会把我们的执行操作放在 NSOperation 中 main 函数中。

**GCD**

Grand Central Dispatch(GCD) 是 Apple 开发的一个多核编程解决方法，GCD 是一个替代诸如 NSThread, NSOperationQueue, NSInvocationOperation 等技术的很高效和强大的技术。它让程序平行排队的特定任务，根据可用的处理资源，安排他们在任何可用的处理器核心上执行任务，一个任务可以是一个函数(function)或者是一个 Block。dispatch queue 分为下面三种:
- private dispatch queues，同时只执行一个任务，通常用于同步访问特定的资源或数据
- global dispatch queue 可以并发执行多个任务，但是执行完成的顺序是随机的
- mian dispatch queue 它是在应用程序主线程上执行任务的
