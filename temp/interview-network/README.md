---
id: interview-network

title: Interview Network
---

# HTTP

## HTTP 版本

### HTTP/1.1

- 持久连接。在 HTTP/1.0 中，每次请求都需要重新建立 TCP 连接。HTTP/1.1 默认开启了持久连接（Connection: keep-alive），允许在同一个 TCP 连接上发送多个请并接收响应，减少了建立和断开连接的开销。
- 管道化传输。允许客户端在一个 TCP 连接上连续发送请求，而不必等待响应返回。但是服务端仍然按照请求的顺序返回响应。如果某个请求的响应很慢，可能会导致队头阻塞问题。
- 新增 HTTP 方法。新增 PUT、DELETE、HEAD、OPTIONS、TRACE 等方法。
- 新增状态码。新增 101 Switching Protocols、206 Partial Content 等状态码。
- Host 头部。允许在同一个 IP 地址上配置多个虚拟主机，通过 Host 头部来区分请求的目标主机。
- 缓存控制。新增了 Cache-Control、ETag 缓存相关的头部字段，提供了更细粒度的缓存控制。
- 新增了 Range 头部以及 206 Partial Content 状态码，允许只请求资源的一部分，避免浪费带宽。

缺点：

- 队头阻塞。没有真正解决队头阻塞问题。当一个 TCP 连接上某个请求的响应很慢时，会阻塞后续请求的处理。
- 报文头部冗余。每次请求都需要携带大量的头部信息，浪费带宽。
- 安全性。明文传输，容易被窃取、篡改。

### HTTP/2.0

- 二进制帧。HTTP/2.0 将报文以二进制的形式编码，并拆分为多个二进制帧。不同报文的帧之间可以乱序发送，接收端根据帧头部的标识符将帧重新组装成完整的报文。
- 多路复用。允许在单个 TCP 连接上并行传输多个请求和响应。
- 头部压缩。使用 HPACK 算法对头部进行压缩。在客户端和服务端之间维护一个相同的头部表，后续请求只需要发送头部的索引，减少了冗余的头部信息。
- 安全性。HTTP/2.0 默认使用 HTTPS。
- 服务器推送。服务器可以在客户端请求之前发送资源，这些资源可能与当前请求的资源相关，客户端可以提前接收并缓存这些资源。
- 流量控制和优先级。通过流控制机制，客户端可以控制接收数据的速率，同时可以为不同的请求设置优先级，以优化资源的加载顺序。

### HTTP/3.0

- 基于 QUIC 协议。QUIC 是基于 UDP 的传输协议，提供类似 TCP 的可靠性有序传输。
- 0-RTT 握手。QUIC 协议支持 0-RTT 连接建立，允许客户端在不需要等待服务器响应的情况下发送数据，减少了连接建立的时间。
- 改进拥塞控制与流量控制。
- 集成 TLS 加密。
- 多路复用。
- 头部压缩。

## HTTPS

### TLS/SSL

HTTPS 实际上是在 HTTP 和 TCP 之间加入了一个安全层，这个安全层就是 TLS/SSL 协议。TLS/SSL 的功能实现主要依赖于三类基本算法：散列函数（Hash）、对称加密、非对称加密。

### HTTPS 通信过程

1. 客户端向服务端发起 HTTPS 请求，连接到服务端的 443 端口。请求中包含使用的协议版本、支持的加密算法等，并生成一个随机数发送给服务端。
2. 服务端收到请求后，确认双方使用的加密算法，返回服务端的数字证书，并生成一个随机数发送给客户端。
3. 客户端收到服务端的数字证书后，首先验证证书的有效性。然后再次生成一个随机数，使用数字证书中的公钥对其加密，发送给服务端。这个过程还会对之前的所有信息进行摘要计算，生成一个信息摘要，提供给服务端验证。
4. 服务端使用私钥解密客户端发送的随机数。此时，双方都有了三个随机数，可以使用这三个随机数以及之前约定的加密算法生成对称密钥。后续的通信双方都使用这个对称密钥进行加解密。

