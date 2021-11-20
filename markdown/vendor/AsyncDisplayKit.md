# AsyncDisplayKit

提升 iOS 界面渲染性能的一个框架

### 主要处理问题

减轻主线程的压力，尽可能放在子线程

- Layout { 文本宽高计算 视图布局计算 } 放在子线程计算
- Rendering { 文本渲染 图片解码 图形绘制 }
- UIKit Objects { 对象创建 对象调整 对象销毁 }

### 基本原理

```
ASNode <-> UIView <-> CALayer

    后台线程       主线程
```

针对 ASNode 的修改和提交，会对其进行封装并提交到一个全局容器当中

ASDK 也在 RunLoop 中注册一个 Observer

当 RunLoop 进入休眠前，ASDK 执行该 loop 内提交的所有任务
