# lwip readme 中文版

**lwIP 简介**

lwIP 是一个轻量级的独立 TCP/IP 协议套件实现，由瑞典计算机科学研究所 (SICS) 的计算机与网络架构 (CNA) 实验室的 Adam Dunkels 开发。

==lwIP TCP/IP 实现的重点是减少 RAM 使用量，同时仍提供完整功能的 TCP 协议。这使得 lwIP 适用于仅有数十 KB 可用 RAM 和大约 40 KB 代码存储空间（ROM）的嵌入式系统。==

**特性**

- IP (网际协议)，包括跨多个网络接口的数据包转发
- ICMP (互联网控制消息协议)，用于网络维护和调试
- IGMP (互联网组管理协议)，用于组播流量管理
- UDP (用户数据报协议)，包括实验性的 UDP-lite 扩展
- TCP (传输控制协议)，包含拥塞控制、RTT 估计和快速恢复/快速重传
- 专业的 raw/native API 以提升性能
- 可选的类 Berkeley 套接字 API
- DNS (域名解析器)
- SNMP (简单网络管理协议)
- DHCP (动态主机配置协议)
- AUTOIP (用于 IPv4，符合 RFC 3927)
- PPP (点对点协议)
- ARP (地址解析协议) 用于以太网

**许可证**

lwIP 基于 BSD 许可证自由提供。

**开发**

lwIP 已发展成为一个优秀的嵌入式设备 TCP/IP 协议栈，使用该协议栈的开发人员经常提交错误修复、改进和新增功能，以进一步增强其可用性。

lwIP 的开发托管在 Savannah 上，这是一个软件开发、维护和分发的中心点。任何人都可以通过使用 Savannah 的界面、CVS 和邮件列表来帮助改进 lwIP。一个核心开发团队将向 CVS 源代码树提交更改。

lwIP TCP/IP 协议栈维护在 'lwip' CVS 模块中，而贡献（例如平台移植）则位于 'contrib' 模块中。

有关用户和开发者访问 CVS 服务器的详细信息，请参见 doc/savannah.txt。

最新的 CVS 代码包可以从以下地址下载：

http://savannah.gnu.org/cvs.backups/lwip.tar.gz[已更改 - 需修正]

当前的 CVS 代码树可通过网页浏览：

http://savannah.nongnu.org/cgi-bin/viewcvs/lwip/lwip/

http://savannah.nongnu.org/cgi-bin/viewcvs/lwip/contrib/

通过 lwIP 项目页面提交补丁和报告错误：

http://savannah.nongnu.org/projects/lwip/

**文档**

lwIP 原始（已过时）的主页以及 Adam Dunkels 关于 lwIP 的论文位于官方 lwIP 主页：

http://www.sics.se/~adam/lwip/

源代码的自动文档会定期从当前 CVS 源码中提取，并可在此网页获取：

http://www.nongnu.org/lwip/

现有一个关于 lwIP 的、不断成长的 Wiki 网站：

http://lwip.wikia.com/wiki/LwIP_Wiki

此外，您可以订阅以下邮件列表：

http://savannah.nongnu.org/mail/?group=lwip

以及可搜索的邮件存档：

http://lists.nongnu.org/archive/html/lwip-users/

http://lists.nongnu.org/archive/html/lwip-devel/

阅读 Adam 的论文、`docs/`目录下的文件、浏览源代码文档以及查阅邮件列表存档，是熟悉 lwIP 设计的有效途径。

Adam Dunkels <adam@sics.se>

Leon Woestenberg <leon.woestenberg@gmx.net>