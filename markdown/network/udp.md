# UDP

面向无连接传输协议，发送数据前不需要建立连接，没有可靠性

支持一对一、一对多、多对一、多对多；也就是支持单播、多播、广播的功能

面向报文传输，发送方 UDP 对应用程序交下来的报文，在添加首部后就向下交付 IP 层。UDP 对应用层交下来的报文，既不合并，也不拆分，而是保留这些报文的边界，因此应用程序必须选择合适大小的报文

UDP 首部开销 8 个字节

```
Source port(2byte) | Destination port(2byte) |
Length 整个数据报文的长度(2byte) | checksum 整个数据报的检验和(2byte) |
```

UDP 不需要维持复杂的连接状态表

对实时性要求很高的场景使用 UDP，比如游戏，媒体通信，实时直播，即使出现传输错误也可以容忍

通信不需要建立连接，收到什么数据就传递什么数据，并且也不会备份数据，发送数据也不会关心对方是否已经正确接收到数据，UDP 因为没有拥塞控制，会以恒定的速度发送数据。所以在网络不好的情况下可能会导致丢包