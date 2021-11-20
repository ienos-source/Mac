# KVC

[Key-Value Coding Programm1ing Guide: About Key-Value Coding](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueCoding/index.html#//apple_ref/doc/uid/10000107-SW1)

### 本质

通过字符串获取对象属性

### 对象

NSObject

### 应用

自定义 Model 打印

```

|- Key-Value (NSKeyValueCoding)

    |- 路由，多层结构

    |- Key
        |- setValue:forKey
            |- 通过相关名字调用 setter 方法
            |- 是否允许访问成员变量
            |- accessInstanceVariableDirectly
            |- Undenfined
                |- 映射处理

        |- valueForKey
            |- 通过相关名字调用相关 getter 方法
            |- 集合类型判断
            |- accessInstanceVariableDirectly
            |- 通过相关名字获取实例变量

    |- Value

        |- 数据类型
            |- 对象类型

        |- 基本数据类型
            |- NSValue
                |- struct
            |- NSNumber
                |- int / float / char

        |- 数据结构
            |- item
            |- collection
                |- operators
```
