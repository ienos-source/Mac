# 编译原理

[iOS开发你不知道的事-编译&链接](https://juejin.cn/post/6844903841842872327)

```
|- 编译原理
    |- 预处理 Prepressing /// 用于导入文件和展开宏，纯文本操作
        |- .h
        |- .cpp
        |- .i
    |- 编译 Compilation /// 对预处理器的输出进行编译
        |- .i
        |- 词法分析
        |- 语法分析 /// 最终生成 AST (Abstract Syntax Tree)
        |- 语义分析
        |- .s /// 生成汇编语言
    |- 汇编 Assembly /// 汇编转为机器语言
        |- .s
        |- .o
    |- 链接 Linking /// 目标文件本身不能使用，将目标文件转换为最终可以使用的形式过程
        |- .o
```

```
    源代码 Source Code ->
    分割系列记号 Token —>
    语法树 Syntax Tree ->
    中间层 Intermedicate Representation ->
    目标代码 Target Code ->
    最终代码 Final Target Code ->
```
