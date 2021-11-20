# Block

[Block-ABI-Apple](https://opensource.apple.com/source/lldb/lldb-167.2/llvm/tools/clang/docs/Block-ABI-Apple.txt.auto.html)


### 本质

携带上下文的对象

### 结构

```
{
    isa,
    FuncPtr,
    flags,
    reserved,
    desc {
      block_size,
      reserved,
      copy & dispose(id 类型) {
        栈 -> 堆
        堆 引用计数 + 1
        全局不处理
      }
    }

}
```

### 形式

- 有参有返
- 有参无返
- 无参无返
- 无参有返

### 分类

- 栈：编译时
- 堆：栈 copy 变成 堆
- 全局：没有捕获变量、编译时

### __block

##### 作用

将非对象类型包裹成对象类型

##### 对象分类

对象类型和常见数据类型

##### 结构

isa、__forwarding、flags

__forwarding 保证当对象在堆上，确保值的更新及获取是以堆上的为准

### 修饰词

- copy: 防止在栈区中随作用域的结束而丢失数据
- strong: retain 实现依旧是 copy

### 对象捕获

##### __block 修饰

```
{
   常见数据类型
   对象类型 {
     分类 { id block }
     新增方法 {
       copy & dispose {
         场景 {
           __weak __block block
           __weak __block id
           __block id
           __block block
         }
         不处理
       }
     }
   }
   结构 {
      isa
      flags {
        引用计数
        是否有 copy & dispose
        内存管理模式 (strong/weak/GC)
        堆 / 栈
      }
      reserved
      size
      __forwarding {
        栈 -> 堆
        堆 -> self
      }

   }
}
```

##### 根据变量分类

- 全局变量: 直接取值不捕获
- 静态全局变量: 直接取值不捕获
- 局部变量: 对象指针拷贝，常见数据类型值拷贝
- 静态局部变量: 指针捕获
- 静态与非静态区别: 是否只作用于该源文件

### 循环引用

原因: A 持有 B，B 持有 A，引用计数始终大于 0，无法释放

解决方法: 中介者模式 + 弱指针，A 持有 B，B 持有指向 A 的弱指针

weak + strong: 防止 block 已经在执行过程中， weak 指向的对象提前释放

为什么用 weak 而不用 unretained？虽然均不进行强引用，但当对象释放时，指针仍指向原先的地址

### MRC / ARC

##### ARC 调用 copy 时机

- block 作为返回值
- 将 block 赋值给 __strong 指针
- block 作为 usingBlock 方法参数
- block 作为 GCD 方法参数

##### MRC

### 源码

libclosure

### 应用场景

反向传值
