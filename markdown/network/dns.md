# DNS

### IP & 域名

IP 地址: 191.168.1.1、域名: www.xxx.com

一般来讲一个域名对应一个 IP 地址，由于 IP 地址不好记录，所以给 IP 地址起了一个别名，就是域名。互联网访问的方式，输入域名，域名解析服务器（DNS）解析成 IP 地址，访问 IP 地址

DNS 是应用层协议，事实上他是为其他应用层协议工作的，包括不限 HTTP 和 SMTP 以及 FTP，用于将用户提供的主机名解析为 ip 地址

用户主机的 DNS 客户端接收到后，向网络中发送一个 DNS 查询报文，所有 DNS 请求和回答报文使用的 UDP 数据报经过端口 53 发送，经过若干 ms 到若干 s 的延时后，用户主机上的 DNS 客户端接收到一个提供所希望映射的 DNS 回答报文。这个查询结果则被传递到调用 DNS 的应用程序


DNS 主要基于 UDP 运输层协议，尽最大能力交付的不可靠数据连接，一次 UDP 名字服务器交换可以短到两个包: 一个查询包、一个响应包。一个 TCP 交换则至少包含 9 个包: 三次握手初始化 TCP 会话、一个查询包、一个响应包以及四次分手的包交换；考虑到效率原因，TCP 连接的开销较大，故采用 UDP 作为 DNS 的传输层协议

DNS 使用的是分布式集群的工作方式，而不是集中式的设计方式。集中式设计会有单点故障、通信容量、远距离的是时间延迟、维护开销大

DNS 服务器一般分为三种: 根 DNS 服务器、顶级 DNS 服务器、权威 DNS 服务器


### 工作原理

DNS 客户端需要查询程序中使用的名称时，它会查询本地 DNS 服务器来解析该名称。客户端发送的每条查询消息都包含 3 条信息，以指定服务器应回答的问题

- 指定的 DNS 域名，表示为完全合格的域名(FQDN)

- 指定查询类型，它可根据类型指定资源记录，或作为查询操作的专门类型

- DNS 域名的指定类别

```

|- 域名解析
    |- 本地 host 文件
    |- 本地 DNS 解析器缓存
    |- 本地 DNS 服务器（TCP/IP 参数中设置的首选）
    |- 本地 DNS 服务器缓存/区域文件
    |- 本地 DNS 服务器（非转发模式），发送至 13 台根 DNS 服务器，然后依次寻找下级域名直到找到主机，最后把结果返回给本地 DNS 服务器，由此 DNS 服务器再返回给客户端
    |- 本地 DNS 服务器（转发模式），发送至上级 DNS 服务器，如果找不到再转发到上上级，最后把结果返回给本地 DNS 服务器，由此 DNS 服务器再返回给客户端

```

### DNS 劫持

- 设置可靠的本地 DNS 服务器

- 由于 DNS 采用 UDP 数据报，容易被劫持，所以可以直接使用 HTTP 协议透传到 HTTPDNS 服务器上 