### HTTPS 协议如何保证安全性

因为 HTTP 协议是明文传输的，明文数据会经过中间代理服务端、路由器、WIFI 热点、通信服务运营商等多个物理节点，数据在传输过程中可能别窃取、篡改。所以需要对传输的数据进行加密。

对称加密：通信双方使用相同的密钥进行加解密，加解密速度快，但是密钥传输过程不安全。

非对称加密：通信双方使用不同的密钥进行加解密。用私钥加密的信息只能用公钥解密，用公钥加密的信息只能用私钥解密。非对称加密安全性更高，但加解密速度很慢，并且也无法保证公钥传输的安全性。

为了兼顾安全性和效率，可以结合两种加密方式，服务端将公钥明文发送给客户端，客户端生成对称加密的密钥，使用公钥加密后发送给服务端，服务端使用私钥解密得到对称加密的密钥，后续通信双方使用对称加密的密钥进行加解密。

上面这种方式的漏洞在于，如果中间人截取到服务端发送给客户端的公钥，然后伪造一个公钥发送给客户端，客户端使用伪造的公钥加密信息发送给服务端，中间人可以使用自己的私钥解密信息，然后再使用服务端的公钥加密信息发送给服务端。这样中间人就获取了双方通信的对称密钥。这个漏洞的根本在于客户端无法保证获取到的公钥的真实性。

因此，需要可信赖的第三方证书颁发机构（CA，Certificate Authority）来签发数字证书。数字证书中包含了证书持有者信息、公钥信息等。服务端将数字证书发送给客户端，客户端能够从数字证书中获取到服务端的公钥。

数字证书由明文数据和数字签名组成。数字证书传输的安全性是通过数字签名的机制来保证的。CA 机构对证书明文数据进行哈希计算，得到证书信息摘要。然后 CA 机构使用自己的私钥对证书信息摘要进行加密，得到数字签名。

由于 CA 是客户端信任的机构，因此客户端保存了 CA 机构的公钥。客户端收到数字证书后，使用 CA 机构的公钥解密数字签名，得到证书信息摘要。然后客户端用证书里指明的哈希算法对证书明文数据进行哈希计算，与解密得到的信息摘要进行比对。如果两者一致，表明数字证书没有被篡改。

### 中间人攻击可以篡改或替换数字证书吗

假设中间人篡改了证书的明文数据，由于他没有 CA 机构的私钥，因此无法生成正确的数字签名。客户端使用 CA 机构的公钥解密数字签名时，会得到错误的信息摘要，与自己计算的信息摘要不一致。

假设中间人也获得了同一家 CA 机构颁发的数字证书，并且在进行中间人攻击时用自己的证书替换了服务端的证书。客户端收到服务端的数字证书后，会发现数字证书上的网站信息与自己实际访问的网站信息不一致。

### 为什么生成数字签名时要先进行哈希计算

非对称加密通常比哈希算法要慢得多，因为它需要进行复杂的数学运算。通过先对数据进行哈希处理，可以生成一个相对较小的摘要，然后对这个摘要进行非对称加密，从而提高整个签名过程的效率。

### 证书链

- 根证书：数字证书链的起点是根证书，这是一个自签名的证书，通常由 CA 自己签发。根证书是信任的基石，因为它是不需要其他证书来验证的。操作系统、浏览器本身会预装一些信任的 CA 机构的根证书。
- 中间证书：在根证书和最终用户证书之间，可能存在一个或多个中间证书。这些中间证书由根证书签发，用于减少根 CA 的负担，同时提供更细粒度的管理和撤销机制。
- 最终用户证书：这是数字证书链的终点，通常是为特定的个人、组织或服务器签发的证书。这个证书包含了公钥和证书持有者的身份信息，由中间 CA 或根 CA 签发。
- 证书签名：每个证书都使用前一个证书中的公钥进行签名，直到根证书。这种签名机制确保了证书链的完整性和可信性。

### 每次 HTTPS 请求都需要重新建立 TLS/SSL 连接吗

