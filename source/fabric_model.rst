超级账本 Fabric 模型
========================

本章节概述了 超级账本Fabric 的关键功能设计，这些关键的功能设计让超级账本Fabric成为一个全面并可定制的企业区块链解决方案。

* :ref:`Assets` 资产 - 资产的定义能让任何拥有价值的事物通过网络进行交易。食品，古董车甚至货币期货也可以定义为资产。
* :ref:`Chaincode` 链码 - 链码的执行和交易排序被隔离执行，以限制跨节点类型的信任和所需的验证级别，同时优化网络可扩展性和性能。
* :ref:`Ledger-Features` 账本特性 - 不可篡改的共享账本纪录了每个通道加密后的交易历史。账本同时具备于SQL类似的查询功能，让审计和争议解决能有效进行。
* :ref:`Privacy-through-Channels` 以通道保障隐私 - 通道的设计保护了多边交易的隐私和机密性，让竞争企业和受管制的行业在一个共同网络上交换资产。 
* :ref:`Security-Membership-Services` 安全的成员服务 - 授权制的会员资格提供了一个可信任的区块链网络，参与者知道所有交易都可以被授权的监管机构和审核员检测和追踪。
* :ref:`Consensus` 共识机制 - 对共识机制独特的处理方式让超级账本Fabric拥有企业所需要的灵活性和可扩展性。


This section outlines the key design features woven into Hyperledger Fabric that
fulfill its promise of a comprehensive, yet customizable, enterprise blockchain solution:

* :ref:`Assets` - Asset definitions enable the exchange of almost anything with
  monetary value over the network, from whole foods to antique cars to currency
  futures.
* :ref:`Chaincode` - Chaincode execution is partitioned from transaction ordering,
  limiting the required levels of trust and verification across node types, and
  optimizing network scalability and performance.
* :ref:`Ledger-Features` - The immutable, shared ledger encodes the entire
  transaction history for each channel, and includes SQL-like query capability
  for efficient auditing and dispute resolution.
* :ref:`Privacy-through-Channels` - Channels enable multi-lateral transactions
  with the high degrees of privacy and confidentiality required by competing
  businesses and regulated industries that exchange assets on a common network.
* :ref:`Security-Membership-Services` - Permissioned membership provides a
  trusted blockchain network, where participants know that all transactions can
  be detected and traced by authorized regulators and auditors.
* :ref:`Consensus` - a unique approach to consensus enables the
  flexibility and scalability needed for the enterprise.

.. 资产:

资产
------
资产可以为有形（房地产和硬件）或无形资产（合同和知识产权）。 超级账本Fabric提供了使用链码交易修改资产的能力。资产在 超级账本Fabric中 以键值对集合的形式表达，资产的状态更改作为交易记录在对应的通道账本里。资产可以用二进制和/或JSON格式表示和纪录。

你可以通过 `Hyperledger Composer <https://github.com/hyperledger/composer>`__ 这个工具，很容易地在超级账本Fabric里面定义和使用你的资产。

Assets can range from the tangible (real estate and hardware) to the intangible
(contracts and intellectual property).  Hyperledger Fabric provides the
ability to modify assets using chaincode transactions.

Assets are represented in Hyperledger Fabric as a collection of
key-value pairs, with state changes recorded as transactions on a :ref:`Channel`
ledger.  Assets can be represented in binary and/or JSON form.

You can easily define and use assets in your Hyperledger Fabric applications
using the `Hyperledger Composer <https://github.com/hyperledger/composer>`__ tool.

.. 链码:

链码
---------
链码是指包含了一项或多项资产定义，以及所有修改资产交易逻辑的软件。换句话说，链码代表了业务逻辑。 链码限制了被容许执行的读取和更改键值对/


库信息的规则。 链码函数使用当前的stateDB里的数据执行，并通过超级账本Fabric的交易协议启动。 链码执行后会产生一组键值对（写入集），这组键值对会被提交到网络并写入所有Peer节点的账本里。 

Chaincode is software defining an asset or assets, and the transaction instructions for
modifying the asset(s).  In other words, it's the business logic.  Chaincode enforces the rules for reading
or altering key value pairs or other state database information. Chaincode functions execute against
the ledger's current state database and are initiated through a transaction proposal. Chaincode execution
results in a set of key value writes (write set) that can be submitted to the network and applied to
the ledger on all peers.

.. 账本特性:

账本特性
---------------
Fabric账本是所有资产状态数据修改的纪录，账本上的数据是已排序并且防篡改的。状态数据修改是用户调用链码（交易）的直接结果。每个交易都会生成一个资产键值对，这个键值对会成为一个增加，修改或删除的纪录提交到账本里。账本是以区块链（链）的数据结构，把排序并不可篡改的数据纪录到每个区块里，同时以stateDB纪录fabric的当前数据状态。每一个通道有一个独立账本，每个Peer节点都会为自己参与的通道维护和备份该通道的账本。

