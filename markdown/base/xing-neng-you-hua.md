# 性能优化篇

### 启动优化
启动优化分为冷启动、热启动
- 冷启动为一个应用程序新开辟一个新的进程
- 热启动指应用程序退到后台，然后重新进入前台

冷启动，分为三个阶段：main() 函数执行前、main() 函数执行后、首屏渲染后

main 函数执行前
- mach-O 可执行文件加载，dyld(dynamic link editor)
- 动态库的加载，dyld(dynamic link editor)
- C++ 全局变量的创建
- 类的 load、category 分类加载， object 类的初始化处理和注册

**分析启动加载时长**

Xcode -> Project -> Scheme -> Edit Scheme，然后找到 Run -> Environment Variables -> +，添加 name 为 DYLD_PRINT_STATISTICS，value 为 1 的环境变量

**扫描并移除不需要用到的类**

使用 fui(Find Unused Imports) 的开源项目，不能处理动态库和静态库里提供的类，也处理不了 C++ 的类模板，配合手动检查增加准确度

**类扩展**

合并功能类似的类和扩展



runtime: call_load_methods、register classes、static inital

main 函数执行后：执行 main 函数到 appDelegate  的 `didFinishLaunchWithOptions:` 中的首屏渲染结束
- 基本库的加载及初始化
- 首屏渲染的处理

**首屏渲染**

在 appDelegate 中的 `didFinishLaunchWithOptions:` 中，我们会对根视图进行初始化，如果当 UITabBarViewController 作为根视图时，会将嵌套的 ChildViewController 的所有 `viewDidLoad` 方法都初始化一遍，可以通过 viewController 进行延后加载，这样用户就会阶段性地获得视觉上的变化



首屏渲染后： appDelegate 中的 `didFinishLaunchWithOptions:` 首屏渲染结束，到该代理方法结束前
- 除首屏渲染之外的其他业务模块的初始化
- 注册监听

优化方案：省略去不必要的加载和使用，例如去除没有使用的类；然后计算各个地方所花费的时间，可以使用 Time Profile，或者利用 hook msgSend 得出更加接近实际花费时间，然后对细分步骤进行分析优化


### 渲染优化

### TableView 优化、怎么减少卡顿

### 应用瘦身

```
|- 资源文件

    |- APP Thining(包切片)
        |- Assets.xcassets: 1x 2x 3x
        |- Architecture: x86 arm64

    |- 资源压缩
        |- 有损压缩
        |- 无损压缩

    |- 去除无用资源

|- 可执行文件

    |- 去除无用代码
    |- 减少动态库使用

```

### 卡顿

渲染过程

- CPU { Layout / Display / Image Encode(Decode) / Bitmap }
- GPU { 顶点着色器、片元着色器、光栅化 }
- Frame Buffer
- 视频控制器
- 显示器



![](/resource/vsync.png)


### UITableView 相关

```
cell = [tableView dequeueResuableCellWithIdentifier: identifier];
```

cell 滑出 UITableView 可视区域之后，cell 被放到重用池中，便于下次使用

### 数据源同步问题

主线程删除操作，子线程数据处理完成后 Load More，会造成数据不同步的问题

- 并发访问，数据拷贝

```
数据拷贝 -> 网络请求、数据解析、预排版等

用户手动删除一行数据，记录删除操作

等待子线程回调完成后，同步删除操作，最后再刷新 UI

```

- 串行访问

```
网络请求、数据解析

如果主线程删除某一行需要等待，数据预排版完成后再进行同步数据删除

最后 reloadUI
```

### 崩溃

- 符号化
- 无符号化
