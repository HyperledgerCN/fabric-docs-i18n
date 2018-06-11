Hyperledger Fabric Functionalities-Hyperledger Fabric的功能
==================================

Hyperledger Fabric is an implementation of distributed ledger technology
(DLT) that delivers enterprise-ready network security, scalability,
confidentiality and performance, in a modular blockchain architecture.
Hyperledger Fabric delivers the following blockchain network functionalities:

Hyperledger Fabric是分布式账本技术（DLT）的一种实现，它在模块化区块链架构中提供企业级网络安全性，可扩展性，保密性和性能。
Hyperledger Fabric提供以下区块链网络功能：

Identity management-身份管理
-------------------

To enable permissioned networks, Hyperledger Fabric provides a membership
identity service that manages user IDs and authenticates all participants on
the network. Access control lists can be used to provide additional layers of
permission through authorization of specific network operations. For example, a
specific user ID could be permitted to invoke a chaincode application, but
blocked from deploying new chaincode.

为了启用许可的网络，Hyperledger Fabric提供了一种成员身份识别服务，用于管理用户ID并对网络上的所有参与者进行身份验证。
访问控制列表可用于通过授权特定的网络操作来提供额外的权限层。
例如，特定的用户ID可以被允许调用链码应用程序，但被阻止配置新的链码。

Privacy and confidentiality-隐私和保密
---------------------------

Hyperledger Fabric enables competing business interests, and any groups that
require private, confidential transactions, to coexist on the same permissioned
network. Private **channels** are restricted messaging paths that can be used
to provide transaction privacy and confidentiality for specific subsets of
network members. All data, including transaction, member and channel
information, on a channel are invisible and inaccessible to any network members
not explicitly granted access to that channel.

Hyperledger Fabric使相互竞争的商业利益以及任何需要私密交易的群体能够在同一个许可的网络上共存。
私有“通道”是受限的消息传递路径，可用于为特定的网络成员子集提供交易隐私和机密性。
任何未明确授予访问该通道权限的网络成员，都不可见也无法访问该通道所有的数据，包括交易，成员及通道信息。

Efficient processing-高效的处理
--------------------

Hyperledger Fabric assigns network roles by node type. To provide concurrency
and parallelism to the network, transaction execution is separated from
transaction ordering and commitment. Executing transactions prior to
ordering them enables each peer node to process multiple transactions
simultaneously. This concurrent execution increases processing efficiency on
each peer and accelerates delivery of transactions to the ordering service.

Hyperledger Fabric按节点类型分配网络角色。
为了向网络提供并发性和并行性，交易执行与交易排序和提交是分开的。
在给它们排序之前执行交易可使每个对等节点同时处理多个交易。
这种并发执行提高了每个节点的处理效率并加速了向排序服务提供交易。

In addition to enabling parallel processing, the division of labor unburdens
ordering nodes from the demands of transaction execution and ledger
maintenance, while peer nodes are freed from ordering (consensus) workloads.
This bifurcation of roles also limits the processing required for authorization
and authentication; all peer nodes do not have to trust all ordering nodes, and
vice versa, so processes on one can run independently of verification by the
other.

除了支持并行处理之外，分工还可以从交易执行和账本维护的需求中解除排序节点的负担，同时节点从排序（共识）工作负载中解放出来。
角色分工也限制了授权和认证所需的处理; 
所有对等节点不必信任所有的排序节点，反之亦然，因此一个节点上的进程可以独立于另一节点的验证运行。


Chaincode functionality-Chaincode的功能
-----------------------

Chaincode applications encode logic that is
invoked by specific types of transactions on the channel. Chaincode that
defines parameters for a change of asset ownership, for example, ensures that
all transactions that transfer ownership are subject to the same rules and
requirements. **System chaincode** is distinguished as chaincode that defines
operating parameters for the entire channel. Lifecycle and configuration system
chaincode defines the rules for the channel; endorsement and validation system
chaincode defines the requirements for endorsing and validating transactions.

Chaincode应用程序对由通道上特定类型的交易调用的逻辑进行编码。
例如，为资产所有权变更定义参数的链码可确保所有变更所有权的交易都遵守相同的规则和要求。
“system chaincode”区别于Chaincode，它定义了整个通道的工作参数。
Lifecycle和配置system chaincode定义了通道的规则；背书和验证system chaincode定义了批准和验证交易的要求。

Modular design-模块化设计
--------------

Hyperledger Fabric implements a modular architecture to
provide functional choice to network designers. Specific algorithms for
identity, ordering (consensus) and encryption, for example, can be plugged in
to any Hyperledger Fabric network. The result is a universal blockchain
architecture that any industry or public domain can adopt, with the assurance
that its networks will be interoperable across market, regulatory and
geographic boundaries.

Hyperledger Fabric实现了模块化架构，为网络设计者提供了功能选择。
例如，用于身份识别，排序（共识）和加密的特定算法可以插入任何Hyperledger Fabric网络。
其结果是任何行业或公共领域都可以采用的通用区块链架构，并确保其网络能在市场，法规和地理边界中共同使用。


.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