The ledger is the sequenced, tamper-resistant record of all state transitions in the fabric.  State
transitions are a result of chaincode invocations ('transactions') submitted by participating
parties.  Each transaction results in a set of asset key-value pairs that are committed to the
ledger as creates, updates, or deletes.

The ledger is comprised of a blockchain ('chain') to store the immutable, sequenced record in
blocks, as well as a state database to maintain current fabric state.  There is one ledger per
channel. Each peer maintains a copy of the ledger for each channel of which they are a member.

- 以主键值，键值区间和复合主键查询和更新账本
- 以丰富查询语言执行只读查询（使用CouchDB作为stateDB的情况下）
- 交易的内容包含所有链码已读取的键值对版本（读取集）和所有写入的键值对（写入集）
- 交易包含所有背书节点的加密签名并以提交到排序服务（ordering service）
- 交易被order节点排序，并由排序服务广播到对应通道的Peer节点
- Peer 节点根据背书政策验证交易，并执行背书政策
- 在交易加入区块前，Peer 节点会教验状态数据版本是否在链码执行后有更新，确保交易结果的有效性。
- 一旦交易成功验证并提交到账本后，交易数据就不可篡改
- 每个通道账本都包含一个设定区块，这个设定区块定义了政策，访问权限清单和其他相关信息
- 通道的成员服务（MSP）实例让每个通道可以从不同的证书颁发机构获得加密算法的资料

想了解更多关于账本数据库，存储结构和查询功能的信息，请参考 :doc:`ledger` 文档。

- Query and update ledger using key-based lookups, range queries, and composite key queries
- Read-only queries using a rich query language (if using CouchDB as state database)
- Read-only history queries - Query ledger history for a key, enabling data provenance scenarios
- Transactions consist of the versions of keys/values that were read in chaincode (read set) and keys/values that were written in chaincode (write set)
- Transactions contain signatures of every endorsing peer and are submitted to ordering service
- Transactions are ordered into blocks and are "delivered" from an ordering service to peers on a channel
- Peers validate transactions against endorsement policies and enforce the policies
- Prior to appending a block, a versioning check is performed to ensure that states for assets that were read have not changed since chaincode execution time
- There is immutability once a transaction is validated and committed
- A channel's ledger contains a configuration block defining policies, access control lists, and other pertinent information
- Channel's contain :ref:`MSP` instances allowing for crypto materials to be derived from different certificate authorities

See the :doc:`ledger` topic for a deeper dive on the databases, storage structure, and "query-ability."

.. _以通道保障隐私:

以通道保障隐私
------------------------

超级账本Fabric在每个通道的基础上使用不可篡改的账本以及可以操纵和修改资产当前状态（即更新键值对）的链码。账本只存在于一个通道范围内，它可以在整个网络中共享（假设每个参与者都在一个共同通道上运营）或者可以将其私有化，只包含一组特定的参与者。在后一种情况下，这些参与者将创建一个单独的通道，从而隔离这个通道的交易和账本。为了缩小总体透明度和隐私之间的差距，链码只能安装在需要访问资产状态以执行读取和写入的Peer节点（换句话说，如果链接代码未安装在Peer节点上，它将无法正确地与账本连接）。为了进一步保护数据，链码可以在将交易发送到排序服务（ordering service）并将区块附加到分类账之前，使用常用的加密算法（如AES）对链码中的值进行加密（部分或全部）。一旦将加密数据写入分类帐，只能由拥有对应密钥的用户解密。

更多关于链码加密的信息，请参考 :doc:`chaincode4ade` 文档。

Hyperledger Fabric employs an immutable ledger on a per-channel basis, as well as
chaincodes that can manipulate and modify the current state of assets (i.e. update
key value pairs).  A ledger exists in the scope of a channel - it can be shared
across the entire network (assuming every participant is operating on one common
channel) - or it can be privatized to only include a specific set of participants.

In the latter scenario, these participants would create a separate channel and
thereby isolate/segregate their transactions and ledger.  In order to solve
scenarios that want to bridge the gap between total transparency and privacy,
chaincode can be installed only on peers that need to access the asset states
to perform reads and writes (in other words, if a chaincode is not installed on
a peer, it will not be able to properly interface with the ledger).

To further obfuscate the data, values within chaincode can be encrypted
(in part or in total) using common cryptographic algorithms such as AES before
sending transactions to the ordering service and appending blocks to the ledger.
Once encrypted data has been written to the ledger, it can only be decrypted by
a user in possession of the corresponding key that was used to generate the cipher text.  
For further details on chaincode encryption, see the :doc:`chaincode4ade` topic.

