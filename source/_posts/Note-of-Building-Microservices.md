title: Note of Building Microservices
author: Jingyi Yang
tags:
  - Microservice
categories:
  - Book Notes
date: 2018-04-14 14:44:00
---

## Chap 2 演化式架构师  
### 三个层次：  
- 战略目标  
- 架构原则  
- 设计和交付实践  

<!-- more -->
  
## Chap 3 如何建模服务  
### What makes a good service?  
松耦合，高内聚  
  
### Bounded context 限界上下文  
> 一个给定领域都包含多个限界上下文， 每个限界上下文中的模型一部分需要与外界通信，另一部分不需要。  

Or define as:  
> 一个由显式边界限定的特定职责  
  
- 共享特定的模型，而非内部表示。  
- 一般来说，微服务应该清晰地与限界上下文保持一致。  
- 避免过早划分边界。逐步划分上下文  
- 考虑功能，而非共享数据
-  - 即首先“这个上下文干什么”，再考虑“它需要什么样的数据”。  
  
## Chap 4 集成  
### 理想的集成技术  
- 避免破坏性修改：  
- - 更改某个服务导致其消费方发生改变  
- 保证API的技术无关性  
- 使服务易于消费方使用  
- 隐藏内部实现细节  
  
### 数据库集成  
最常见，但应当避免，因为：  
- 暴露内部实现细节  
- 绑定了特定技术  
- 破坏内聚性  
  
### 服务间通信  
同步与异步（加回调）  
- 即请求/响应或基于事件  
  
### 服务间协作  
编排与协同 Orchestration and Choreography  
- 协同可降低耦合度，但需要跨服务的监控  

_(Orchestration of Microservices, Choreography of SOA ?)_  
  
### 请求/响应  
对于请求/响应方式，考虑两种技术：RPC和REST  

### RPC  
- SOAP, Thrift, protocol buffers, RMI  
- 技术耦合，过度抽象  
### REST  
- RPC的替代方案  
- 资源的概念  
- 底层协议常使用HTTP  
- - 动词复用（GET，POST  
- - 生态系统（Varnish HTTP缓存代理，mod_proxy LB）  
  
### JOSN Or XML  
### JSON  
- 简单，紧凑  
### XML  
- 超媒体控制，XPATH  
  
### 基于HTTP的REST的缺点  
- 无法生成客户端的桩代码，RPC可以  
- 超媒体控制需要自行实现  
- 一些Web框架无法支持所有HTTP动词（PUT，DELETE）  
- 性能，不如二进制协议（Thrift）  
- 序列化自行实现  
  
### 异步协作  
对于基于事件的异步协作方式，考虑：微服务发布事件机制和消费者接收事件机制  
- MQ或者HTTP（ATOM）  
事件驱动架构和异步编程有一定复杂性，谨慎选用。（监控和并联ID）  
  
### 响应式扩展（Reactive extensions, Rx）  
- 组装多个调用的结果，并执行操作  
  
### DRY  
- 微服务内部避免，跨服务情况下可适当违反（减少耦合）  
  
### 客户端库  
- 简化服务的使用，避免不同消费者间重复代码  
- 不要包含与目标服务相关的逻辑  
  
### 按引用访问  
- 避免资源失效  
- 缓存，取舍  
  
### 版本管理  
- 破坏性修改：及早发现，尽可能延迟  
- 语义化的版本管理  
- 不同版本的接口共存  
  
### UI  
- 不同交互形式的约束  
- API组合  
- UI片段的组合  
- BFF（Backends For Frontends，为前端服务的后端）  
- - API聚合接口层，对多个后端调用进行编排，为不同设备定制  
  
## Chap 5 分解单块系统  
### 接缝  
### 分离数据库：  
- 打破外键  
- 共享静态数据（e.g. 国家代码）  
- 共享数据：抽象为提供数据的服务  
- 共享表：拆成两张  
  
### 事务边界  
### 分布式事务  
- 事务管理器  
- 两阶段提交（所有投票成功则提交）  
  
## Chap 6 部署  
- CI  
- 每个微服务都有一个源代码库和CI构建  
- 构建流水线和CD（持续交付）  
- 定制化虚拟机镜像（可作为构建物）  
- 不可变服务器  
- 服务与主机间的映射（尽量单服务-单主机/容器）  
- 自动化一切  
  
### 虚拟化：  
- Hypervisor+虚拟机  
- Vagrant： 用于开发和测试  
- Linux容器（内核相同）  
- Docker  
  
## Chap 7 测试  
### 测试类型  
测试象限 -》 右图  
### 测试范围  
- 测试金字塔：  
- - 用户界面测试  
- - 服务测试  
- - 单元测试  
-   
- 服务测试  
- - 绕开UI，直接针对服务的测试  
- 端到端测试  
- - 覆盖整个系统， 打开浏览器操作UI  
  
### 减少测试所用时间，加强反馈  
  
### 元版本  
- 整个系统使用一个版本号：加强了耦合  
  
### 测试场景，而非故事  
### 消费者驱动的测试  
- CDC， consumer-driven contract  
  
### 部署后再测试  
- 冒烟测试  
- 蓝/绿部署：两个版本，只有一个接受真正的请求  
- 金丝雀发布：部分生产流量引至新部署的版本，并检验其预期  
  
### MTBF  
- Mean time between failures 平均故障时间  
### MTTR  
- Mean time to repair 平均修复时间  
### MTTR prior to MTBF  
  
## Chap 8 监控  
- ELK  
- 更多日志  
- 综合监控  
- 合成事务 /语义监控  
- 关联标识：跟踪调用  
- 级联故障：监控系统间的集成点  
  
