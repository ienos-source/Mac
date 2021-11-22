# SandBox

[Apple.FileSystem](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)

在 Apple 文件系统中（APFS），每个进程拥有对应的文件系统进行数据持久化，进程之间不相互共享，用户无法直接获得文件系统中的内容

---

\>> 沙盒路径目录如下
- Document - 用户存储用户生成的文件
- Library - 这个目录下有两个子目录，存储不向用户公布的文件
  - Preferences - 包含应用程序的偏好设置文件，通常使用 NSUserDefault
  - Caches - 保存应用程序再次启动过程中需要的信息
- Tmp - 这个目录用于存放临时文件，关闭应用程序将会清除

### 二、相关代码
- 获取 Sandbox 路径
`NSString *homeDir = NSHomeDirectory()`

- 获取 Documents 目录
`NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);`
`NSString *docuDir = [paths firstObject];`

- 获取 Library 目录
`NSArray *paths = NSSearchPathForDiectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES);`
`NSString *libDir = [paths lastObject];`

- 获取 Cache 目录
`NSArray *paths = NSSearchPathForDiectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);`
`NSString *cachesDir = [paths firstObject];`

- 获取 tmp 目录
`NSString * tmpDir = NSTemporaryDirectory();`

-- **其他路径**
- 模拟器的位置
`/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs`

- 文档安装位置
`/Applications/Xcode.app/Contents/Developer/Documentation/DocSets/`

- 插件保存路径
`~/Library/ApplicationSupport/Developer/Shared/Xcode/Plug-ins`

- 自定义代码段的保存路径
`~/Library/Developer/Xcode/UserData/CodeSnippets`
如果找不到 CodeSinppets 文件夹，可以自己新建一个 CodeSnippets 文件夹

- 证书路径
`~/Library/MobileDevice/Provisioning Profile`
