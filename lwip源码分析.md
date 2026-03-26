# lwip源码分析（原始版本）

## 1. lwip简介

lwip是[TCP/IP协议栈](https://zhida.zhihu.com/search?content_id=231988713&content_type=Article&match_order=1&q=TCP%2FIP协议栈&zhida_source=entity)的轻量化实现，是瑞典计算机科学院([SICS](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=SICS&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJTSUNTIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM5MzI0NzE1LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ._jr2fzzi-mTppb6QKvK2JUSpbEEJv_1DEKnJMC_wVDY&zhida_source=entity))的 [Adam Dunkels](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=Adam+Dunkels&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJBZGFtIER1bmtlbHMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyMzkzMjQ3MTUsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.VgZGMsn3WkPz6d2mSWY6ZKWXyp99fW8fBdRRh9Wh120&zhida_source=entity) 开发的一个小型开源的 [TCP/IP 协议栈](占用的存储空间只有40kb。lwip运行时占用的内存空间也只有几十kb。通常用在资源紧张的嵌入式平台上，比如[MCU](https://zhida.zhihu.com/search?content_id=231988713&content_type=Article&match_order=1&q=MCU&zhida_source=entity)。lwip支持移植到裸机中，也支持移植到操作系统之上。

### 1.LWIP特性

LWIP 具有的主要特性：

（1）支持 [ARP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=ARP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJBUlAg5Y2P6K6uIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM5MzI0NzE1LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.eC3yi2BpsaHcDzdE64aMo3-hdYxG6MyXHsKXYA6ucJ4&zhida_source=entity)。 　　

（2）支持 [ICMP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=ICMP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJJQ01QIOWNj-iuriIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjIzOTMyNDcxNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.EXQTtTtj3Hsmn8m-tkNfMkNdThUltJ_co4e4cxjqXB4&zhida_source=entity)（控制报文协议），用于网络的调试与维护。 　　

（3）支持 [IGMP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=IGMP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJJR01QIOWNj-iuriIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjIzOTMyNDcxNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.pflMfC2Gd1fnNAR_VZOo7HBUBJs_cesy_GSRK7T9JQQ&zhida_source=entity)（互联网组管理协议），可以实现多播数据的接收。 　　

（4）支持 [UDP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=UDP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJVRFAg5Y2P6K6uIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM5MzI0NzE1LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.UG4dR6B3pgPw-caly4o1nXpvowWHpMi2bQJcOy1lm4g&zhida_source=entity)(用户数据报协议)。 　　

（5）支持 TCP 协议(传输控制协议)，包括阻塞控制、 RTT 估算、快速恢复和快速转发。 　　

（6）支持 PPP 协议（点对点通信协议） ，支持 PPPoE。 　　

（7）支持 [DNS](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=DNS&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJETlMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyMzkzMjQ3MTUsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.97dRGxmBHIXnL0KQeIlFymGTTRYgdQsvELdIXmrExsA&zhida_source=entity)（域名解析）。

（8）支持 [DHCP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=DHCP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJESENQIOWNj-iuriIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjIzOTMyNDcxNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.ikGZtahwE-5_YSbnE_M9PocaTJmaTCD-3Gwq0_CjcMg&zhida_source=entity)，动态分配 IP 地址。 　　

（9）支持 IP 协议，包括 IPv4、 IPv6 协议，支持 IP 分片与重装功能，多网络接口下的数据包转发。 　　

（10）支持 [SNMP 协议](https://zhida.zhihu.com/search?content_id=239324715&content_type=Article&match_order=1&q=SNMP+协议&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzM4MjA4MjAsInEiOiJTTk1QIOWNj-iuriIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjIzOTMyNDcxNSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.T-FiBuR1BRMR-EWL-EKKwi-j68wjO28yzI_PVNplj7M&zhida_source=entity)（简单网络管理协议）。 　　

（11）支持 AUTOIP，自动 IP 地址配置。 　　

（12）提供专门的内部回调接口(Raw API)，用于提高应用程序性能。 　　

（13）提供可选择的 Socket API、 NETCONN API (在多线程情况下使用) 。

### 2. 应用程序API

==**API（应用程序编程接口）**是一套清晰定义的“规则”和“工具”，让不同的软件组成部分能够相互沟通和协作。==

LWIP提供三种API：

（1）==Raw/native API==

==RAW API把协议栈和应用程序放到一个进程里边==，该接口==基于函数回调技术==，为了接收数据，==应用程序会向协议栈注册一个回调函数==。该回调函数与特定的连接相关联，==当该关联的连接到达一个信息包，该回调函数就会被协议栈调用==。

TCP/IP协议栈原生是基于事件驱动的，所以`raw api`==只是向用户提供了注册回调的接口==，这也是最高效的实现。回调机制虽然效率高，但是给应用程序的顺序编程带来了麻烦。将lwip移植到裸机上时，**raw api是唯一的选择，因为其他的api需要操作系统支持**。`raw api`在多线程环境下要谨慎使用，因为它并不是线程安全的。

这既有优点也有缺点。优点是既然应用程序和TCP/IP协议栈驻留在同一个进程中，那么发送和接收数据就不再进行进程切换。主要缺点是TCP/IP处理与业务处理不能并行，这个缺点需要把应用程序分为两部分来克服，一部分处理通讯，一部分处理业务。

（2）==Netconn API==

`netconn api`是为了让用户利用线程进行顺序编程，所以`netconn api`需要操作系统来给它提供线程机制。所有协议栈对数据包的处理都是在独立的线程中实现，应用线程使用`message box`和`semaphores`机制与协议栈线程进行交互。

（3）==SOCKET API==

`SOCKET API` 提供了基于`open-read-write-close`模型的UNIX标准API，==Socket，即套接字==，它的最大特点是使应用程序移植到其它系统时比较容易，但用在嵌入式系统中效率比较低，占用资源多。在嵌入式设备中一般不会使用。`socket api`可以参考posix标准接口。

## 2.lwip架构

![img](typora-img/lwip源码分析/v2-67a1e3b2ca65ed20df191ccf1c06f9f2_1440w.jpg)

### src文件夹的中内容

1. api ：文件装的是`NETCONN API` 和`SOCKET API` 源文件，==只有在操作系统中才会编译==;
2. apps：文件夹里面装的是==应用程序的源文件==，包括常见的应用程序，如 `httpd`、`mqtt`、`tftp`、`sntp`、`snmp`等;
3. core：文件夹里面是 LwIP 的==内核源文件==;
4. include：文件夹里面是 LwIP 所有模块对应的==头文件==;
5. netif：文件夹里面是==与网卡移植有关的文件==，这些文件为我们移植网卡提供了模板，我们可以直接使用。

### core文件简介

1. `altcp.c`、`altcp_alloc.c`、`altcp_tcp.c` 等文件是应用程序分层 TCP 连接 API，从 `TCP/IP 线程`使用，是一个抽象层，可以模拟应用程序的 tcp 回调 API，同时防止直接链接，没有使用;
2. `def.c`：文件定义了一些基础类函数，比如主机序和网络序的转换、字符串的查找和比较、整数转换成字符串等，这些函数会被 LwIP 内核的很多模块所调用。在 include 目录里面的 `def.h` 文件对外声明了 `def.c` 所实现的函数，同时定义了许多宏，能实现一些基础操作，比如取最大值、取最小值、计算数组长度等，这些宏同样也被内核的许多模块所调用.
3. `dns.c` :文件实现了域名解析的功能;
4. `inet_chksum.c`:文件提供了 LwIP 所需的校验和功能;
5. `init.c` :文件对 LwIP 的用户宏配置进行了检查，会将配置错误和不合理的地方，通过编译器的 `#error`和 `#warning` 功能表示出来。另外，`init.c` 定义了`lwip_init` 初始化函数，这个函数会依次对 LwIP 的各个模块进行初始化。
6. `ip.c`:文件实现了 IP 协议相关的函数;
7.  `mem.c` :文件实现了内存堆（heap）管理机制;
8. `memp.c`:文件实现了动态内存池（Dynamic）管理机制；
9. `netif.c`文件实现了网卡的操作，比如注册/删除网卡、使能/禁能网卡、设置网卡 IP 地址等等；
10. `pbuf.c`:文件实现了 LwIP 对网络数据包的各种操作；
11. `raw.c`:文件实现了一个传输层协议的框架；
12. `stat.c`:文件实现了 LwIP 内核的统计功能，使用户可以实时地查看 LwIP 内核对网络数据包的处理情况；
13. `sys.c`：它提供了与临界区相关的操作；
14. `tcp.c`、`tcp_in.c` 和 `tcp_out.c`:文件实现了 TCP 协议，包括对 TCP 连接的操作、对 TCP 数据包的输入输出操作和 TCP 定时器；
15. `timeouts.c`:定义了 LwIP 内核的超时处理机制；
16. `udp.c`:文件实现了 UDP 协议，包括对 UDP 连接的操作和 UDP 数据包的操作。

## 3.内存管理

lwip有自己的动态内存管理机制，用于替代`libc`中的内存管理。lwip提供两种内存管理的方式，一种是简单的内存管理(`mem`模块)，另一种是基于内存池的内存管理(`memp`模块)。

![img](typora-img/lwip源码分析/v2-a299bd393d0a290bc2d983d043cfa643_1440w.jpg)

图（1）

### 3.1. 简单内存管理

`mem`模块的内存管理是在划分给lwip使用的内存区域上进行，不会干扰到其他内存区域，**可以将这片区域看成是lwip私有的堆区**，如图(1)中的`ram`到`ram_end`区域。

#### 3.1.1. 初始化

```c
void mem_init(void)
```

调用`mem_init`函数可以初始化堆区，lwip定义了一个全局数组作为堆区，然后`ram`**指针指向了堆顶**，`ram_end`指针**指向了堆底**。初始化操作就是在堆顶放一个`struct mem`，在堆底放一个`struct mem`，这样就在两个`struct mem`中形成了一块空闲区域。如图(1)。

#### 3.1.2. 分配内存

```c
void *mem_malloc(mem_size_t size_in)
```

在分配内存时，首先lwip会在堆区中查找第一个大于`size_in`大小的空闲区域，空闲区域就是`used`字段为0的`struct mem`与其`next`指针指向位置中间的区域。通过遍历查找的方式就可以找到第一个大于`size_in`大小的空闲区域，如果这个空间区域非常大，就要将这片区域拆分。拆分的方法也很简单，就是重新创建一个`struct mem`，并将其插入链表中。如图(2),(3)。

![img](typora-img/lwip源码分析/v2-d3f1d5fe5d1d8d07a81a81fc6c732780_1440w.jpg)

图（2）

![img](typora-img/lwip源码分析/v2-becc451ffa1c0200f4d722bdc0ea612a_1440w.jpg)

图（3）

#### 3.1.3. 释放内存

```c
void mem_free(void *rmem)
```

将传入的指针上移`sizeof(struct mem)`<需要考虑内存对齐>，然后转换成`struct mem*`类型的指针，就可以访问到这个内存块对应的`struct mem`。最简单的内存释放方式就是将`used`字段赋值为0，**但是这样会造成内存碎片**。在释放内存时，lwip会合并上下相邻的空闲内存块。如图(4),(5)。

### 3.2. 基于内存池的内存管理

`memp`模块通过内存池来管理内存，当然它的底层还是全局数组。内存池由多个固定大小的内存块构成，根据内存块的大小可以给内存池分类，特定类型的内存池用于存储特定类型的数据。

#### 3.2.1. 初始化内存池

```c
void memp_init(void)
```

lwip有一个全局的`struct memp_desc *`数组，其中的元素是内存池描述符指针，内存池描述符保存着内存池的私有数据。以`UDP_PCB`类型的内存池为例，内存池描述符中保存着该内存池的元素大小(size)和元素个数(num)。`base`指针指向的是内存池的本体，`tab`指针指向的是一个全局变量，该全局变量是空闲内存块指针。在初始化时，内存池本体被划分成num个size大小的内存块，每个内存块顶部是一个`next`指针，空闲的内存块通过`next`指针，串联在一起。

#### 3.2.2. 分配内存

```c
void *memp_malloc(memp_t type)
```

当需要分配内存时，先通过内存池类型查找到对应的内存池描述符，然后通过其中的tab指针找到空闲内存块，并返回给用户。在内存块空闲时，顶部会有一个`next`指针，但是分配给用户后，这个指针就失效了，用户可以完整的使用整个内存块。

#### 3.2.3. 释放内存

```c
void memp_free(memp_t type, void *mem)
```

用户将想要释放的内存块指针传递给`memp_free`函数，该函数在内存块顶部重新构造`next`指针，并串联在空闲队列顶部。

![img](typora-img/lwip源码分析/v2-10ffa12d92a464c479b97f9d9bfc25d5_1440w-1774426286548.jpg)

图（6）

### 3.3. pbuf

lwip使用`pbuf`结构体对数据进行封装，并在协议栈中传递。pbuf的重要性就跟linux内核协议栈中的`skb_buff`一样，先分析一个几种不同类型的pbuf的内存布局。

#### 3.3.1. PBUF_RAM

`PBUF_RAM`类型的`pbuf`是将所有数据放入一整个连续内存块中，`payload`直接跟在pbuf结构体之后。这种pbuf可以自定义大小。使用`mem`模块进行内存管理。

 ![img](typora-img/lwip源码分析/v2-382638b5cf0b34d516a0e7101a83447a_1440w.jpg)

图（7）

#### 3.3.2. PBUF_POOL

`PBUF_POOL`类型的`pbuf`是通过`memp`模块进行内存管理的，也就是通过内存池分配内存，所以所有`PBUF_POOL`类型的`pbuf`大小都是固定的。固定大小为:

```
[sizeof(struct pbuf) + (TCP_MSS+PBUF_IP_HLEN+PBUF_TRANSPORT_HLEN+PBUF_LINK_ENCAPSULATION_HLEN+PBUF_LINK_HLEN)<内存对齐>]<内存对齐>
```

![img](typora-img/lwip源码分析/v2-ad9fd7e4173adfbd252d1c2d811c43ad_1440w-1774426568652.jpg)

图（8）

### 3.3.3. PBUF_REF/PBUF_ROM

`PBUF_REF`和`PBUF_ROM`这两种`pbuf`中的`payload`指针都是指向其他地方的，用于引用其他数据，被引用的数据如果是易变数据（例如RAM中的数据）则使用`PBUF_REF`类型`pbuf`，如果是非易变数据（例如ROM中的数据）则使用`PBUF_ROM`类型的`pbuf`。这两种类型的`pbuf`都是固定大小，使用`memp`模块进行内存管理。

![img](typora-img/lwip源码分析/v2-8b6a63220263894e35dd65565a12dd9d_1440w.jpg)

图（9）

## 4. 数据收发过程（以UDP为例）

### 4.1. udp初始化

1. 创建`udp pcb(protocol control block)`，pcb中维护着udp端点的私有数据。

```c
struct udp_pcb *udp_new(void)
```

首先看一下`udp_pcb`中有什么数据，首先是[IP层](https://zhida.zhihu.com/search?content_id=231988713&content_type=Article&match_order=1&q=IP层&zhida_source=entity)相关的数据，然后是udp相关的端口、标志位等数据。

![img](typora-img/lwip源码分析/v2-8ea32694291c80ef832bae358ecfec8f_1440w.jpg)

图（10）

2. 绑定pcb到本地IP地址和端口，传入`udp_pcb`，IP地址，以及端口

```c
err_t udp_bind(struct udp_pcb *pcb, const ip_addr_t *ipaddr, u16_t port)
```

绑定的过程简单来说就是将`udp_pcb`中的`local_ip`字段、`local_port`字段进行赋值，然后将`udp_pcb`结构体插入全局的`udp_pcb`链表中。

3. 注册`udp`接收回调函数，传入对应`pcb`、回调函数指针、参数指针

```c
void udp_recv(struct udp_pcb *pcb, udp_recv_fn recv, void *recv_arg)
{
  /* remember recv() callback and user data */
  pcb->recv = recv;
  pcb->recv_arg = recv_arg;
}
```

注册回调的过程也很简单，就是将回调函数的指针赋给`pcb`中的`recv`字段，然后将参数指针赋给`recv_arg`字段

### 4.2. udp发送

1. 创建一个`pbuf`，并将待发送数据放入，传入的参数`layer = PBUF_TRANSPORT`，`pbuf_alloc`函数中根据`layer`预留对应的头部空间，比如`PBUF_TRANSPORT`预留76字节，`PBUF_IP`预留56字节。`length`是`data`长度，`type`是`pbuf`类型，这里以`PBUF_RAM`为例。

```c
struct pbuf *pbuf_alloc(pbuf_layer layer, u16_t length, pbuf_type type)
```

![img](typora-img/lwip源码分析/v2-1c386d2260652b7d96011325bdfef0dd_1440w.jpg)

图（10）

2. 以`udp_sendto()`函数为例，发送`udp`报文时，传入使用的`udp_pcb`，待发送数据指针`p`，目的IP地址`dst_ip`，目的端口`dst_port`

```c
err_t udp_sendto(struct udp_pcb *pcb, struct pbuf *p, const ip_addr_t *dst_ip, u16_t dst_port)
```

3. 对参数进行合法性检查

4. 根据目的IP地址查找路由，找到对应的网络接口

```c
netif = ip_route(&pcb->local_ip, dst_ip_route);
```

5. 获取源IP地址，如果`udp_pcb`的`local_ip`字段为`ANY`，则使用`netif`的ip地址作为源地址，否则使用`udp_pcb`绑定的ip地址，`udp_pcb`绑定的ip地址必须与`netif`的IP地址一致。

6. 添加udp头部信息，将`payload`指针向前移动8字节，给`udp`头部留出空间，然后对`udp`头部进行赋值

![img](typora-img/lwip源码分析/v2-eb1d3bb417ebbe758baa5dda6dad8e53_1440w.jpg)

图（11）

7. 传递给ip层

```c
err_t ip4_output_if_src(struct pbuf *p, const ip4_addr_t *src, const ip4_addr_t *dest,
                  u8_t ttl, u8_t tos,
                  u8_t proto, struct netif *netif)
```

8. 添加ip头部信息，将payload指针向前移动20字节，如果有options，则移动20+sizeof(options)<4字节对齐>

![img](typora-img/lwip源码分析/v2-3b3bd4a3ffa823fc514c77abe49802e2_1440w.jpg)

图（12）

9. 如果`pbuf`的`tot_len`大于`netif`的`mtu`，则需要分片，分片算法可以先从协议栈标准相关文档了解，再看具体实现会好理解一些

> err_t ip4_frag(struct pbuf *p, struct netif *netif, const ip4_addr_t *dest)
> 分片流程如下：
> 1. 计算nfb(分片block数量，以8字节为单位)
> const u16_t nfb = (u16_t)((netif->mtu - IP_HLEN) / 8);
>
> 2. 计算剩余数据量
> left = (u16_t)(p->tot_len - IP_HLEN);
>
> 3. 根据nfb对数据进行分片
> fragsize = LWIP_MIN(left, (u16_t)(nfb * 8));
>
> 对于每一个数据片：
>     1. 创建一个pbuf专门放IP头部
>         rambuf = pbuf_alloc(PBUF_LINK, IP_HLEN, PBUF_RAM);
>         SMEMCPY(rambuf->payload, original_iphdr, IP_HLEN);
>         2. 创建struct pbuf_custom_ref引用原始pbuf中的payload
>             3. 如果原始pbuf为pbuf chain，则必要情况下可以创建多个struct pbuf_custom_ref来分开引用

![img](typora-img/lwip源码分析/v2-ae212536b2f6d931f39e5425d92a4796_1440w.jpg)

图（13）

10. 调用`netif`的`output`接口对分片后的`pbuf`链表进行发送，一般`neif`的output接口就是`etharp_output`函数。

```c
err_t etharp_output(struct netif *netif, struct pbuf *q, const ip4_addr_t *ipaddr)
```

`etharp_output`函数中主要是做ip地址到mac地址的转换工作，包括查询`arp`表等，然后将数据链路层的头部信息填入`pbuf`中。

11. 调用`netif`的`linkoutput`接口对最终的二层以太网帧进行发送。 这个接口需要网卡驱动实现，这也是移植lwip需要做的工作之一。

### 4.3. udp接收

lwip的`raw api`中对udp的接收是通过注册回调函数的方式进行的，在初始化时需要注册回调函数，这个回调函数会在网络接收中断中被调用。在移植lwip时需要在网络接收中断中将数据以`pbuf`的形式传递给`netif`的`input`接口。`netif_add`函数会指定`netif`的`input`接口，一般是`ethernet_input`函数。

1. 在网络接收中断中，将数据拷贝到`pbuf`中，调用`ethernet_input`函数

```c
err_t ethernet_input(struct pbuf *p, struct netif *netif)
```

由于这里使用`pbuf`是`PBUF_POOL`类型的，拷贝后的数据分布如下图所示：

![img](typora-img/lwip源码分析/v2-798a474b2f0f5d67ec6c95d630c0e951_r.jpg)

图（14）

2. 解析以太网帧头部，获取协议类型信息，然后调整`pbuf`的`payload`指针，使其指向下一层头部。根据协议类型调用不同接口，比如IP协议，调用`ip4_input`接口；ARP协议，调用`etharp_input`接口。

![img](typora-img/lwip源码分析/v2-4ca0f14b4b7a0a21c9e76408c6cccf71_1440w.jpg)

图（15）

3. 以IP协议为例，如果报文中是IP数据报，则调用`ip4_input`。`ip4_input`函数中解析了IP头部，获取了IP头部长度，报文长度等信息，并进行合法检查。保存ip地址等信息到全局变量`ip_globals`中。

```c
err_t ip4_input(struct pbuf *p, struct netif *inp);
```

保存这些数据到全局变量应该是传输层会用到这些数据。

![img](typora-img/lwip源码分析/v2-959c897225d78d2b1901985e658e004e_1440w.jpg)

图（16）

4. 调用`ip4_input_accept`函数，根据报文中的目的IP地址，判断是否是发给这个网络接口的，如果是则继续接收。

```c
static int ip4_input_accept(struct netif *netif)
```

5. 调用`ip4_reass`函数对ip报文进行重组，对于重组算法建议先看IP协议的标准文档，然后先看代码实现会好理解一些。

```c
struct pbuf *ip4_reass(struct pbuf *p)
```

目前，lwip不支持IP options，所以报文头部长度大于20字节，会报错。

> ```text
> 重组流程如下：
> 1. 根据IP头部的id字段、目的地址、源地址，在当前数据报队列中查找报文段所属的数据报pbuf链表；
> 2. 如果当前报文段是第一帧，则将当前报文段的头部放入数据包的头部
> 3. 如果当前报文段是最后一帧，则更新数据包头部的总长度字段
> 4. 找到合适的位置，将当前报文段插入数据包的pbuf链表中
> 5. 如果接收到最后一帧，并且所有报文段都接收到了，就把报文头部拷贝到第一个报文段，然后将后面的报文段头部都删除掉
> ```

如下图所示，`struct ip_reassdata`结构体用于表示数据包，lwip会维护一个全局的`struct ip_reassdata`链表，来表示现在正在重组的数据包。每个`ip_reassdata`结构体自身会维护一个`pbuf`链表，其中的元素是接收到的报文段，它们按照片内偏移排列。每当需要重组的报文到来时，报文中的`ip_header`所在的内存区域会被替换成 `struct ip_reass_helper`结构体，`struct ip_reass_helper`结构体负责按照片内偏移组织`pbuf`。

![img](typora-img/lwip源码分析/v2-57c3a053f0e94e9b2b57b1bc85251c14_1440w.jpg)

图（17）

6. 更新全局的IP头部信息，将`pbuf`的`payload`指针指向`udp`头部，根据IP头部的协议类型信息，将`pbuf`送入下一层，这里以UDP协议为例，将`pbuf`传入`udp_input`。

```c
void udp_input(struct pbuf *p, struct netif *inp)
```

7. 遍历全局的`udp_pcb`链表，根据`pcb`的`ip`和`port`属性查找数据包对应的`pcb`

8. 将`pbuf`的`payload`指针指向`app_data`

9. 调用该`pcb`注册的`recv`函数，处理`pbuf`数据

## 5. TCP协议实现

TCP模块是协议栈实现的重点，因为TCP协议引入众多可靠传输机制，使得其实现较为复杂，这里专门用一章来剖析TCP协议。建议先阅读TCP协议标准文档，然后再看代码实现。

### 5.1. 数据收发过程梳理（以建立连接为例）

1. 创建`tcp_pcb`

```c
struct tcp_pcb *tcp_new(void)
```

2. 绑定`pcb`到本地IP和端口

```c
err_t tcp_bind(struct tcp_pcb *pcb, const ip_addr_t *ipaddr, u16_t port)
```

3. 调用`tcp_listen`，将`pcb`的状态设置为`LISTEN`，等待连接。并且为了节省内存空间，将`tcp_pcb`结构体转换成`tcp_pcb_listen`结构体。将`pcb`插入全局的`tcp_listen_pcbs`链表中。

```c
#define   tcp_listen(pcb) tcp_listen_with_backlog(pcb, TCP_DEFAULT_LISTEN_BACKLOG)
struct tcp_pcb *tcp_listen_with_backlog(struct tcp_pcb *pcb, u8_t backlog)
```

![img](typora-img/lwip源码分析/v2-64a7b3c5898fc3cf98b8b80ec68a7168_1440w.jpg)

图（18）

4. 注册`accept`回调函数，在`accept`回调函数中对多个回调函数进行注册。

```c
void tcp_accept(struct tcp_pcb *pcb, tcp_accept_fn accept)
```

5. 如果主动连接的一方发送了握手报文(SYN置位)，由上一章可知，报文在ip层会根据协议类型调用相应接口，假设接收到了握手报文后，调用`tcp_input`处理报文。

```c
void tcp_input(struct pbuf *p, struct netif *inp)
```

在`tcp_input`函数中会依次遍历`tcp_active_pcbs`、`tcp_tw_pcbs`、`tcp_listen_pcbs`三个链表，直到找到`IP`地址和`port`一致的`pcb`。`LISTEN`状态的`pcb`在`tcp_listen_pcbs`链表中。如果在`tcp_listen_pcbs`链表中找到了对应的`pcb`，则调用`tcp_listen_input`函数进行进一步处理。

```c
static void tcp_listen_input(struct tcp_pcb_listen *pcb)
```

6. **判断报文是否是握手报文**，重新分配一个`tcp_pcb`结构体，释放掉原来的`tcp_pcb_listen`结构体，调用`tcp_enqueue_flags`函数，发送`ACK`报文

```c
err_t tcp_enqueue_flags(struct tcp_pcb *pcb, u8_t flags)
```

7. 在`tcp_enqueue_flags`函数中，创建了一个`pbuf`，将`mss option`作为`payload`。然后调用`tcp_create_segment`函数进行组包。将组包后的`tcp segment`挂在`pcb`的`unsent`链表上

```c
static struct tcp_seg *tcp_create_segment(const struct tcp_pcb *pcb, struct pbuf *p, u8_t hdrflags, u32_t seqno, u8_t optflags)
```

![img](typora-img/lwip源码分析/v2-5d464827329d6c496b2fb2f15ac0c5ae_1440w.jpg)

图（19）

8. 调用tcp_output发送报文段。发送前要做的工作如下

```c
err_t tcp_output(structtcp_pcb*pcb)
```

> ```text
> tcp报文段发送过程：
> 1. 计算发送窗口
>       wnd = LWIP_MIN(pcb->snd_wnd, pcb->cwnd);
> 2. 根据pcb绑定的网络接口，或者ip层的路由结果确定发送的网络接口
> 3. 检查发送当前tcp报文段会不会超过发送窗口，如果超过了发送窗口，同时RTO定时器没有开启（换句话说没有在空中的数据(in-flight data)）,那么就启动persist timer，当persist timer超时后，如果发送窗口为0，则实施零窗口探测；如果发送窗口不为0，则实施分割tcp报文段
> 4. while循环一直发送报文段，直到发送窗口小于报文段长度
> 5. 对于每一次发送，调用tcp_do_output_nagle检查是否满足nagle算法限制，如果满足就调用tcp_output_segment发送。在tcp_output_segment函数中填入确认号、窗口、MSS option等字段，设置超时重传定时器，计算校验和，调用ip_output_if发送报文段，接下来的发送过程跟udp一致
> ```

### 5.2. 序列号与确认机制

序列号与确认的机制是TCP协议实现可靠的基石。TCP协议通过给每个字节分配序列号，并在接收到对端的确认后就认为对应字节已成功接收。

#### 5.2.1. 序列号初始化

TCP协议标准规定，在`MSL`时间内，序列号空间需要不重叠，序列号初始化时应该是时钟、发送IP、发送端口、接收IP、接收端口、密钥相关的一个随机数，计算公式为 `ISN = M + F(localip, localport, remoteip, remoteport, secretkey)`
lwip中在主动连接或者在`LISTEN`状态在接收到握手报文后就会调用`tcp_next_iss`函数生成初始化序列号。

```c
u32_t
tcp_next_iss(struct tcp_pcb *pcb)
{
#ifdef LWIP_HOOK_TCP_ISN
  ···
  return LWIP_HOOK_TCP_ISN(&pcb->local_ip, pcb->local_port, &pcb->remote_ip, pcb->remote_port);
#else /* LWIP_HOOK_TCP_ISN */
  static u32_t iss = 6510;
  ···
  iss += tcp_ticks; //tcp_ticks会在定时器函数中递加，每500ms加一
  return iss;
#endif /* LWIP_HOOK_TCP_ISN */
}
```

lwip只提供非常基础的序列号初始化算法，无法抵御网络欺骗攻击(Spoofing attacks)，如果想要更加稳健的序列号初始化算法，需要开启`LWIP_HOOK_TCP_ISN`宏定义，然后自己移植相应算法。

#### 5.2.2. 确认机制

发送时通过改变`pcb->snd_nxt`，递增已使用序列号

```c
err_t tcp_output(struct tcp_pcb *pcb)
{
    ...
    //发送tcp segment
    err = tcp_output_segment(seg, pcb, netif);
    ...
    //更新pcb的snd_nxt字段
    snd_nxt = lwip_ntohl(seg->tcphdr->seqno) + TCP_TCPLEN(seg);
    if (TCP_SEQ_LT(pcb->snd_nxt, snd_nxt)) {
      pcb->snd_nxt = snd_nxt;
    }
    ...
}
```

接收到报文后通过改变`pcb->lastack`，递增已确认序列号

```c
static void tcp_receive(struct tcp_pcb *pcb)
{
    ...
    if (TCP_SEQ_BETWEEN(ackno, pcb->lastack + 1, pcb->snd_nxt)) {
        pcb->lastack = ackno;
    }
    ...
}
```

成功发送报文后，会将报文段挂在`pcb->unacked`链表上。接收到报文后，会根据报文中的确认号将`unacked`链表中可以被确认的报文段释放掉。并且，会根据确认号释放掉`unsent`链表中的报文。之所以要遍历`unsent`链表，是因为在超时重传时，会将要重传的报文段放到`unsent`链表中。

### 5.3. 收发过程中的数据管理

众所周知，TCP是**面向字节流的**，但是在实际发送过程中肯定不能一个字节一个字节的发送，肯定会有一个组包的过程，分段就是按照MSS(Maximum Segment Size)对字节流进行组包。

#### 5.3.1. 计算mss

在连接建立起来之后呢，我们可以使用`tcp_write`接口进行发送

```c
err_t tcp_write(struct tcp_pcb *pcb, const void *arg, u16_t len, u8_t apiflags)
```

在`tcp_write`函数中会对数据进行分段，每个报文段的长度为`mss`，这个值取的是 **min(pcb->mss, pcb->snd_wnd_max / 2)**。第二个值从形式上就看出了与糊涂窗口综合征避免算法有关，第一个值就是正儿八经的`mss`值，`pcb->mss`在每次接收到tcp报文时会被重新计算，在TCP协议标准中mss的计算公式是 **Eff.snd.MSS = min(SendMSS+20, MMS_S) - TCPhdrsize - IPoptionsize**

1. 在`tcp_pcb`初始化时会对`mss`进行初始化

```c
#define INITIAL_MSS TCP_MSS
#define TCP_MSS                         536

pcb->mss = INITIAL_MSS;
```

2. 在接收到有`MSS option`的报文时对`mss`进行更新

```c
pcb->mss = ((mss > TCP_MSS) || (mss == 0)) ? TCP_MSS : mss;

//如果握手成功则更新mss
pcb->mss = tcp_eff_send_mss(pcb->mss, &pcb->local_ip, &pcb->remote_ip);

#define tcp_eff_send_mss(sendmss, src, dest) \
    tcp_eff_send_mss_netif(sendmss, ip_route(src, dest), dest)

u16_t
tcp_eff_send_mss_netif(u16_t sendmss, struct netif *outif, const ip_addr_t *dest)
{
    ···
    mtu = outif->mtu;
    ···
    offset = IP_HLEN + TCP_HLEN;
    ···
    //通过网络接口的mtu计算mss_s
    mss_s = (mtu > offset) ? (u16_t)(mtu - offset) : 0; 

    //因为lwip不支持IP options，所以这里没有考虑IP options；对于TCP options会在tcp_write中考虑
    sendmss = LWIP_MIN(sendmss, mss_s); 

    return sendmss;
}
```

3. 每次发送报文前，重新计算`mss`

```c
pcb->mss = tcp_eff_send_mss(pcb->mss, &pcb->local_ip, &pcb->remote_ip);
```

#### 5.3.2. 发送过程

前面的章节以建立连接为例已经梳理了TCP的收发过程，但是毕竟建立连接的过程不涉及`payload`的处理，下面两个章节分析了在收发过程中的数据结构变化。在建立连接之后，通过`tcp_write`和`tcp_output`来发送数据，`tcp_write`的`flag`参数可以配置数据处理是**深拷贝**还是**浅拷贝**。由于浅拷贝更加高效，这里以浅拷贝为例。`tcp_write`传入的是一整块用户数据，如下图中的`data_to_segment`。数据处理的过程如下：

1. 如果`unsent`链表中的最后一个`segment`没满`MSS`，则将一部分数据放在最后一个`segment`上。如果最后一个`segment`的最后一个`pbuf`有`oversize`，则拷贝一部分数据到其中，否则创建一个新的`pbuf`挂在最后一个`segment`上；

2. 创建一个新的`segment`，挂到`unsent`链表中；

![img](typora-img/lwip源码分析/v2-71bfb91b483e8ad9a0c21858298ab682_1440w.jpg)

图（20）

调用`tcp_write`只是将数据分段，然后挂在`unsent`链表中，调用`tcp_output`才会将满足发送条件的报文发送出去。在`tcp_output`函数中会取下`unsent`链表的首个元素，然后判断是否满足发送条件（是否在发送窗口内），如果满足条件，则将`tcp_seg->p`指向的`pbuf`链表传递给IP层接口。最终IP层会再添加IP头部然后发送出去。可以看出`tcp_seg`结构体只是在分段的时候使用，最终的数据还是通过`pbuf`进行发送。

#### 5.3.3. 接收过程

在网络接收中断中,会将数据以`pbuf`链表的形式传递给lwip协议栈。IP层的处理可以参考之前`udp`收发的过程。ip层会将`pbuf`的`payload`指针指向`tcp header`，然后调用`tcp_input`交给tcp处理。`tcp_input`函数中首先做的就是将`pbuf`转换成`tcp_seg`，网络驱动中一般会使用`PBUF_POOL`类型的`pbuf`。

![img](typora-img/lwip源码分析/v2-e05084808df84db5b6d748843ee31eec_1440w.jpg)

图（21）

从上图可以看出，`tcp_input`函数中将`pbuf`挂在了一个全局的`tcp_seg`结构体上，然后调用`tcp_process`函数。`tcp_process`函数主要是对状态机的处理，不同状态下的行为不同，这里以`ESTABLISHED`状态为例。`tcp_process`调用`tcp_receive`函数对报文进行接收，`tcp_receive`函数中会根据接收窗口对`pbuf`进行裁剪，裁剪完如果`seqno == rcv.nxt`，也就是数据是连续的，则将`pubf`指针赋给一个全局变量`recv_data`，这样应用层就可以通过这个指针对数据进行处理了。

![img](typora-img/lwip源码分析/v2-19ffa391d93879fe4c530aecc757dcf9_1440w.jpg)

图（22）

### 5.4. 超时重传

调用`tcp_write`函数会将数据分段成`tcp_seg`，然后将`tcp_seg`挂在`pcb`的`unsent`链表中，并不会立即发送出去。调用`tcp_output`函数会将可发送数据发送出去。

#### 5.4.1. 重传定时器

1. 发送报文前初始化重传定时器，将`tcp_seg`挂在`pcb`的`unacked`链表

```c
pcb->rtime = 0;
```

1. 每500ms检查一次重传定时器，如果超时就触发重传

```c
void tcp_slowtmr(void)
{
    ···
    if ((pcb->rtime >= 0) && (pcb->rtime < 0x7FFF)) {
          ++pcb->rtime;
    }
    ···

    //超时发生
    if (pcb->rtime >= pcb->rto) {

          //tcp_rexmit_rto_prepare函数将unacked链表上的tcp_seg，全部取下来插入到unsent链表头部
          if ((tcp_rexmit_rto_prepare(pcb) == ERR_OK) || ((pcb->unacked == NULL) && (pcb->unsent != NULL))) {

            if (pcb->state != SYN_SENT) {

                //更新RTO，sa是8倍的RTT平均值，sv是4倍的RTT方差，（(pcb->sa >> 3) + pcb->sv）是协议标准中的RTO计算方法，这里又乘了个系数（tcp_backoff[backoff_idx]），这个系数随着重传次数（pcb->nrtx）的增加而增加
                u8_t backoff_idx = LWIP_MIN(pcb->nrtx, sizeof(tcp_backoff) - 1);
                int calc_rto = ((pcb->sa >> 3) + pcb->sv) << tcp_backoff[backoff_idx];
                pcb->rto = (s16_t)LWIP_MIN(calc_rto, 0x7FFF);
            }

            //重置重传定时器
            pcb->rtime = 0;

            //拥塞控制相关
            eff_wnd = LWIP_MIN(pcb->cwnd, pcb->snd_wnd);
            pcb->ssthresh = eff_wnd >> 1;
            if (pcb->ssthresh < (tcpwnd_size_t)(pcb->mss << 1)) {
                pcb->ssthresh = (tcpwnd_size_t)(pcb->mss << 1);
            }
            pcb->cwnd = pcb->mss;

            pcb->bytes_acked = 0;

            //这个函数中会调用tcp_output发送unsent报文段，然后增加重传次数（pcb->nrtx）
            tcp_rexmit_rto_commit(pcb);
        }       
    }
    ···
}
```

#### 5.4.2. RTT和RTO的计算

RTO的计算是依赖于RTT的计算的，RTT的计算涉及两个主要参数，sa和sv，sa是8倍的RTT平均值，sv是4倍的RTT方差。在每接收一帧报文时，会对RTT进行估计：

```c
//如果开启了RTT估计，并且报文的ack包括了rtseq，则对RTT进行估计
if (pcb->rttest && TCP_SEQ_LT(pcb->rtseq, ackno)) {

    //rttest记录了报文序列号为rtseq的报文发送时的时间戳，tcp_ticks是当前时间戳，这里m记录上RTT测量值
    m = (s16_t)(tcp_ticks - pcb->rttest);
    ...

    //这里的算法都是标准里的
    m = (s16_t)(m - (pcb->sa >> 3));
    pcb->sa = (s16_t)(pcb->sa + m);
    if (m < 0) {
    m = (s16_t) - m;
    }
    m = (s16_t)(m - (pcb->sv >> 2));
    pcb->sv = (s16_t)(pcb->sv + m);

    //根据sa和sv计算rto
    pcb->rto = (s16_t)((pcb->sa >> 3) + pcb->sv);
    ...
    pcb->rttest = 0;
}
```

当然，在发送重传时，RTT估计是关闭的

### 5.5. 拥塞控制

#### 5.5.1. 初始化拥塞窗口

在建立连接时对`cwnd`进行初始化，初始化`cwnd`为2~4MSS

```c
pcb->cwnd = LWIP_TCP_CALC_INITIAL_CWND(pcb->mss);

#define LWIP_TCP_CALC_INITIAL_CWND(mss) ((tcpwnd_size_t)LWIP_MIN((4U * (mss)), LWIP_MAX((2U * (mss)), 4380U)))
```

#### 5.5.2. 正常收发报文时拥塞窗口的变化

在没有报文丢失和拥塞发生时，每接收到一帧报文，cwnd会如下变化：

```c
static void tcp_receive(struct tcp_pcb *pcb)
{
    ...
    if (pcb->state >= ESTABLISHED) {

        //慢启动
        if (pcb->cwnd < pcb->ssthresh) {
            tcpwnd_size_t increase;

            //当正在进行RTO重传时，拥塞窗口的增长上限是1MSS，否则为2MSS
            u8_t num_seg = (pcb->flags & TF_RTO) ? 1 : 2;

            //acked表示这帧报文ack了多少字节数据
            increase = LWIP_MIN(acked, (tcpwnd_size_t)(num_seg * pcb->mss));
            TCP_WND_INC(pcb->cwnd, increase);

        //拥塞避免
        } else {
            //bytes_acked在快恢复发生后会被清零，每当bytes_acked增长到cwnd时，cwnd增加MSS
            TCP_WND_INC(pcb->bytes_acked, acked);
            if (pcb->bytes_acked >= pcb->cwnd) {
                pcb->bytes_acked = (tcpwnd_size_t)(pcb->bytes_acked - pcb->cwnd);
                TCP_WND_INC(pcb->cwnd, pcb->mss);
            }
        }
    }
    ...
}
```

#### 5.5.3. 超时重传发生时拥塞窗口的变化

```c
void tcp_slowtmr(void)
{
    ...
    eff_wnd = LWIP_MIN(pcb->cwnd, pcb->snd_wnd);

    //慢启动阈值变为窗口的一半，最小为2MSS
    pcb->ssthresh = eff_wnd >> 1;
    if (pcb->ssthresh < (tcpwnd_size_t)(pcb->mss << 1)) {
        pcb->ssthresh = (tcpwnd_size_t)(pcb->mss << 1);
    }

    //cwnd变为1MSS
    pcb->cwnd = pcb->mss;

    pcb->bytes_acked = 0;
    ...
}
```

#### 5.5.4. 快重传时拥塞窗口的变化

当连续接收到无效ACK时

```c
static void tcp_receive(struct tcp_pcb *pcb)
{
    ...
    //无效ACK
    if (TCP_SEQ_LEQ(ackno, pcb->lastack)) {
      //接收到的报文长度为0
      if (tcplen == 0) {
        //窗口无更新
        if (pcb->snd_wl2 + pcb->snd_wnd == right_wnd_edge) {
          //RTO定时器在计时
          if (pcb->rtime >= 0) {
            //报文中的确认号为最大的已确认序列号
            if (pcb->lastack == ackno) {
              found_dupack = 1;
              if ((u8_t)(pcb->dupacks + 1) > pcb->dupacks) {
                ++pcb->dupacks;
              }

              //快重传满足条件
              if (pcb->dupacks > 3) {
                //每接收到一次重复ACK就将cwnd增加mss
                TCP_WND_INC(pcb->cwnd, pcb->mss);
              }
              if (pcb->dupacks >= 3) {
                //快重传
                tcp_rexmit_fast(pcb);
              }
            }
          }
        }
      }
      ...
    } 
    ...
}

void tcp_rexmit_fast(struct tcp_pcb *pcb)
{
  ...
  if (pcb->unacked != NULL && !(pcb->flags & TF_INFR)) {

    if (tcp_rexmit(pcb) == ERR_OK) {

      //ssthresh减半
      pcb->ssthresh = LWIP_MIN(pcb->cwnd, pcb->snd_wnd) / 2;

      //ssthresh最少2MSS
      if (pcb->ssthresh < (2U * pcb->mss)) {
        pcb->ssthresh = 2 * pcb->mss;
      }

      //cwnd变为ssthread + 3MSS
      pcb->cwnd = pcb->ssthresh + 3 * pcb->mss;
      tcp_set_flags(pcb, TF_INFR);

      pcb->rtime = 0;
    }
  }
}
```

#### 5.5.5. 快恢复时拥塞窗口变化

```c
static void tcp_receive(struct tcp_pcb *pcb)
{
    ...
    if (pcb->flags & TF_INFR) {
        tcp_clear_flags(pcb, TF_INFR);

        //快重传完成后，将cwnd设置为ssthresh
        pcb->cwnd = pcb->ssthresh;
        pcb->bytes_acked = 0;
    }   
    ...
}
```