.. 安全的成员服务:

安全的成员服务
------------------------------
超级账本 Fabric 支持一个由已知身份的参与者组成的交易网络。公钥基础建设用于生成与组织，网络成员，用户或客户端的加密证书。数据访问权限因此可以在更广泛的网络和通道级别上进行操纵和管理。 超级账本 Fabric的这种 “授权” 概念，再加上通道的功能，有助于解决隐私和机密性成为首要考量的使用场景。

关于超级账本Fabric的加密功能实现，加密签名，认证和授权的操作，请参考 :doc:`msp` 文档。

Hyperledger Fabric underpins a transactional network where all participants have
known identities.  Public Key Infrastructure is used to generate cryptographic
certificates which are tied to organizations, network components, and end users
or client applications.  As a result, data access control can be manipulated and
governed on the broader network and on channel levels.  This "permissioned" notion
of Hyperledger Fabric, coupled with the existence and capabilities of channels,
helps address scenarios where privacy and confidentiality are paramount concerns.

See the :doc:`msp` topic to better understand cryptographic
implementations, and the sign, verify, authenticate approach used in
Hyperledger Fabric.

.. 共识机制:

共识机制
---------
在分布式账本技术的讨论中，共识机制最近已成为特定算法的同义词，然而共识不仅仅是简单地就交易顺序达成一致。超级账本 Fabric通过其在整个交易流程中的基本角色（从提案和背书，到排序，确认和提交）突出了这种对共识机制理解的差异。简而言之，超级账本Fabric里的共识机制定义为对区块里的交易组正确性的全面验证。

当区块内交易顺序和结果通过政策标准检查时，这个区块的内的数据就能达成共识。这些检查发生在交易的生命周期中，包括使用背书政策来规定哪些特定成员必须认可那些指定的交易类别。这些交易检查还会使用链码以确保策略得到执行和维护。在发布修改之前，Peer节点将使用链码来确保有有效的背书，并且这些背书来源于适当的实体。此外，在包含交易的任何块被提交到账本之前，Peer 节点将进行版本检查，以确认账本的当前状态已获得共识并没有更新。此最终检查可防止双重支出操作以及可能危及数据完整性的其他威胁，并允许针对非静态变量执行功能。

除了背书操作，有效性和版本检查之外，交易流程中还进行大量的身份验证。访问权限控制列表在网络层上实施（由排序服务到通道）。在交易流程中，交易建议在通过不同的架构组件时会被重复地签名和验证。总而言之，共识机制并不仅仅局限于一批交易的共识顺序，一个有效交易在Fabric机制中，通过提案到提交之间的持续核查过程后，共识是一个必然生成的副产品。

请参考可视化的交易流程 :doc:`txflow`，以了解更多关于共识机制的内容。

In distributed ledger technology, consensus has recently become synonymous with
a specific algorithm, within a single function. However, consensus encompasses more
than simply agreeing upon the order of transactions, and this differentiation is
highlighted in Hyperledger Fabric through its fundamental role in the entire
transaction flow, from proposal and endorsement, to ordering, validation and commitment.
In a nutshell, consensus is defined as the full-circle verification of the correctness of
a set of transactions comprising a block.

Consensus is ultimately achieved when the order and results of a block's
transactions have met the explicit policy criteria checks. These checks and balances
take place during the lifecycle of a transaction, and include the usage of
endorsement policies to dictate which specific members must endorse a certain
transaction class, as well as system chaincodes to ensure that these policies
are enforced and upheld.  Prior to commitment, the peers will employ these
system chaincodes to make sure that enough endorsements are present, and that
they were derived from the appropriate entities.  Moreover, a versioning check
will take place during which the current state of the ledger is agreed or
consented upon, before any blocks containing transactions are appended to the ledger.
This final check provides protection against double spend operations and other
threats that might compromise data integrity, and allows for functions to be
executed against non-static variables.

In addition to the multitude of endorsement, validity and versioning checks that
take place, there are also ongoing identity verifications happening in all
directions of the transaction flow.  Access control lists are implemented on
hierarchal layers of the network (ordering service down to channels), and
payloads are repeatedly signed, verified and authenticated as a transaction proposal passes
through the different architectural components.  To conclude, consensus is not
merely limited to the agreed upon order of a batch of transactions, but rather,
it is an overarching characterization that is achieved as a byproduct of the ongoing
verifications that take place during a transaction's journey from proposal to
commitment.

Check out the :doc:`txflow` diagram for a visual representation
of consensus.

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
