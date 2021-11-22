# 组件化篇

### 路由原理

单例里面维护了一个字典，openURL 的时候，解析 URL 拿到对应的参数，以及回调 Block


### 优点

解决代码耦合、增强代码复用性、工程易管理

### 方案1: url-block

所有模块通过 MGJRouter 进行注册 `registerURL:toHandler:` 和 跳转 `openURL:`

缺点

- 需要在内存中维护 url-block 的表，组件多了会造成内存问题

- url 传递参数受限，只能常规传递一些字符串参数，无法传递非常规参数，例如 UIImage、NSData 等类型

- 没有区分远程调用和本地调用的情况，尤其是远程调用会因为 url 参数受限，导致一些功能受限

- 组件本身依赖了中间件，且分散注册使的耦合较多


### 方案2: protocol-class

通过 protocol 定义服务接口，把 class 和 protocol 做一个映射表，同时在内存中保存一张映射表，使用时就通过 protocol 找到对应的 class 来获取需要的服务

解决了无法传递非常常规参数的问题，使组件间的调用更加方便，但是仍然无法解决组件依赖中间件、内存中维护映射表、组件的分散调用的问题

### 方案3: url-controller

LDBusMediator，通过组件实现公共协议的接口，来对提供服务

### 方案4: target-Action

利用 runtime 对外提供服务，组装封装一层 target 对象来对外提供服务


### 具体实施方式

```
工程 {
  main
  appDelegate

}
```
