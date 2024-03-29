
QUIC代表“Quick UDP Internet Connections”，是一种基于UDP协议的新一代互联网传输协议。它是由Google在2012年开始开发，旨在取代现有的基于TCP协议的HTTP传输协议。

QUIC的目标是通过减少连接建立时间、提高传输速度和增强安全性等方面的优化，提高网络性能和用户体验。**QUIC通过在传输层（即OSI模型的第四层）中实现加密、可靠性和多路复用等功能**，而无需在应用层（即OSI模型的第七层）中实现这些功能。

QUIC的主要特点包括：

-   快速建立连接：QUIC连接的建立和维护时间比TCP连接更短。[[0-RTT]]
-   多路复用：QUIC可以在单个连接上同时传输多个流，提高传输效率。
-   安全性：QUIC内置了TLS协议，可以提供端到端的加密和认证。
-   鲁棒性：QUIC可以自动适应网络状况的变化，如丢包、延迟等。
-   可靠性：QUIC在传输层提供可靠的数据传输，无需依赖TCP的机制。

QUIC最初是为Google的产品和服务开发的，但现在已经成为许多其他公司和组织的关注点，包括Mozilla、Cloudflare、Facebook等。目前，QUIC已被标准化为IETF的一个标准协议，即QUICv1（也称为QUIC-HTTP/3）。