# TCP

TCP (Transmission Control Protocol)，传输层控制协议

TCP 是面向连接的协议，在发送数据前先需要建立连接，然后再发送数据；TCP 会按照特定顺序重新排列数据包，相对传输速度较慢，在发送任何用户数据之前，TCP 需要三次握手建立连接

### 头部结构

20 bytes  

| Name | Size |
| :--- | :--- |
| Source Port（源端口号） | 2 byte |
|Destination Port（目的端口号） | 2 byte |
| Sequence Number (序列号) | 4 byte |
| Acknowledge Number (if ACK set) | 4 byte |
| Data offset | 4 bit |
| Reserved |  3 bit |
| NS、CWR 、ECE 、URG、ACK、PSH、RST、SYN、FIN | 9 bit |
| Window Size | 2 byte |
| CheckSum | 2 byte |
| Urgent Pointer (if URG set) | 2 byte |
| Options (if data offset > 5, Padded at the end with "0" bytes if necessary) | rest |



### 三次握手

需要确认双方都能够通信；你能收到消息吗？我可以，你可以接收到我的消息吗？我也可以

Server Receive Check Passsed

- Client 发送 SYN 信号，Sequence Number = x；然后处于 SYN_SENT 状态，Server 收到后进入 SYN_RCVD

Client Receive And Send Check Passed

- Server 发送 ACK + SYN 信号，Acknowledge Number = x+1，Sequence Number = y; Client 收到后进入 ESTABLISHED 状态

Server Receive Check Passsed

- Client 发送 ACK 信号，Acknowledge Number = y+1，Server 收到后进入 ESTABLISHED

可不可以只设置两次握手？不可以，首先丢失最后一步，会造成如果 Client 接收能力缺失，但是却建立连接的情况


### 四次挥手

需要确认双方都已经断开连接；我准备断开连接了。好的，我也准备断开连接。好的

- Client 发送 FIN 信号，然后处于 FIN_WAIT_1 状态，Server 收到后处于 CLOSE_WAIT 状态，确认是否关闭，此时 Server 还能接收数据

- Server 发送 ACK 信号，确认已经可以关闭，Client 收到后处于 FIN_WAIT_2 状态，Server CLOSE_WAIT
- Server 发送 FIN 信号，Server 进入 LAST_ACK 状态，Client 收到后进入 TIME_WAIT 状态
- Client 发送 ACK 信号，Server 收到后处于 CLOSE 状态

为什么关闭连接时会比建立连接多出来一步，因为 Server 端被动关闭时，此时还有可能有数据没有发送成功，需要等待至可关闭状态，然后发送 FIN 信号

### 2MSL

MSL (Maximum Segment Lifetime) 报文段最大生存时间，它是任何报文段被丢弃前在网络内的最长时间

在四次挥手中，2MSL 能够确保足够长的等待时间，确保在处理下次指令时，不会受到来自上次迷路报文的干扰

- TIME_WAIT，Client 收到 Server 的 FIN 信号后，然后发送 ACK 信号，进入 TIME_WAIT 状态，TIME_WAIT 需要等待 2MSL 时间。其中一个 MSL 是 Server 端收到 ACK 信号的超时时间，另外一个 MSL 是 Server 没有收到 ACK 信号后重新发送 FIN 信号的超时时间

- CLOSE_WAIT，与 TIME_WAIT 同理，Server 端收到 Client 的 FIN 信号后，进入 CLOSE_WAIT 状态，CLOSE_WAIT 需要等待 2MSL 时间。其中一个 MSL 是 Client 端收到 ACK 信号的超时时间，另外一个 MSL 是 Client 没有收到 ACK 信号后重新发送 FIN 信号的超时时间

### 差错校验

### 拥塞控制

#### 慢启动、拥塞避免

TCP 发送端会维护一个拥塞窗口 cwnd，拥塞窗口初始为 1 个报文段，每次经过一次 RTT (数据完全发送到确认时间)，窗口大小会翻倍，指数型增长。当 cwnd 增长到阈值 ssthresh 之后，每经过一个 RTT 就把拥塞窗口加一，线性增长。当检测到拥塞后，cwnd 重置为 1 个报文段，且 ssthresh 变成拥塞时 cwnd 的一半，重新走慢启动和拥塞避免

#### 快重传、快恢复




### 失败重传