服务端会为每个客户端维护一个 Session ID，在 TLS/SSL 握手阶段传递给客户端。客户端生成好密钥传给服务器后，服务器会把该密钥存到相应的上下文中。之后的请求，客户端只需要带上 Session ID，服务端就可以找到相应的密钥。

### HTTPS 的特点

优点：

- 加密传输，防止数据被窃取、篡改，安全性。
- 大幅增加了中间人攻击的成本。

缺点：

- 加密、解密过程增加了计算量，更加耗费资源。
- 协议握手阶段更加耗时。
- 服务端资源占用更多，需要投入更多的硬件资源。
- 需要购买数字证书，增加了成本。
- 数字证书需要绑定 IP，因此无法在一个 IP 上绑定多个域名。

## HTTP 状态码

- 1xx：信息性状态码（Informational），表示请求已被接受，正在处理。
- 2xx：成功状态码（Success），表示请求正常处理完毕。
- 3xx：重定向状态码（Redirection），表示需要进行附加操作以完成请求。
- 4xx：客户端错误状态码（Client Error），表示服务器无法处理请求。
- 5xx：服务器错误状态码（Server Error），表示服务器处理请求出错。

200 OK：表示客户端的请求已被服务端正常处理。

204 No Content：表示客户端的请求已被服务端正常处理，但没有返回的内容，响应报文中不包含实体的主体部分。一般在只需要客户端往服务端发送信息，而不需要服务端返回信息的情况下使用。

206 Partial Content：表示客户端进行了范围请求，服务端成功执行了这部分的 GET 请求。响应报文中包含由 Content-Range 指定范围的实体内容。

301 Moved Permanently：永久重定向。表示请求的资源已被分配了新的 URI，以后应使用资源现在所指的 URI。新的 URI 在响应报文的 Location 字段中返回。例如浏览器书签、搜索引擎等在得到这个状态码后会自动更新对应的数据。

302 Found：临时重定向。表示请求的资源被分配到了新的 URI，希望本次访问使用新的 URI。新的 URI 在响应报文的 Location 字段中返回。

303 See Other：表示请求的资源存在另一个 URI，客户端应使用 GET 方法定向获取请求的资源。新的 URI 在响应报文的 Location 字段中返回。通常作为 PUT 或 POST 操作的返回结果，表示重定向链接指向的不是新上传的资源，而是另外一个页面，例如消息确认页面或上传进度页面等。

虽然在标准中，当 301、302 状态码返回时是禁止将请求方法改为 GET 的。但是当 301、302、303 状态码返回时，几乎所有浏览器都会把请求方法改为 GET，并删除请求报文主体，之后请求会自动再次发送。

304 Not Modified：浏览器缓存相关。表示客户端发送附带条件的请求时，服务端允许请求访问资源，但未满足条件的情况。304 状态码返回时，响应报文中不包含实体的主体部分。304 状态码与重定向无关。

# DNS

## DNS 解析过程

1. 缓存：浏览器缓存、操作系统缓存、路由器缓存、ISP DNS 缓存等。
2. 本地 DNS 服务器：主机向本地 DNS 服务器发起域名解析请求，一般采用递归查询。首先本地 DNS 服务器会查询自己的缓存，如果没有则进行下一步迭代查询。
3. 根域名服务器（`.`）：本地 DNS 服务器向根域名服务器发起请求，根域名服务器返回顶级域名服务器的地址。
4. 顶级域名服务器（`.com` ...）：本地 DNS 服务器向顶级域名服务器发起请求，顶级域名服务器查询自己的缓存，如果有记录则返回结果；否则返回下一级权威域名服务器的地址。
5. 权威域名服务器（`.baidu` ...）：本地 DNS 服务器向权威域名服务器发起请求，权威域名服务器返回查询结果。

## 递归查询与迭代查询