### 对于每个服务：  
- 最低限度：请求响应时间，下游服务的健康状态  
- 标准化如何收集和存储指标  
- 标准格式，记录到标准位置：方便聚合  
- 监控底层操作系统：跟踪流氓进程，进行容量规划  
  
## Chap 9 安全  
### 核心：  
- Authentication and authorization  
- 身份验证：确认他是谁  
- 授权：主体映射到他可以进行的操作中  
### 主体（principal）  
- 进行身份验证的人或事  
  
### 单点登录（Single Sign-On）  
- 技术：  
- - SAML  
- - OpenID Connect  
- 身份提供者：  
- - 身份验证  
- - External/Internal  
- 服务提供者：  
- - 资源访问控制  
### 单点登录网关：  
- 集中处理重定向用户的行为，只在一个地方执行握手  
  
### 服务间身份验证和授权  
- 边界之内允许一切  
- - 很常见  
- - 无法抵抗中间人攻击  
- HTTPS  
- - 避免通信窃听或篡改  
- - SSL证书管理麻烦  
- - 不能被反向代理服务器缓存  
- SAML/Open ID Connect  
- - 为客户端创建账户/服务账户  
- 客户端证书  
- - TLS  
- - 似乎比SSL更麻烦  
- - 特别重要的数据通信时考虑使用  
- HMAC  
- - 基于哈希的消息码  
- - Part of OAuth specification  
- - e.g. AWS S3 API  
- API密钥  
  
### 静态数据的安全  
- 使用众所周知的加密算法  
- - 自行实现非常不靠谱  
- 一切系于密钥  
- - 单独的密钥库或者安全设备  
- 选择加密目标  
- - 更细粒度的服务，区分不同安全级别  
- 按需解密  
- - 首次加密，按需解密，且不保存结果  
- 加密备份数据  
  
### 深度防御  
- 防火墙  
- 日志  
- 入侵检测（和预防）系统  
- - IDS Intrusion Detection Systems  
- - IPS Intrusion Prevention Systems  
- - 启发式，可信范围内积极寻找可疑行为  
- 网络隔离  
- - 服务放在不同网段，控制之间的通信  
- - 定义对等互连规则（peering rules）  
- - e.g. VPC of AWS  
- 操作系统  
- - 持续打补丁  
  
### 社会工程学  
  
## Chap 10 康威定律和系统设计  
### 康威定律（Conway's law）指出：  
> organizations which design systems ... are constrained to produce designs which are copies of the communication structures of these organizations.  
- 任何设计系统的组织，最终产生的设计等同于组织之内、之间的沟通结构。  
  
### 松耦合组织与紧耦合组织  
  
### 服务所有权分配  
- 共享服务  
- 特性团队  
- - 所有服务共享  
  
### 试图将服务所有权与同地团队匹配，两者亦与组织限界上下文匹配  
  
## Chap 11 规模化微服务  
### 故障无处不在  
- 假设一切都可能失败  
### 多少是太多？  
- 跨功能需求  
- - 响应时间/延迟  
- - 可用性  
- - 数据持久性  
### 功能降级  
### 架构性安全措施  
- 断路器  
- 超时  
- 舱壁（bulkhead）隔离  
### 反脆弱  
- 主动引发故障，拥抱故障  
- e.g. Netflix Chaos Monkey  
### 幂等操作  
- HTTP的GET和PUT具有幂等语言，但是仍需要自行实现  
  
### 扩展（无状态的服务）  
- 目的：  
- - 可用性与性能  
- 方案：  
- - 更强大的主机  
- - 拆分负载  
- - 分散风险  
- - 负载均衡（避免单点故障）  
- - 基于Worker的系统  
- - 重新设计  
  
### 扩展数据库  
- 扩展读取  
- - 缓存  
- - 只读副本  
- 扩展写操作  
- - 分片  
- 共享数据库基础设施  
- CQRS模式  
- - Command-Query Responsibility Segregation 命令查询职责分离  
  
### 缓存  
- 缓存在哪里？  
- - 客户端缓存  
- - 反向代理/CDN缓存  
- - 服务器缓存  
- HTTP缓存  
- 为写使用缓存  
- - 后写式（writebehind）缓存  
- 为弹性使用缓存  
- - 使用失效但可用的数据  
- 隐藏源服务  
- - 保护源服务  
- - 后台异步重建缓存  
- 保持简单  
- - 缓存越多越难追踪  
  
### 自动伸缩  
  
### CAP定理  
- 一致性（Consistency）  
- - 访问多个节点能得到同样的值  
- 可用性（Availability）  
- - 每个请求都能获得响应  
- 分区容忍性（Partition Tolerance）  
- - 集群中的某些节点无法联系之后，整体还能继续服务的能力  
- 此三个方面最多只能保证其中的两个  

_(CAP theorem is considered incomplete in many papers, an extended version PACELC theorem is preferred)_
  
### AP系统  
- 放弃一致性：最终一致性  
- 可能看到失效的数据  
### CP系统  
- 牺牲可用性  
- 然而保证多个节点之间的一致性非常困难  
### CA系统  
- 没有P就无法跨网络运行，在分布式系统中不存在CA系统  
  
### 服务发现  
- DNS  
- - 标准化，支持多  
- - 部署麻烦  
- 动态服务注册  
- - Zookeeper  
- - Consul  
- - Eureka  
  
### 文档服务  
- Swagger  
- HAL  
- - 超文本应用程序语言  
### 自描述系统  
- UDDI  
- - Universal Description，Discovery，and Integration 通用描述，发现与集成服务  
  
## Chap 12 总结  
### 微服务的原则  
- 围绕业务概念建模  
- 接受自动化文化  
- 隐藏内部实现细节  
- 让一切都去中心化  
- 可独立部署  
- 隔离失败  
- 高度可观察