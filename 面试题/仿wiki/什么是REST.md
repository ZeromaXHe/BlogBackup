# 导航表

| key          | value                                                        |
| ------------ | ------------------------------------------------------------ |
| **题目**     | 什么是 REST？                                                |
| **同义题目** | 什么是 RESTful 风格？                                        |
| **主题**     | 架构、微服务                                                 |
| **上推问题** | （微服务架构中的进程通信，待完成）、<br/>（基于同步远程过程调用模式的通信，待完成） |
| **平行问题** | （什么是 gRPC，待完成）                                      |
| **下切问题** | （什么是 HATEOAS 原则，待完成）                              |

REST 是 Representational State Transfer 的简称，中文翻译为“表征状态转移”。

如今开发者非常喜欢使用 RESTful 风格来开发 API。REST 是一种（总是）使用 HTTP 协议的进程间通信机制。

REST 中的一个关键概念是**资源**，它通常表示单个业务对象，例如客户或产品，或业务对象的集合。REST 使用 HTTP 动词来操作资源，使用 URL 引用这些资源。例如，GET 请求返回资源的表示形式，该资源通常采用 XML 文档或 JSON 对象的形式，但也可以使用其他形式（如二进制）。POST 请求创建新资源，PUT 请求更新资源。

# 1. REST 成熟度模型

Leonard Richard 为 REST 定义了一个成熟度模型，具体包含以下四个层次。

> Leonard 是 RESTful API 设计领域的专家，Beautiful Soup 的开发者，他还是一个科幻作家。

- **Level 0**：Level 0 层级服务的客户端只是向服务端点发起 HTTP POST 请求，进行服务调用。每个请求都指明了需要执行的操作、这个操作针对的目标（例如，业务对象）和必要的参数。
- **Level 1**：Level 1 层级的服务引入了资源的概念。要执行对资源的操作，客户端需要发出指定要执行的操作和包含任何参数的 POST 请求。
- **Level 2**：Level 2 层级的服务使用 HTTP 动词来执行操作，譬如 GET 表示获取、POST 表示创建、PUT 表示更新。请求查询参数和主体（如果有的话）指定操作的参数。这让服务能够借助 Web 基础设施服务，例如通过 CDN 来缓存 GET 请求。
- **Level 3**：Level 3 层级的服务基于 HATEOAS（Hypertext As The Engine Of Application State）原则设计，基本思想是在由 GET 请求返回的资源信息中包含链接，这些链接能够执行该资源允许的操作。例如，客户端通过订单资源中包含的链接取消某一个订单，或者发送 GET 请求去获取该订单，等等。HATEOAS 的优点包括无须再客户端代码中写入硬链接的 URL。此外，由于资源信息中包含可允许操作的链接，客户端无须猜测在资源的当前状态下执行何种操作。

# 2. REST 的好处和弊端

REST 有如下好处：

- 它非常简单，并且大家都很熟悉。
- 可以使用浏览器扩展（比如 Postman 插件）或者 curl 之类的命令行（假设使用的是 JSON 或其他文本格式）来测试 HTTP API。
- 直接支持请求 / 响应方式的通信。
- HTTP 对防火墙友好。
- 不需要中间代理，简化了系统架构。

它也存在一些弊端：

- 它只支持请求 / 响应方式的通信。
- 可能导致可用性降低。由于客户端和服务直接通信而没有代理来缓冲消息，因此它们必须在 REST API 调用期间都保持在线。
- 客户端必须知道服务实例的位置（URL）。即服务发现，这是现代应用程序中的一个重要问题。客户端必须使用所谓的**服务发现机制**来定位服务实例。
- 在单个请求中获取多个资源具有挑战性。
- 有时很难将多个更新操作映射到 HTTP 动词。

# 参考文档

- 《微服务架构设计模式》3.2.1 使用 REST