- 递归查询：客户端向 DNS 服务器发起请求，DNS 服务器代为向下一级 DNS 服务器发起请求，直到找到查询结果。客户端只需要发送一次请求。
- 迭代查询：客户端向 DNS 服务器发起请求，DNS 服务器返回下一级 DNS 服务器的地址，客户端再向下一级 DNS 服务器发起请求。客户端需要发送多次请求。

一般情况下，向本地 DNS 服务器发起请求时采用递归查询，本地 DNS 服务器向其他 DNS 服务器发起请求时采用迭代查询。

# 网络模型

## OSI 七层模型

- 应用层：为计算机用户提供应用接口，也为用户直接提供各种网络服务。常见的应用层协议有：HTTP、HTTPS、FTP、SMTP、POP3 等。
- 表示层：提供各种用于应用层数据的编码和转换功能，确保一个系统的应用层发送的数据能被另一个系统的应用层识别。数据压缩、加密也是表示层可以提供的转换功能。
- 会话层：负责建立、管理、终止表示层实体之间的通信会话。该层的通信由不同设备中的应用程序之间的服务请求和响应组成。
- 传输层：建立端到端的链接，为上层协议提供端到端的数据传输服务，包括处理差错控制、流量控制等问题。传输层向上层屏蔽了下层数据通信的细节，为上层用户暴露了在两个传输实体之间的一条主机到主机的、可由用户控制和设定的、可靠的数据通路。常用的传输层协议包括：TCP、UDP 等。
- 网络层：为两台主机提供通信服务，负责数据包从源到目的地的传输和路由选择。网络层使用的主要协议是互联网协议（IP），包括 IPv4 和 IPv6。IPv4 是目前最广泛使用的版本，而 IPv6 是为解决 IPv4 地址耗尽问题而设计的。
- 数据链路层：确保物理层传输的数据无误，将比特组合成字节进而组合成帧，通过帧的方式传输数据。使用链路层地址（MAC 地址）来访问介质。提供错误检测和纠正机制，如循环冗余检查（CRC）。
- 物理层：最终信号的传输是通过物理层来实现的。通过物理介质传输比特流。常用设备有集线器、中继器、调制解调器、网线、双绞线、同轴电缆等。

特点：对等通信。通信双方的每一层都需要与对方的同一层进行通信。每一层的通信过程中使用本层自己的协议进行通信。

## TCP/IP 五层模型

TCP/IP 模型中的应用层整合了 OSI 模型中的应用层、表示层、会话层。

每一层有不同的设备进行工作：

- 物理层：中继器、集线器、网线、双绞线、同轴电缆等。
- 数据链路层：网桥、以太网交换机、网卡等。
- 网络层：路由器、三层交换机等。
- 传输层：四层交换机、四层路由器等。

# TCP/UDP

## UDP

UDP（User Datagram Protocol，用户数据报协议）是传输层协议。

特点：

- 无连接。UDP 发送数据之前不需要建立连接。
- 单播、多播、广播。UDP 支持一对一、一对多、多对多、多对一的传输方式。
- 面向报文。UDP 只是数据报文的搬运工，不会对数据报文进行任何拆分和拼接操作，而是保存报文的边界。在发送端，应用层将数据传递给 UDP，UDP 只负责给数据加上 UDP 头部，然后就传递给网络层；在接收端，网络层将数据传递给 UDP，UDP 只负责去掉 IP 头部，然后就传递给应用层。因此，应用层应该自行选择合适的报文大小。传输的时候是一个个报文独立传输的。
- 不可靠。无连接；收到什么数据就传递什么数据，不会备份数据，发送方不会关心接收方是否正确接收数据；没有拥塞控制，一直以恒定速度发送数据，即使网络条件不好也不会调整发送速率（缺点在于网络条件不好时可能会导致丢包，优点在于适用某些实时性要求较高的场景）。
- 头部开销小。UDP 头部只有 8 个字节，包含源端口号（可选）、目的端口号、数据报文长度、数据报文校验和（IPv4 可选，用于发现头部信息和数据中的错误）。

## TCP

TCP（Transmission Control Protocol，传输控制协议）是传输层协议。

