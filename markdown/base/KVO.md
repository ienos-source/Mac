# KVO

[如何自己动手实现 KVO](https://tech.glowing.com/cn/implement-kvo/?spm=a2c6h.13066369.0.0.6173618b3e9Evb)

[Introduction to Key-Value Observing Programming Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)

### 本质

基于 KVC 的键值观察者模式

### 思想

观察者模式

### 原理

当一个对象添加了 KVO 监听，iOS 系统会通过 Runtime 动态创建一个子类，并让这个对象的 isa 指向这个新创建的子类。子类拥有自己的 setter 方法实现，setter 方法内部会调用 willChangeValueForKey 方法，原来的 setter 方法、didChangeValueForKey 方法，在 didChangeValueForKey 方法内部优惠调用 observeValueForKeyPath 监听方法。所以本质上，调用 willChangeValueForKey 和 didChangeValueForKey 就可以出发 KVO。直接修改成员变量不会触发 KVO

### 过程

isa 指针重定向，Class，NSKVONotifying_Class(Class 子类)，_isKVO，dealloc，setPropertyName:

关联 observers -> observeInfo -> key / value

NSKeyValueWillChange / NSKeyValueDidChange

observeValueForKeyPath

removeObserver:forKeyPath

isa 复原

### IIDB

watch var

### 缺点

如果移除未添加的 KeyPath 导致崩溃，需要在 dealloc 中移除所有 KeyPath，只能在 observeValueForKeyPath

### 如何手动关闭 KVO

- 重写被观察对象的 automaticallyNotifiesObservesForKey 方法，返回 NO

- 重写 automaticallyNotifiesObservesForKey，返回 NO 注意，关闭 KVO 后，需要手动在赋值前后添加 willChangeValueForKey 和 didChangeValueForKey，才可以收到观察通知

### 哪些情况下使用 KVO 会崩溃，怎么防护崩溃

- removeObserver 一个未注册的 keyPath，导致错误 Cannot remove an observe A for the key path "str"，because it is not registered as an observer。解决方法: 根据实际情况，增加一个添加 keyPath 的标记，在 dealloc 中根据这个标记，删除观察者

- 添加的观察者已经销毁，但是未被移除这个观察者，当下次这个观察者的 keyPath 发生变化时，KVO 中的观察者的引用变成了野指针，导致 crash。解决方法: 在观察者即将销毁的时候，先移除这个观察者。也可以将观察者 observer 委托给另外一个类去完成，这个类弱引用被观察者，当这个类摧毁的时候，移除观察者对象
