### Method-Swizzling

selector1 -> IMP1
selector2 -> IMP2

selector1 -> IMP2
selector2 -> IMP1

```
+ (void)load {

    /// 获取 test 方法
    Method test = class_getInstanceMethod(self, @selector(test));

    /// 获取 otherTest 方法
    Method otherTest = class_getInstanceMethod(self, @selector(otherTest));

    /// 交换两个方法的实现
    method_exchangeImplementations(test, otherTest);

}

- (void)test {
   NSLog(@"test");
}

- (void)otherTest {
   [self otherTest];
   NSLog(@"otherTest");
}

/// 页面进出添加统计方法，AOP
```

### 使用中的注意事项

1. 需要在 load 方法中添加 dispatch_once 保证只交换一次
2. 假如说子类未实现父类方法，然后交换父类方法，会将父类的 IMP 指针进行 Swizzling
3. 加入子类和父类都未实现该方法，然后进行 Swizzling，会造成 IMP 不存在，导致交换失败，如果此时像类似如下的情况，会造成循环调用

```
- (void)test {
  [self test];
}
```
