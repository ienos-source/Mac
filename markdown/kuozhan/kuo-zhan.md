# 扩展篇

### 相册组件怎么实现? 需要注意什么问题

### 图片缩放

### 图片缓存的话放到哪里

### 原生相册是怎么做到那么流畅的

### 推送 deviceToken 是否可以注销

### Bugly 原理

### 卡顿检测 - RunLoop

### FMDB

### 会导致崩溃的原因

### 跨平台方案实现？Flutter 的原理？是怎么渲染到屏幕上的

### 遇到的难题

### 检测内存泄露的方法

```
{
    静态分析 {
      Product -> Analyze
    }

    动态分析 {
      Instrument -> Leak 动态分析内存泄露(优点: 不需要写新的代码；缺点: 需要一个一个页面去点击)
      Instrument -> Allocation 工具了解内存的分配情况
      Debug Memory Graph 了解各个对象的持有情况

      MLeaksFinder {
        通过 hook viewController 的 viewWillAppear 和 viewDidDisappear 等方法中，认为在 viewDidDisappear 等方法后，需要销毁；能检测出内存泄露和循环引用，并且弹框提醒
      }
    }
}
```

### 定位 Bug

### Crash 防护怎么做

参照 AvoidCrash，使用 Method_Swizzling 替换原本方法实现，在会引起崩溃的地方添加默认实现，并收集错误堆栈，上报 Bugly

### 断点续传如何实现

断点续传分为两部: 断点、续传

- 首先需要为每条下载数据，保存当前下载进度
- 在每次下载开始之前需要先读取数据库，查询是否有未完成的记录，有就继续下载，没有就创建新的任务
- 在每次文件写入的过程中往数据库中写入下载进度
- 下载完成后，删除数据库中的下载记录

[NSURLSessionDownloadTask API 说明](https://developer.apple.com/documentation/foundation/nsurlsessiondownloadtask/1411634-cancelbyproducingresumedata?language=occ)
