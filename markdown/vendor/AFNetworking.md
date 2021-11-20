# AFNetworking

```
|- 通信核心类，网络相关 API 的一层封装
    |- AFURLSessionManager // 继承并实现其他协议
    |- AFHTTPSesssionManager // HTTP

|- 序列化类

    |- AFURLRequestSerialization // 将传入的参数构造成 NSURLRequest
        |- AFHTTPRequestSerializer
        |- AFJSONRequestSerializer
        |- AFPropertyListRequestSerializer

    |- AFURLResponseSerialization // 将系统的 NSURLResponse 处理成我们需要的 responseObject
        |- AFHTTPResponseSerializer
        |- AFJSONResponseSerializer
        |- AFXMLParserResponseSerializer
        |- AFXMLDocumentResponseSerializer
        |- AFPropertyListResponseSerializer
        |- AFImageResponseSerializer
        |- AFCompoundResponseSerializer

|- 辅助类
    |- AFSecurityPolicy // 用户处理网络连接安全策略 HTTPS
    |- AFNetworkReachabilityManager // 网络状态监控

|- UIKit // 通过 Category 提供一下 UIKit 的便利方法
```

### AFURLSessionManager

- 创建和管理 NSURLSession / NSURLSessionTask

- 实现 NSURLSessionDelegate 等协议的代理方法

- 引入 AFSecurityPolicy 保证请求安全

- 引入 AFNetworkReachabilityManager 监控网络状态

### 大致流程

通过 AFURLRequestSerialization 生成 NSURLRequest

通过 NSURLSession 和 NSURLRequest 生成一个 NSURLSessionTask 开启一个网络请求

NSURLSessionTask.taskIdentifier 作为 Key, AFURLSessionManagerTaskDelegate 作为 Value 保存在一个字典中



### 代码架构

NSURLSession 有两个初始化方式，第一个是使用系统共享的 session，另一中是自己创建的 session。AFN 选择的是自己创建的 session，所以每个请求都会创建一个 session，复用一个 session 可以减少握手和挥手的过程，提高请求速度，Apple 允许 iOS 设备上一个域名可以有四个连接同时存在。可以在外面对 AFN 进行二次封装，将 AFHTTPSesssionManager 复用为单例对象

### 缓存

提供三种缓存配置
- 默认会话模式，默认添加内存缓存和磁盘缓存
- 瞬时会话模式，只添加内存缓存，不实现磁盘缓存
- 后台会话模式，内存和磁盘都不进行缓存

缓存策略

- NSURLRequestUseProtocolCachePolicy：对特定的URL请求使用网络协议中实现的缓存逻辑，这是默认的策略
- NSURLRequestReloadIgnoringLocalCacheData：数据需要从原始地址加载，不使用现有缓存。NSURLRequestReloadIgnoringLocalAndRemoteCacheData：不仅忽略本地缓存，同时也忽略代理服务器或其他中间介质目前已有的、协议允许的缓存
- NSURLRequestReturnCacheDataElseLoad：无论缓存是否过期，先使用本地缓存数据。如果缓存中没有请求所对应的数据，那么从原始地址加载数据
- NSURLRequestReturnCacheDataDontLoad：无论缓存是否过期，先使用本地缓存数据。如果缓存中没有请求所对应的数据，那么放弃从原始地址加载数据，请求视为失败（即：“离线”模式）
- NSURLRequestReloadRevalidatingCacheData：从原始地址确认缓存数据的合法性后，缓存数据就可以使用，否则从原始地址加载

### 内存缓存

### AFSecurityPolicy

```
// MODE

    // 不使用固定的证书验证服务器，直接从客户端系统中的受信任颁发机构 CA 列表中去验证
    - AFSSLPinningModeNone

    // 会对服务器返回的证书 PublicKey 进行验证
    - AFSSLPinningModePublicKey

    // 代表会对服务器返回的证书同本地证书全部校验
    - AFSSLPinningModeCertificate

```

当通过 HTTPS 协议收到服务端的 challenge 时，比如需要验证证书，会执行代理方法
```
- (void)URLSession:(NSURLSession *)session didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler
```

在这个代理方法中，用来自定义如何应对服务器端不同类型的认证挑战

- 判断接收服务器挑战的方法是否是信任证书
-


### 如何开启常驻子线程，为什么需要常驻子线程

### 历史版本

- 1.0 基于 NSURLConnection 的封装
- 2.0 两套实现，分别基于 NSURLConnection 和 NSURLSession，是专享 NSURLSession 的过渡版本
- 3.0 基于 NSURLSession 的封装
