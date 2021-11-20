# 进程间的通信方式

每个 APP 都由一个进程进行维护，iOS 是相对封闭的系统，每个 APP 都有对各自的沙盒（Sandbox），每个 APP 都只能读到 iPhone 上 iOS 系统为该应用程序创建的文件夹 AppData 下的内容，不能随意跨域自己的沙盒去访问别的沙盒中的内容


### URL Scheme

APP_A 通过 openURL 的方式跳转到 APP_B，并且在 URL 中带上想要的参数，需要分别在 APP_A 和 APP_B 配置 URLScheme

### KeyChain

安全的存储容器、本质上是一个 sqllite 数据库，所有的数据均经过加密存储，可以用来为不同的 app 保存敏感信息，即使 APP 删除后，KeyChain 依然存在

```
{
  KeyChainItemWrapper: identifier、accessGroup {
    kSecAttrAccount: value1,
    kSecValueData: value2
  }

}
```

### UIPasteboard

系统粘贴板

### UIDocumentInteractionController

用来实现同设备上 app 之间的共享文档，以及文档浏览、打印、发邮件、复制等功能

### local socket

通过本地端口，APP_A 开启某个端口进行 TCP bind 和 listen，APP_B 对该端口进行 TCP Connect，缺点需要另外一个应用程序保活

### AirDrop

支持不同设备的 APP 之间的文档和数据分享

### UIActivityViewController

发送数据、分享数据和操作数据

### APP Groups

利用 idenfitier 同个开发团队开发的 App 之间，包括 APP 和 Extension 之间共享一份读写空间进行数据共享
