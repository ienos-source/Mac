# HTTP

[（建议精读）HTTP灵魂之问，巩固你的 HTTP 知识体系](https://juejin.cn/post/6844904100035821575)

HTTP（Hypertext Transfer Protocol）超文本传输协议，用于规定客户端与服务端之间的应用层传输协议。超文本不单单只是文本，还可以传输图片、音频、视频等

### 设计目的

提供一种发布和接收 HTML 页面接收的方法

### 特点

无状态，Cookie / Session 同一个客户端每次请求之间是没有对应关系的

无连接，HTTP 的持久连接；非持久链接，每次发起请求，响应后都关闭；持久链接，在开启后延迟一段时间后才断开

应用层: OSI 参考模型的最高层，是用户与网络的接口，该层通过应用程序来完成网络用户的应用请求

### URL

统一资源定位符 URL (Uniform Resource Locator)，地址例如 `http://www.xxx.com/img/logo.png`，包含协议、IP 地址/域名

**常见协议**

| 常见协议 | 格式 | 作用 |
|:---:|:---:|:---|
| HTTP | http:// | 超文本传输协议，访问远程网络资源 |
| file | file:// | 访问本地计算机上的资源 |
| mailto | mailto: | 访问电子邮件地址 |
| FTP | ftp:// | 访问共享主机的文件资源 |

##### URI & URL

统一资源标识符（URI）可以是名称或者定位符、也可以两者皆有，URI 只能提供标识，并不能提供访问方式；当字符串中含有 http:// 那它是一个 URL，URL 能够具体告知资源如何访问

```
/// 所有的 URL 都是 URI，但并非所有的 URI 都是 URL
URI (identifier): ISBN 0-486-27557-4 {

  URN (name): urn:isbn:0-486-27557-4

  URL (locator): https://google.com

}
```


##### URL 中带中文问题

如果地址是英文/数字，原样发送，如果是空格，转换为 +，如果是中文/其他字符，则直接把字符串用 BASE64 编码，得出 %XX，XX 为该符号以 16 进制表示的 ASCII

```
NSString *urlEncode = [url stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet characterSetWithCharactersInString:url]];
```

### 端口

HTTP 的默认端口是 80，而 HTTPS 的默认端口是 443

### 请求报文组成

```
{

/// 请求方法 + 请求 URL + HTTP 协议及版本 = 请求行
    POST /chapter17/user.html HTTP/1.1

/// 请求头
    Accept: image/jpeg, application/x-ms-application, ..., */*
    Referer: http://localhost: 888/chapter17/user/register.html
    Accept-Language: zh-CN
    User-Agent: Mozilla/4.0
    Host: localhost:8088
    Content-Length: 112
    Connection: Keep-Alive
    Cache-Control: no-cache
    Cookie: JSESSIONID=123123SDJJSKD98J34J

/// 请求主体

    name=tom&password=12&realName=tomson

}
```

#### 请求方法

GET / POST / DELETE / HEAD / OPTIONS / PUT / TRACE / CONNECT

#### GET

GET 通常用来获取资源，安全、幂等、可缓存。这里的安全性指的是不引起 Server 端的任何变化，幂等指同一个请求方法执行多次和执行一次的效果完全相同，常见的场景是通过网页上的链接在浏览器上面浏览网页，请求参数和对应的值附加在 URL 后面，利用一个问号？代表 URL 的结尾与请求参数的开始，各个参数之间用 & 符号隔开，例如:

```
/index.jsp?id=100&op=bind
```

实际上，HTTP 协议并没有对 URL 进行长度限制，实际上的限制是特定浏览器及服务器端对它的限制，不适合传输大量数据

GET 请求的请求信息都在请求参数里面，易获取

GET 请求在发送过程中会产生一个 TCP 数据包，客户端会把 HTTP Header 和 data 一并发出去，服务器响应 200 返回数据

#### POST

POST 通常用来处理资源，非安全、非幂等、不可缓存，使用 POST 方法可以允许客户端给服务器提供较多的信息，POST 方法将请求封装在 HTTP 请求数据中，以 key:value 的方式出现，可以传输大量数据，POST 对传送的数据大小没有限制，也不会显示在 URL 中，POST 请求也能完成 GET 的功能

POST 请求将参数放在请求体 Body 中，对用户来说不可见

POST 请求在发送过程中会产生两个 TCP 数据包，客户端先发送 Header，服务器响应 100，然后再发送 data，服务器响应 200

#### HEAD

HEAD 类似 GET，只不过服务器接受到 HEAD 请求后只返回响应行，而不会响应内容

当我们只需要查看某个页面的状态的时候，使用 HEAD 是非常高效的，因为再传输过程中省去了页面内容

#### 请求 URL

请求对应的 URL 地址，和报文头的 Host 属性，组合起来是一个完整的 URL

#### 协议版本

协议名称及版本号

#### 请求头

有若干个属性，形式为 key: value，用于服务端获取客户端的一些附加信息

```
NSURLRequest / NSMutableURLRequest 的 setvalue: forHTTPHeaderField:
```

**常见报文头属性**

| 属性 | 解释 |
|:---:|:---|
| Accept | 告诉服务端，客户端接收什么类型的响应 |
| Referer | 表示该请求是从哪个 URL 进来的 |
| Cache-Control | 对缓存进行控制，例如缓存的过期时间或者设置不设缓存 |
| Accept-Encoding | 告诉服务端自己能接受什么编码格式，包括字符编码，压缩形式 |
| Accept-Language | 设置接受的语言 |
| Host | 指定要请求的资源所在的主机和端口 |
| User-Agent | 告诉服务器，客户端使用的操作系统、浏览器版本和名称 |



#### 请求体

将一个页面表单中的组件值通过 param1=value1&param2=value2 键值对的形式编码成一个格式化串，它承载多个请求参数的数据，不但报文头可以传输请求参数，URL 也可以通过 /chapter15/user.html?param1=value1&param2=value2 的方式传递数值

```
NSURLRequest / NSMutableURLRequest 的 HTTPBody
```

### 响应报文

与请求报文一样，由三个部分组成: 响应行、响应头、响应体

```
{

/// 报文协议及版本 状态码及状态描述
    HTTP/1.1 200 OK

/// 响应头
    Server: Apache-Coyote/1.1
    Content-Type: application/json
    Transfer-Encoding: chunked
    Date: Mon, 12 Sep 2011 12:41:24 GMT

/// 响应体
    { "password": "1234", "userName": Justin }

}

```

##### 状态码

| 状态码 | 含义 |
|:---:|:---|
| 100~199 | 消息 -- 成功接收请求，要求客户端继续提交下一次请求才能完成整个处理过程 |
| 200~299 | 成功 -- 成功接收请求，并已经完成整个处理过程 |
| 300~399 | 重定向 -- 为完成请求，客户端需进一步细化请求。例如，请求的资源已经移动一个新的地址 |
| 400~499 | 请求错误 -- 客户端的请求有错误 |
| 500~599 | 服务器错误 -- 服务器端出现问题 |

常见状态码

| 状态码 | 含义 |
|:---:|:---|
| 200 | OK 表示成功 |
| 303 | 重定向，重定向到其他页面 |
| 304 | 资源并未修改，可以直接使用本地的缓存 |
| 404 | 找不到页面（页面被删除或其他）|
| 500 | 服务器错误 |

##### 响应报文属性

| 属性 Key | 含义 |
|:---:|:---|
| Cache-Control | 响应输出到客户端后，服务器通过该属性告诉客户端该怎么控制响应内容的缓存 |
| ETag | 表示你请求资源的版本，如果该资源发生变化，那么这个属性也会跟着变化 |
| Location | 在重定向中或者创建资源时使用 |
| Set-Cookie | 服务端可以设置客户端的 Cookie |

### HTTP 的历史演化

HTTP / 1.1 之前
- 不支持持久连接，一旦服务器对客户端发出响应就立即断开 TCP 连接
- 无请求头和响应头
- 客户端的前后请求是同步的，下一个请求必须等上一个请求从服务器拿到响应后才能发出，有点类似于多线程的同步机制

HTTP / 1.1
- 增加请求头和响应头
- 支持持久连接，客户端通过请求头指定 Connection 为 keep-alive 告知服务器不要在完成响应后立即释放链接。HTTP 基于 TCP 连接，在 HTTP 1.1 中一次 TCP 连接可以处理多次 HTTP 请求
- 客户端不同请求之间是异步的，下一个请求不必等到上一个请求回来后再发出，而是可以持久发出请求，有点类似于多线程的异步处理

HTTP 2.0
- 只用于 HTTPS 地址

HTTP 3.0

### 持久连接

请求头部字段

```
  /// 设置持久连接
  Connection: keep-alive

  /// 持久连接持续时长
  time: 20

  /// 这个连接对多可以发生多少次 HTTP 请求响应对
  max: 10
```

如何判断一个请求是否结束？

```
/// 1. 客户端接收的最大长度
Content-length: 1024

/// 2.
chunked, 最后会有一个空的 chunked
```

如果连接被频繁地启动然后断开，就会花费很多资源在 TCP 三次握手和四次挥手上，效率低下

### 建立过程

```
  1. TCP 三次握手: SYN, SYN/ACK, ACK
  2. HTTP 请求报文、http 响应报文
  3. TCP 四次挥手: FIN, ACK, FIN/ACK, ACK
```

### 无状态 - Cookie

HTTP 是一个无状态协议，指的是每次请求相对独立，没有记忆功能；例如用户登录了一个网页，然后关闭浏览器，当用户再次打开该网页的时候，还是需要重新登录。当然现在在浏览器中，我们再次打开浏览器，下次登录不再需要重新输入用户名和密码，是因为 Cookie 发生了作用，能够让浏览器有记忆功能。

Cookie 一般存在请求头中，示例: Cookie: JSSESSIONID=xxxxxxxxx。一般过程如下

- 客户端发起请求
- 服务端在响应报文中，返回 Set-Cookie
- 客户端在后面发起请求时都会携带 Cookie，放在请求头里面


### 抓包原理

中间人攻击

```
Client -> Server

Client -> 中间人 -> Server
```

### HTTPS

HTTPS 是以安全为目的的 HTTP 通道，即 HTTP 下加入 SSL 层进行加密

- SSL (Secure Sockets Layer) 安全套接字层
- TLS (Transfer Layer Security) 传输层安全协议，标准化 SSL 后的命名，可视为与 SSL 同一个东西的不同阶段

HTTP 协议以明文的方式发送内容，不提供任何方式的数据加密，如果攻击者截取了 Web 浏览器和网站服务器之间的传输报文，就可以直接读取其中的信息，因此 HTTP 协议不适合传输一些敏感信息，例如信用卡号、密码等等。

SSL 依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。HTTPS 协议需要到 CA 申请证书，一般免费证书很少，需要交费；使用的端口是 443

##### 建立过程

- Client 告知 Server 端 TLS 版本号、支持的加密算法、random number C

- Server 确认支持的加密算法，Server 保存着私钥，random number S, server 证书（携带公钥）

- Client 通过证书信任链以及数字签名验证证书

- Client 通过服务器公钥来生成一个前主密钥(Pre-Master Secret, PMS), 并通过该前预主密钥和 random number c 和 random number S 组装成会话密钥

- Client 通过 Server 的公钥对预主密钥进行加密发送给 Server

- Server 通过私钥解密得到预主密钥

- Server 通过 random number c 和 random number s 和与预主密钥组装会话密钥

会话密钥 = random S + random C + 预主秘钥，建立过程中使用非对称加密，非对称加密相对耗时，所以在正式通信过程中使用对称加密

#### WWDC 16

在 WWDC 16 苹果宣布之后所有应用程序必须启用 App Transport Security 安全功能，App Transport Security (ATS) 是苹果在 iOS 9 中引入的一项隐私保护功能，屏蔽铭文 HTTP 资源加载，连接必须进入更安全的 HTTPS


### CRLF


### POST body 使用 form-urlencoded 和 multipart/form-data

1. application/x-www-form-urlencoded:

窗体数据被编码为名称/键值对，这是标准且默认的编码格式。当 action 为 get 的时候，客户端把 form 数据转换成一个字符串 append 到 url 后面，用 ? 分割。当 action 为 POST 的时候，浏览器把 form 数据封装到 http body 中，然后发送给 server

2. multipart/form-data:

multipart 表示的意思是单个消息头包含多个消息体的解决方案，multipart 媒体类型对发送非文本的各媒体类型是有用的。一般多用于文件上传。
multipart/form-data 只是 multipart 的一种。目前常用的有以下这些类型
注: 任何一种执行时无法识别的 multipart 子类型都被视为子类型 "mixed"