特点：

- 面向连接。发送数据之前需要在通信两端建立连接。建立连接时需要进行三次握手，以便于建立可靠连接。
- 单播。每个 TCP 连接只能有两个端点，只能进行一对一的通信，不支持多播和广播。
- 面向字节流。TCP 以字节流的方式传输数据，不保留报文边界。发送端的 TCP 会将应用层传递下来的数据看成字节流，把字节流组织成大小不等的数据块，然后将数据块封装成 TCP 报文段进行传输。接收端的 TCP 根据 TCP 报文段的序号将数据还原成字节流。
- 可靠性。TCP 通过段编号、确认号来实现可靠传输。TCP 会对发送的数据进行编号，编号也保证了接收端能够按序接收；接收端会对已成功收到的数据回复一个相应的确认号；如果发送端在合理的往返时延（RTT）内没有收到确认，那么就会重新发送对应的数据。
- 拥塞控制。当网络出现拥塞时，TCP 会减少发送数据的速率和数量，以缓解网络拥塞。
- 全双工通信。TCP 允许通信双方的应用程序在任何时候都能发送数。因为连接的两端都有缓存，用来临时存放双方通信的数据。TCP 可以立即发送一个数据段，也可以缓存一段时间以便一次发送更多的数据段（最大报文段 MSS）。

## TCP 与 UDP 的区别

- TCP 是面向连接的；UDP 是无连接的。
- TCP 提供可靠传输（数据顺序和正确性），使用流量控制和拥塞控制；UDP 是不可靠传输，不使用流量控制和拥塞控制。
- TCP 只支持单播；UDP 支持单播、多播、广播。
- TCP 是面向字节流的；UDP 是面向报文的。
- TCP 头部最小 20 字节，最大 60 字节；UDP 头部只有 8 字节。
- TCP 适用于效率要求相对低，可靠性要求相对高的场景，例如文件传输、邮件传输等；UDP 适用于效率要求相对高，实时性要求相对高的场景，可靠性要求相对低的场景，例如视频会议、直播等。

## UDP 为什么不可靠

- 不保证消息交付：不确认，不重传，无超时。
- 不保证交付顺序：不设置包序号，不重排，不会发生队头阻塞。
- 不跟踪连接状态：不必建立连接，不必维护连接状态。
- 不进行拥塞控制：不内置客户端或网络反馈机制。

## TCP 重传机制

由于网络层可能出现数据丢失、重复、失序的情况，为了保证数据传输的可靠性，TCP 会重传其认为已丢失的包。TCP 在发送一个数据段以后，会启动一个定时器，若在定时器超时之前没有收到接收端的确认信息，就会进行重传。若重传达到一定次数仍未收到确认，就会放弃并发送一个复位信号。

## TCP 拥塞控制

TCP 拥塞控制主要包括四种机制：

- 慢启动
- 拥塞避免
- 快速重传
- 快速恢复

### 慢启动

- 在开始发送的时候设置一个拥塞窗口 `cwnd` 以及慢启动阈值 `ssthresh`。
- 开始的时候不要发送大量数据，而是先测试网络的拥塞程度，逐渐增加拥塞窗口的大小，直到达到慢启动阈值。
- 这个阶段，`cwnd` 的增长速度是指数级的，每经过一个 RTT 就会将 `cwnd` 的大小翻倍。

### 拥塞避免

- 当 `cwnd > ssthresh` 时，进入拥塞避免阶段。
- 在这个阶段，`cwnd` 的增长速度会减慢为线性增长，通常每经过一个 RTT 只增加一个 MSS 的量。
- 在慢启动或拥塞避免阶段，只要发送方判断网络没有出现拥塞，就会把慢启动阈值 `ssthresh` 设置为出现拥塞时的 `cwnd` 的一半，然后重置 `cwnd`，重新开始慢启动。
- 根据是否收到确认信息来判断是否出现拥塞（虽然可能是因为其他原因导致没有收到确认信息）。

### 快速重传

