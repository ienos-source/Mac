# 内部实现篇

### [UIImage imageNamed:]

使用 imageNamed 这个方法生成的 UIImage 对象，会在应用的 bundle 中寻找图片，如果找到了 Cache 到系统缓存中，该区域程序员无法操作 Cache; 通过重新加载该图片，系统会自动从 Cache 中找到这张图片，当加载很多大量的图片时，内存消耗过大的时候，就会强制释放内存，遇到内存警告（Memory warnings）

在 iOS 系统中释放图片的内存比较麻烦，所以容易产生内存泄露，像 [[UIImageView alloc] init] 还有其他一些 init 方法，返回的都是 autorelease 对象，而 autorelease 不能保证什么时候释放，所以不一定在引用计数为 0 的时候就立即释放，只能保证在 autorelease 结尾的时候释放

像 UIImage 还有 NSData 这种，大部分情况应该是延迟释放的，可以理解为到 autorelease 结束的时候才释放

### NSArray NSDictionary 的数据结构

### NSMutableArray

insert delete 的时候都做了什么操作

### 为什么刷新 UI 必须在主线程

[参考链接](https://blog.csdn.net/u014600626/article/details/108244386)

这个问题转换为: 为什么 Apple 建议刷新 UI 必须在主线程。如果在多线程里面刷新 UI 会怎样？

首先在多线程里面如果操作同一个视图会造成数据安全问题，假如说在两个线程中同时操作了同一个视图，就很有可能由于视图释放多次，导致程序崩溃。

其次，如果多个线程同时操作同一个视图，会造成页面的卡顿和不流畅。因为绘图过程是在 RunLoop 的中的一个阶段进行的，每个绘图过程会有对应多个事务。因为每一个线程会有对应一个 RunLoop，如果对同一个视图进行操作，但是 RunLoop 循环重绘时机不一致，可能会看到多个 view 变化过程而不是立即变化

另外，多个线程不同 RunLoop 对应不同的渲染事务，多个渲染事务会延长渲染时间，导致画面卡顿


### UIView & CALayer

单一职责原理

- UIView 为 CALayer 提供内容，以及负责处理触摸等时间，参与响应链
- CALayer 负责显示内容 contents