- 快速重传要求接收方在收到一个失序的报文段后，立即发出重复确认，使发送方尽早知道有报文段没有到达。发送方在收到三个重复确认后，就会立即重传对应的报文段，而不必等待定时器。

### 快速恢复

- 当发送方收到三个重复确认时，会将慢启动阈值 `ssthresh` 设置为当前 `cwnd` 的一半，然后将 `cwnd` 设置为 `ssthresh` 的大小，然后进入拥塞避免阶段。

## TCP 流量控制

流量控制是为了防止发送方发送速度过快，导致接收方来不及处理。TCP 使用滑动窗口机制来进行流量控制。窗口大小单位是字节，表示每次每次传输的数据大小。

- 当一个连接建立时，连接的每一端分配一个缓冲区，用于保存输入的数据，并将缓冲区的大小发送给对方。
- 接收方每次收到数据后会发送一个确认信息，确认信息中包含了接收方剩余的缓冲区大小。剩余的缓冲区大小就是接收窗口的大小。
- 如果接收方应用程序读取数据的速度能够满足数据到达的速度，接收方在每一个确认信息中发送的窗口大小为正数；否则发送的窗口大小为 0。
- 如果发送方收到的确认信息中窗口大小为 0，会停止发送数据，并启动持续定时器周期性探测接收方的窗口大小。一旦接收方的窗口大小变为正数，发送方就会继续发送数据。

## TCP 可靠性传输机制

TCP 的可靠性传输机制是基于连续 ARQ（Automatic Repeat Request，自动重传请求）机制以及滑动窗口机制实现的。

- 发送方会维护一个发送窗口，发送窗口以前的报文段都是已经发送并且确认的；发送窗口中包含的报文段是已经发送但未确认的，以及允许发送但未发送的；发送窗口以后的报文段都是缓存中还不允许发送的。
- 当发送方向接收方发送报文时，会依次发送窗口内的所有报文段，并且设置一个定时器。
- 接收方采用累计确认的机制，对于按序到达的报文段，只返回最后一个报文段的确认信息；如果接收到一个乱序报文段，会返回最后一个按序到达的报文段的重复确认信息。
- 发送方在定时器时间内收到某一个报文段的确认信息以后，会滑动窗口，将已经确认的报文段移出窗口；此时如果还有已发送未确认的报文段，则重置定时器；否则关闭定时器；如果定时器超时，则重新发送所有已发送未确认的报文段，并将定时器超时时间翻倍。
- 当发送发接收到三个冗余的确认信息时，说明该报文段以后的报文段可能丢失，会启用立即重传机制，立即发送所有已发送未确认的报文段。
- 发送窗口的大小是动态变化的，它根据接收窗口大小、拥塞窗口大小等因素动态调整。TCP 通过控制发送窗口的大小来控制报文段的发送速率。
- 接收方在其接收缓冲区中为失序的报文段预留空间，但不会立即处理该报文段，只有当丢失的报文段被重传并接收才会继续处理。

## TCP 三次握手

TCP 三次握手是建立 TCP 连接的过程，主要作用是为了确认双方的接收和发送能力是否正常，指定自己的初始化序列号为后续的可靠性传输做准备。

- 初始状态：客户端处于 `CLOSED` 状态，服务端处于 `LISTEN` 状态。
- 第一次握手：客户端向服务端发送一个同步序列编号（SYN，Synchronize Sequence Number）报文，指明客户端的初始化序列号 ISN（Initial Sequence Number），进入 `SYN_SENT` 状态。头部中的同步位 `SYN=1`，序号 `seq=x`。`SYN=1` 的报文段不能携带数据，但会消耗一个序号。
- 第二次握手：服务端收到客户端的 SYN 报文后，会回复一个同步确认（SYN-ACK）报文，确认客户端的初始序号，指明服务端的 ISN，进入 `SYN_RECEIVED` 状态。头部中的同步位 `SYN=1`，确认位 `ACK=1`，确认号 `ack=x+1`，序号 `seq=y`。
- 第三次握手：客户端收到服务端的 SYN-ACK 报文后，会回复一个确认（ACK，Acknowledgement）报文，确认服务端的初始序号，进入 `ESTABLISHED` 状态。头部中的确认位 `ACK=1`，确认号 `ack=y+1`，序号 `seq=x+1`。ACK 报文可以携带数据，若不携带数据则不消耗序号。服务端收到 ACK 报文后也进入 `ESTABLISHED` 状态。

三次握手的作用：

- 双方就绪。保证双方都能够确认对方的发送和接收能力正常。
- 初始化序列号。三次握手过程中交换的序列号用于初始化连接的序列号，防止旧的重复数据引起错误。
- 如果只用两次握手，可能存在的问题在于，如果客户端发送的第一次连接请求在网络中滞留而未收到确认，客户端会再次发送连接请求，此时第二个连接请求成功建立连接，数据传输完毕以后连接释放。之后滞留的第一个连接请求才到达服务端，服务端会误认为是客户端再次发送的连接请求，于是再次建立连接。此时客户端忽略服务端的确认，也不会再次发送数据，导致服务端一直等待客户端发送数据，浪费资源。

## TCP 四次挥手

TCP 四次挥手是断开 TCP 连接的过程，主要作用是为了保证数据的完整传输，确保双方都能正常关闭连接。

- 初始状态：客户端和服务端都处于 `ESTABLISHED` 状态。
- 第一次挥手：客户端向服务端发送一个连接释放（FIN，Finish）报文，请求断开连接，进入 `FIN_WAIT_1` 状态，停止发送数据。头部中的终止位 `FIN=1`，序号 `seq=u`。
- 第二次挥手：服务端收到客户端的 FIN 报文后，会回复一个确认（ACK）报文，进入 `CLOSE_WAIT` 状态。头部中的确认位 `ACK=1`，确认号 `ack=u+1`，序号 `seq=v`。此时 TCP 处于半关闭状态，服务端可以向客户端发送数据，但客户端不能向服务端发送数据。客户端收到 ACK 报文后进入 `FIN_WAIT_2` 状态。
- 第三次挥手：服务端数据发送完毕后，向客户端发送连接释放（FIN）报文，请求断开连接，进入 `LAST_ACK` 状态。头部中的终止位 `FIN=1`，确认位 `ACK=1`，确认号 `ack=u+1`，序号 `seq=w`。
- 第四次挥手：客户端收到服务端的 FIN 报文后，向服务端发送确认（ACK）报文，进入 `TIME_WAIT` 状态。头部中的确认位 `ACK=1`，确认号 `ack=w+1`，序号 `seq=u+1`。服务端收到 ACK 报文后进入 `CLOSED` 状态。客户端在 `TIME_WAIT` 状态等待 2MSL（最长报文段寿命）时间后，进入 `CLOSED` 状态。

四次挥手的作用：

- 因为 TCP 是全双工通信，双方都可以向对方发送数据，因此在断开连接时需要双方都发送连接释放报文。单独一方发送连接释放报文时，只是表示自己不再发送数据，但仍然可以接收数据，连接处于半关闭状态。
- 相较于建立连接时的三次握手，当客户端决定断开连接时，服务端可能还有数据需要发送，因此需要四次挥手来确保数据的完整传输。

最后一次挥手时，客户端等待一段时间以后再关闭是为了防止发送给服务端的最后一个 ACK 报文丢失，导致服务端重传 FIN 报文，此时若客户端已经不再接收数据，会导致服务端一直等待。

## TCP 粘包与拆包

TCP 是基于字节流的传输协议，并且有自己的拥塞控制、流量控制机制，TCP 传输和接收数据的速率是不完全受应用层控制的，TCP 只保证按照发送方的字节顺序将字节流按序提交给接收方应用程序。

所以实际上问题在于，如何在 TCP 协议的基础上涉及应用协议，保证接收方可以按顺序读取并解析到发送方希望发送的数据大小。

可能出现粘包的情况包括：

- 要发送的数据小于 TCP 发送缓冲区的大小，TCP 将多次写入缓冲区的数据一次发送出去。
- 接收数据端的应用层没有及时读取接收缓冲区中的数据。
- TCP 默认会启用延迟传送算法（Nagle 算法）。若数据发送过快，导致发送缓冲区积压多个数据后才一次性发送出去。

解决粘包的方案就是在发送的 TCP 数据时明确数据的边界，如使用消息长度字段；使用特殊字符标记或分隔；使用固定长度地数据包。

如果应用层数据包过大，超过了 MSS 的大小，就会被拆成多个 TCP 报文发送。

- MSS（Maximum Segment Size）：最大报文段长度，是 TCP 报文段中的数据部分的最大长度。MSS 是根据网络链路的 MTU 来确定的。MSS = MTU - IP 头部长度 - TCP 头部长度。
- MTU（Maximum Transmission Unit）：最大传输单元，是链路层数据帧中的数据部分的最大长度。不同类型的网络 MTU 也会不同，我们普遍使用的以太网的 MTU 是 1500，即最大只能传输 1500 字节的数据帧。

# WebSocket

WebSocket 是 HTML5 定义的协议，是一种全双工通信协议，属于应用层协议。它基于 TCP 传输协议，并复用 HTTP 的握手通道。浏览器和服务端之间只需要完成一次握手，就可以建立持久性连接，进行双向数据传输。

特点：

- 基于 TCP 协议。
- 全双工通信。
- 持久连接。
- 可以发送文本、二进制数据。
- 数据格式比较轻量，性能开销小。
- 没有同源限制（但是握手阶段基于 HTTP 协议，因此会受到同源策略的限制）。
- 协议标识符是 `ws`，加密协议标识符是 `wss`。
- 握手阶段使用 HTTP 协议，默认端口与 HTTP 协议一致（ws：80，wss：443），因此能通过各种 HTTP 代理服务器。

## 其他方案对比

### 短轮询

- 客户端定时向服务端发送请求，服务端立即返回响应。
- 优点：实现简单，兼容性好。
- 缺点：实时性差，频繁的请求会增加服务器压力。

### 长轮询

- 客户端向服务端发送请求，服务端保持连接，直到有数据可返回时才返回响应。客户端收到响应即再次发送请求，形成一个持续的循环。
- 优点：实时性好，减少了请求次数。
- 缺点：连接挂起也会导致服务端资源浪费。

### SSE

SSE 允许服务器通过 HTTP 持久连接向客户端异步发送数据。SSE 基于 HTTP 协议，使用 text/event-stream 内容类型，并以流的形式发送数据。

特点：

- 单向数据流：数据只能从服务器流向客户端。
- 自动重连：在连接断开时，浏览器会自动尝试重新连接，可以通过 retry 字段设置重连时间间隔。
- 简单易用：使用 EventSource 接口，易于在客户端实现。
- 兼容性：现代浏览器普遍支持 SSE，但某些老旧浏览器如 IE/Edge 不支持。
- 基于 HTTP：可以利用现有的 HTTP 基础设施，包括代理和防火墙规则

工作原理：

1. 客户端使用 EventSource 接口建立到服务器的连接。
2. 服务器通过设置 Content-Type 为 text/event-stream 来推送数据流。
3. 数据流中的每个事件由 data 字段包含，事件之间通过两个换行符\n\n 分隔。
4. 客户端通过监听 message 事件来接收数据，并可以定义回调函数来处理接收到的数据。

SSE 与 WebSocket 的区别：

- WebSocket 提供全双工通信，适合需要客户端和服务器频繁交换数据的场景。
- SSE 是单向通信，更适合数据主要从服务器流向客户端的场景。
- WebSocket 协议相对复杂，而 SSE 使用 HTTP 协议，更加简单易用。
- SSE 默认支持断线重连，而 WebSocket 需要手动实现。
- SSE 通常只用于文本数据传输，二进制数据需要编码，而 WebSocket 支持二进制数据传输。
