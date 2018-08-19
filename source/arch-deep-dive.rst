Architecture Explained -- 架构解读
====================================

The Hyperledger Fabric architecture delivers the following advantages:

Hyperledger Fabric架构提供了以下优点：

-  **Chaincode trust flexibility.** The architecture separates *trust
   assumptions* for chaincodes (blockchain applications) from trust
   assumptions for ordering. In other words, the ordering service may be
   provided by one set of nodes (orderers) and tolerate some of them to
   fail or misbehave, and the endorsers may be different for each
   chaincode.


- **链码信任灵活性。** 该架构将对链码（区块链应用）的 *信任设想* 同对排序服务的信任设想分离
  开来。换言之，排序服务是由node集合组成，并容忍其中一些失效或者恶意节点，而对每个
  链码的背书节点可以不同。

-  **Scalability.** As the endorser nodes responsible for particular
   chaincode are orthogonal to the orderers, the system may *scale*
   better than if these functions were done by the same nodes. In
   particular, this results when different chaincodes specify disjoint
   endorsers, which introduces a partitioning of chaincodes between
   endorsers and allows parallel chaincode execution (endorsement).
   Besides, chaincode execution, which can potentially be costly, is
   removed from the critical path of the ordering service.

- **水平扩展性。** 由于特定链码的背书节点和排序节点是正交的，相比于这些功能在同一个节点
  上，系统可以更好地伸缩。尤其是当不同的链码指定不同的背书节点时，相当于引入了背书
  节点间链码的分区，从而允许链码并行地执行（背书）。另外，链码的执行可能非常耗时，
  将其从排序服务这种关键路径上移除。

-  **Confidentiality.** The architecture facilitates deployment of
   chaincodes that have *confidentiality* requirements with respect to
   the content and state updates of its transactions.

-  **保密性。** 考虑交易的内容、状态更新的保密性，该框架便于部署满足这种保密需求的链码。

-  **Consensus modularity.** The architecture is *modular* and allows
   pluggable consensus (i.e., ordering service) implementations.

-  **一致性算法模块化** 该框架是 *模块化* 的，允许插件化一致性算法的实现 (比如，排序服务) 。


**Part I: Elements of the architecture relevant to Hyperledger Fabric
v1**

**章节 I: 该框架中与 Hyperledger Fabric v1 相关的基础**

1. System architecture
2. Basic workflow of transaction endorsement
3. Endorsement policies

1. 系统架构
2. 交易背书的基本流程
3. 背书策略

**Part II: Post-v1 elements of the architecture**

**章节 II: v1版本后新增的框架基础**

4. Ledger checkpointing (pruning)

4. 账本检查点 (裁剪)


1. System architecture -- 系统框架
---------------------------------------------------------------------------------

The blockchain is a distributed system consisting of many nodes that
communicate with each other. The blockchain runs programs called
chaincode, holds state and ledger data, and executes transactions. The
chaincode is the central element as transactions are operations invoked
on the chaincode. Transactions have to be "endorsed" and only endorsed
transactions may be committed and have an effect on the state. There may
exist one or more special chaincodes for management functions and
parameters, collectively called *system chaincodes*.

区块链是一个有许多相互通信的节点组成的分布式系统。区块链通过运行链码程序，进行交易，
持有状态和账本数据。由于交易操作都是通过操作链码实现，链码是最中心的元素。交易必须
被背书，也只有背书过的交易才能被提交从而影响状态。为了实现管理面功能，有一个或多个特殊
的链码，统称为 *系统链码* 。

1.1. Transactions -- 交易
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Transactions may be of two types:

-  *Deploy transactions* create new chaincode and take a program as
   parameter. When a deploy transaction executes successfully, the
   chaincode has been installed "on" the blockchain.

-  *Invoke transactions* perform an operation in the context of
   previously deployed chaincode. An invoke transaction refers to a
   chaincode and to one of its provided functions. When successful, the
   chaincode executes the specified function - which may involve
   modifying the corresponding state, and returning an output.

交易通常有两个类型：

- *部署交易* 把一个程序作为参数创建新的链码。当部署交易成功执行，业务链码也就在
  区块链上安装完成。
- *调用交易* 在上面部署的链码环境中执行操作。一个调用交易需要指向一个链码和它提供的函
  数。如果成功调用，链码会执行指定的函数，可能修改对应的状态，并返回一个结果。

As described later, deploy transactions are special cases of invoke
transactions, where a deploy transaction that creates new chaincode,
corresponds to an invoke transaction on a system chaincode.

正如讨论的，部署交易是调用交易的一个特例，当一个部署交易创建新的链码，对应的是系统链
码的一次调用交易。

**Remark:** *This document currently assumes that a transaction either
creates new chaincode or invokes an operation provided by *one* already
deployed chaincode. This document does not yet describe: a)
optimizations for query (read-only) transactions (included in v1), b)
support for cross-chaincode transactions (post-v1 feature).*

**备注:**  *这个文档一般假定一个交易都是从一个已经部署的链码上创建新的链码或者进行调用操作。该文档也不讨论
：a）一个查询（只读）交易的优化（包含版本v1），b）支持跨链交易（v1之后版本特性)*


1.2. Blockchain datastructures -- 区块链数据结构
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1.2.1. State -- 状态
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The latest state of the blockchain (or, simply, *state*) is modeled as a
versioned key/value store (KVS), where keys are names and values are
arbitrary blobs. These entries are manipulated by the chaincodes
(applications) running on the blockchain through ``put`` and ``get``
KVS-operations. The state is stored persistently and updates to the
state are logged. Notice that versioned KVS is adopted as state model,
an implementation may use actual KVSs, but also RDBMSs or any other
solution.

区块链的最新状态(或简称为状态)建模为版本化的key/value存储（KVS），key是名字而values是
任意的文本。这些记录通过运行区块链上的 ``put`` 和 ``get`` 的kvs操作来改变。这些状态被持久化
存储，更新操作记录于日志。注意版本化的KVS只是状态模型，而实际的实现可能是KVS存储，也
可能是RDBMSs，或者其他解决方案。

More formally, state ``s`` is modeled as an element of a mapping
``K -> (V X N)``, where:

-  ``K`` is a set of keys
-  ``V`` is a set of values
-  ``N`` is an infinite ordered set of version numbers. Injective
   function ``next: N -> N`` takes an element of ``N`` and returns the
   next version number.

正式地表达，状态 ``s`` 被建模为一个字典的元素 ``K -> (V X N)`` ：

- ``K`` 是keys的集合
- ``V`` 是values的集合
- ``N`` 是排序的版本号的无限集合。单映射函数 ``next: N -> N`` ，根据元素 ``N`` 返回下一个版本号。

Both ``V`` and ``N`` contain a special element |falsum| (empty type), which is
in case of ``N`` the lowest element. Initially all keys are mapped to 
(|falsum|, |falsum|). For ``s(k)=(v,ver)`` we denote ``v`` by ``s(k).value``,
and ``ver`` by ``s(k).version``.


``V`` 和 ``N`` 都包含了特殊的元素 |falsum| (空类型)，这个元素是N最小的那个元素。初始化时所有的keys
都映射到(|falsum|, |falsum|)。对于 ``s(k)=(v,ver)`` ，我们能推导出 ``v=s(k).value`` ，而 ``ver=s(k).version`` 。

.. |falsum| unicode:: U+22A5
.. |in| unicode:: U+2208


KVS operations are modeled as follows:

-  ``put(k,v)`` for ``k`` |in| ``K`` and ``v`` |in| ``V``, takes the blockchain
   state ``s`` and changes it to ``s'`` such that
   ``s'(k)=(v,next(s(k).version))`` with ``s'(k')=s(k')`` for all
   ``k'!=k``.
-  ``get(k)`` returns ``s(k)``.

KVS的操作如下建模：

-  ``put(k,v)`` 对于 ``k`` |in| ``K`` 和 ``v`` |in| ``V``，将blockchain的状态 ``s`` 改变为
   ``s'`` ，``s'(k)=(v,next(s(k).version))`` ，而对于其他 ``k'!=k`` 有 ``s'(k')=s(k')`` 。
-  ``get(k)`` 返回 ``s(k)``.


State is maintained by peers, but not by orderers and clients.

状态由peer维护，排序节点和客户端不维护。

**State partitioning.** Keys in the KVS can be recognized from their
name to belong to a particular chaincode, in the sense that only
transaction of a certain chaincode may modify the keys belonging to this
chaincode. In principle, any chaincode can read the keys belonging to
other chaincodes. *Support for cross-chaincode transactions, that modify
the state belonging to two or more chaincodes is a post-v1 feature.*

**状态分区。** KVS中的keys通过所属链码的名称来识别，也意味着只有特定链码的交易能修改属于
这个链码的keys。原则上，任何链码都能读取属于其他链码的keys。*在v1版本之后的特性中，支
持跨链交易，即修改属于两个以上链码的状态*

1.2.2 Ledger -- 账本
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ledger provides a verifiable history of all successful state changes (we
talk about *valid* transactions) and unsuccessful attempts to change
state (we talk about *invalid* transactions), occurring during the
operation of the system.

账本提供了在系统操作过程中，所有成功的状态变化(我们讨论的是 *有效* 交易)和未成功的状态改
变尝试（我们讨论的是 *无效* 交易）可验证的历史记录。

Ledger is constructed by the ordering service (see Sec 1.3.3) as a
totally ordered hashchain of *blocks* of (valid or invalid)
transactions. The hashchain imposes the total order of blocks in a
ledger and each block contains an array of totally ordered transactions.
This imposes total order across all transactions.

账本作为一个完全排序好的包含交易（有效或者无效）信息的哈希链 *区块* ，是由排序服务构建
的（参考章节1.3.3）。这个哈希链强制账本中的区块是完全有序的，区块中包含的交易列表也是
完全有序的。这使得所有的交易都是排序好的。

Ledger is kept at all peers and, optionally, at a subset of orderers. In
the context of an orderer we refer to the Ledger as to
``OrdererLedger``, whereas in the context of a peer we refer to the
ledger as to ``PeerLedger``. ``PeerLedger`` differs from the
``OrdererLedger`` in that peers locally maintain a bitmask that tells
apart valid transactions from invalid ones (see Section XX for more
details).

账本保存在所有的peer节点上，以及一部分排序节点上。在order节点上，我们将账本称为
``OrdererLedger``，在peer节点上我们称为 ``PeerLedger`` 。 ``PeerLedger`` 和 ``OrdererLedger`` 的区别
在于peer本地维护了一个bitmask来区分有效交易和无效交易（详情参看章节XX）。

Peers may prune ``PeerLedger`` as described in Section XX (post-v1
feature). Orderers maintain ``OrdererLedger`` for fault-tolerance and
availability (of the ``PeerLedger``) and may decide to prune it at
anytime, provided that properties of the ordering service (see Sec.
1.3.3) are maintained.

Peer可能如章节XX(V1后特性)所说，会裁剪 ``PeerLedger`` 。``OrdererLedger`` 提供了排序服务需要
维护的一些特性(参考章节1.3.3)，排序节点为了容错和可用性而维护 ``OrdererLedger``
，但也可能随时裁剪它。

The ledger allows peers to replay the history of all transactions and to
reconstruct the state. Therefore, state as described in Sec 1.2.1 is an
optional datastructure.

账本允许节点重放所有交易的历史记录来构建状态。因此，章节1.2.1描述的状态是一个可选的数
据结构。

1.3. Nodes -- 节点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nodes are the communication entities of the blockchain. A "node" is only
a logical function in the sense that multiple nodes of different types
can run on the same physical server. What counts is how nodes are
grouped in "trust domains" and associated to logical entities that
control them.

节点是区块链中的通信实体。一个节点只是一个逻辑函数概念，实际上不同类型多种节点可以运
行在同一个物理服务器上。重要的是节点是如何在"信任域"内分组以及如何和控制他们的逻辑实
体关联的。

There are three types of nodes:

有三种类型的节点：

1. **Client** or **submitting-client**: a client that submits an actual
   transaction-invocation to the endorsers, and broadcasts
   transaction-proposals to the ordering service.

1. **Client** 或者 **submitting-client** ：客户端实际提交交易调用给背书节点，然后广播交易提案给
   排序服务。

2. **Peer**: a node that commits transactions and maintains the state
   and a copy of the ledger (see Sec, 1.2). Besides, peers can have a
   special **endorser** role.

2. **Peer**: 负责提交交易、通过账本拷贝来维护状态的节点。另外peer有一种特殊的 *背书节点* 的角色。

3. **Ordering-service-node** or **orderer**: a node running the
   communication service that implements a delivery guarantee, such as
   atomic or total order broadcast.

3. **Ordering-service-node** 或者 **orderer**: 通信系统中运行的实现了某种传递保证的节点，比如实
   现原子或者完全有序的广播。

The types of nodes are explained next in more detail.

接下来将详细介绍各个类型的节点。


1.3.1. Client -- 客户端
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The client represents the entity that acts on behalf of an end-user. It
must connect to a peer for communicating with the blockchain. The client
may connect to any peer of its choice. Clients create and thereby invoke
transactions.

客户端是代表了终端用户的实体。它必须通过连接一个peer来和区块链通信。客户端可以选择连
接任意peer。客户端创建交易请求，之后再通过链码调用。

As detailed in Section 2, clients communicate with both peers and the
ordering service.

如在章节2中详细描述，客户端同时会和peer节点及排序服务通信。

1.3.2. Peer -- Peer节点
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A peer receives ordered state updates in the form of *blocks* from the
ordering service and maintain the state and the ledger.

一个peer节点从排序服务接收到 *区块* ，写入到账本，更新并维护状态。

Peers can additionally take up a special role of an **endorsing peer**,
or an **endorser**. The special function of an *endorsing peer* occurs
with respect to a particular chaincode and consists in *endorsing* a
transaction before it is committed. Every chaincode may specify an
*endorsement policy* that may refer to a set of endorsing peers. The
policy defines the necessary and sufficient conditions for a valid
transaction endorsement (typically a set of endorsers' signatures), as
described later in Sections 2 and 3. In the special case of deploy
transactions that install new chaincode the (deployment) endorsement
policy is specified as an endorsement policy of the system chaincode.

Peer节点另外承担了 **endorsing peer** 或者 **endorser** 的角色。在一个交易被提交前，*endorsing peer*
的特殊功能对特定的链码就能派上用场。每个链码都会指定一个 *背书策略* ，指向一系列的
背书节点。这个策略定义了一个有效背书（通常是一系列背书者的签名）的必要充足条件，将在
后续章节2和3详细讨论。一个特殊的例子是安装新的链码的部署交易，它的背书策略是系统链码
的背书策略。

1.3.3. Ordering service nodes (Orderers) -- 排序服务节点（Orderers）
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The *orderers* form the *ordering service*, i.e., a communication fabric
that provides delivery guarantees. The ordering service can be
implemented in different ways: ranging from a centralized service (used
e.g., in development and testing) to distributed protocols that target
different network and node fault models.

*ordering service* 简称 *orderers* ，一个提供传递保证的通信实体。排序服务可以通过不同的方式实
现：从集中式服务（用在开发和测试）到用在不同网络和节点故障模型中的分布式协议。

Ordering service provides a shared *communication channel* to clients
and peers, offering a broadcast service for messages containing
transactions. Clients connect to the channel and may broadcast messages
on the channel which are then delivered to all peers. The channel
supports *atomic* delivery of all messages, that is, message
communication with total-order delivery and (implementation specific)
reliability. In other words, the channel outputs the same messages to
all connected peers and outputs them to all peers in the same logical
order. This atomic communication guarantee is also called *total-order
broadcast*, *atomic broadcast*, or *consensus* in the context of
distributed systems. The communicated messages are the candidate
transactions for inclusion in the blockchain state.

排序服务为peer节点和客户端提供了一个共享的 *通信通道* ，用来广播包含交易的信息。客户端连
接到通道并在通道中广播消息，之后这些消息会传递给所有的peer节点。这个通道支持消息的 *原子*
传递，也即消息的传递是完全有序、可靠的（特殊实现）。换言之，通道对于所有的连接节点
输出相同逻辑顺序、完全相同的消息。这个原子通信保障也成为 *完全有序广播* ， *原子广播* ，或
者分布式系统中的 *一致性* 。这个通信消息是将包含在区块状态的候选交易。

**Partitioning (ordering service channels).** Ordering service may
support multiple *channels* similar to the *topics* of a
publish/subscribe (pub/sub) messaging system. Clients can connect to a
given channel and can then send messages and obtain the messages that
arrive. Channels can be thought of as partitions - clients connecting to
one channel are unaware of the existence of other channels, but clients
may connect to multiple channels. Even though some ordering service
implementations included with Hyperledger Fabric support multiple
channels, for simplicity of presentation, in the rest of this
document, we assume ordering service consists of a single channel/topic.

**分区(排序服务通道)。** 排序服务支持多 *通道* ，就像发布/订阅系统中的 *主题* 一样。客户端连接到
给定的通道上，然后发送消息，接受到达的消息。通道可以想象为一个分区，客户端可以连接到
一个通道上而不感知其他通道的存在，当然客户端也可以连接到多个通道上。Hyperledger
Fabric的有些排序服务实现支持多通道，但是为了简单演示，我们在接下来部分假设排序服务只
包含一个通道/主题。

**Ordering service API.** Peers connect to the channel provided by the
ordering service, via the interface provided by the ordering service.
The ordering service API consists of two basic operations (more
generally *asynchronous events*):

**排序服务API。** peer通过排序服务提供的接口连接到排序服务提供的通道上。排序服务API包含以
下两个基本操作（更一般称为 *异步事件* ）

**TODO** add the part of the API for fetching particular blocks under
client/peer specified sequence numbers.

**TODO** 增加用client/peer获取特定序号区块的部分API。

-  ``broadcast(blob)``: a client calls this to broadcast an arbitrary
   message ``blob`` for dissemination over the channel. This is also
   called ``request(blob)`` in the BFT context, when sending a request
   to a service.


-  ``broadcast(blob)``：一个客户端调用该接口在通道内去广播任意消息 ``blob`` 。
   当想一个服务发送一个请求时，在BFT里我们又称之为 ``request(blob)`` 。

-  ``deliver(seqno, prevhash, blob)``: the ordering service calls this
   on the peer to deliver the message ``blob`` with the specified
   non-negative integer sequence number (``seqno``) and hash of the most
   recently delivered blob (``prevhash``). In other words, it is an
   output event from the ordering service. ``deliver()`` is also
   sometimes called ``notify()`` in pub-sub systems or ``commit()`` in
   BFT systems.

-  ``deliver(seqno, prevhash, blob)`` : 排序服务指定一个非负序列号（ ``seqno`` ）和最近传递的
   blob（ ``prevhash`` 和本次传递的消息 ``blob`` 来调用该接口。换言之，这个排序服务的输出事件
   。 ``deliver()`` 有时在发布/订阅系统中也称为 ``notify()`` ，在BFD系统中也称为 ``commit()`` 。


**Ledger and block formation.** The ledger (see also Sec. 1.2.2)
contains all data output by the ordering service. In a nutshell, it is a
sequence of ``deliver(seqno, prevhash, blob)`` events, which form a hash
chain according to the computation of ``prevhash`` described before.

**账本和区块格式。** 账本（参考章节1.2.2）包含了排序服务的所有输出。概况地说，它是一系列
``deliver(seqno, prevhash, blob)`` 事件，依据计算之前讨论的 ``prevhash`` 构建哈希链。

Most of the time, for efficiency reasons, instead of outputting
individual transactions (blobs), the ordering service will group (batch)
the blobs and output *blocks* within a single ``deliver`` event. In this
case, the ordering service must impose and convey a deterministic
ordering of the blobs within each block. The number of blobs in a block
may be chosen dynamically by an ordering service implementation.

大部分情况下，考虑到效率，排序服务会给blobs打包然后输出 *区块* 到一个 ``deliver`` 事件中，
而不是输出一个个的交易（blobs）。这种情况下，排序服务必须强制每个区块中的blobs是确定有
序的。区块中blob的数量根据不同的排序服务的实现而动态选择。

In the following, for ease of presentation, we define ordering service
properties (rest of this subsection) and explain the workflow of
transaction endorsement (Section 2) assuming one blob per ``deliver``
event. These are easily extended to blocks, assuming that a ``deliver``
event for a block corresponds to a sequence of individual ``deliver``
events for each blob within a block, according to the above mentioned
deterministic ordering of blobs within a blocs.

接下来为了便于演示，我们定义排序服务特性(文章的剩余章节)并且解释交易背书的流程(假设一个blob一
次 ``deliver`` 事件)。这些可以简单的扩展到区块，根据上述的区块中每个blobs确定规则
的前提，假设区块响应的一次 ``deliver`` 事件对应一系列的区块中每一个blob的单独 ``deliver`` 事
件。

**Ordering service properties** -- **排序服务特性**

The guarantees of the ordering service (or atomic-broadcast channel)
stipulate what happens to a broadcasted message and what relations exist
among delivered messages. These guarantees are as follows:

排序服务保证（通道内原子广播）规定了如何广播消息，以及传递的消息之间的关联性。有如下
保证：

1. **Safety (consistency guarantees)**: As long as peers are connected
   for sufficiently long periods of time to the channel (they can
   disconnect or crash, but will restart and reconnect), they will see
   an *identical* series of delivered ``(seqno, prevhash, blob)``
   messages. This means the outputs (``deliver()`` events) occur in the
   *same order* on all peers and according to sequence number and carry
   *identical content* (``blob`` and ``prevhash``) for the same sequence
   number. Note this is only a *logical order*, and a
   ``deliver(seqno, prevhash, blob)`` on one peer is not required to
   occur in any real-time relation to ``deliver(seqno, prevhash, blob)``
   that outputs the same message at another peer. Put differently, given
   a particular ``seqno``, *no* two correct peers deliver *different*
   ``prevhash`` or ``blob`` values. Moreover, no value ``blob`` is
   delivered unless some client (peer) actually called
   ``broadcast(blob)`` and, preferably, every broadcasted blob is only
   delivered *once*.

   Furthermore, the ``deliver()`` event contains the cryptographic hash
   of the data in the previous ``deliver()`` event (``prevhash``). When
   the ordering service implements atomic broadcast guarantees,
   ``prevhash`` is the cryptographic hash of the parameters from the
   ``deliver()`` event with sequence number ``seqno-1``. This
   establishes a hash chain across ``deliver()`` events, which is used
   to help verify the integrity of the ordering service output, as
   discussed in Sections 4 and 5 later. In the special case of the first
   ``deliver()`` event, ``prevhash`` has a default value.

1. **安全性（一致性保证）**: 只要peer节点和通道保持足够长的连接（它们可以断连或者奔溃，但
   是能够重启和重连），它们最终看到一系列 *一致* 的传递的 ``(seqno, prevhash, blob)`` 消息。
   这意味着在所有peer上的输出（``deliver()`` 事件）是一样的顺序和序列号，同时内容也是完全
   一致的。注意这里只是 *逻辑顺序* ，在真实时间线上，peer节点上的 ``deliver(seqno, prevhash, blob)``
   消息并不需要和其他节点接受到的顺序完全相同。换句话
   说，给定一个 ``seqno`` ， *没有* 两个正确的节点会传递 *不同* 的 ``prevhash`` 或者 ``blob`` 值。没有一个值 ``blob`` 会被传递，
   除非被一些客户端（peer）调用了 ``broadcast(blob)`` ，每个广播的blob最好只传递 *一次* 。

   更进一步， ``deliver()`` 事件包含之前 ``deliver()`` 事件的哈希值。 ``prevhash`` 的值是序列号为
   ``seqno-1`` 的 ``deliver()`` 事件的哈希值，这是排序服务实现原子广播的保证。这可以在不同的
   ``deliver()`` 事件中建立一个链，哈希值可以用来验证排序服务输出的完整性，在之后章节4和
   5中将详细讨论。特殊情况是第一个 ``deliver()`` 事件，它的 ``prevhash`` 有一个默认值。

2. **Liveness (delivery guarantee)**: Liveness guarantees of the
   ordering service are specified by a ordering service implementation.
   The exact guarantees may depend on the network and node fault model.

   In principle, if the submitting client does not fail, the ordering
   service should guarantee that every correct peer that connects to the
   ordering service eventually delivers every submitted transaction.

2. **活性（传递保证）**：排序服务的活性保证根据排序服务的实现而不同。准确的保证和网络及节
   点故障模型相关。

   原则上，如果客户端提交不失败，排序服务应该保证每个连接到排序服务的peer节点最终都能
   收到每个提交的交易。

To summarize, the ordering service ensures the following properties:

总而言之，排序服务确保了以下的特性：

-  *Agreement.* For any two events at correct peers
   ``deliver(seqno, prevhash0, blob0)`` and
   ``deliver(seqno, prevhash1, blob1)`` with the same ``seqno``,
   ``prevhash0==prevhash1`` and ``blob0==blob1``;
-  *一致性。* 对于正确peer的两个事件， ``deliver(seqno, prevhash0, blob0)`` 和 ``deliver(seqno, prevhash1, blob1)`` ，
   具有相同的序列号，有 ``prevhash0==prevhash1`` 且 ``blob0==blob1`` 。
-  *Hashchain integrity.* For any two events at correct peers
   ``deliver(seqno-1, prevhash0, blob0)`` and
   ``deliver(seqno, prevhash, blob)``,
   ``prevhash = HASH(seqno-1||prevhash0||blob0)``.
-  *哈希链完整性。* 对于两个peer节点的任意两个事件 ``deliver(seqno-1, prevhash0, blob0)`` 和 ``deliver(seqno, prevhash, blob)``
   有 ``prevhash = HASH(seqno-1||prevhash0||blob0)`` 。
-  *No skipping*. If an ordering service outputs
   ``deliver(seqno, prevhash, blob)`` at a correct peer *p*, such that
   ``seqno>0``, then *p* already delivered an event
   ``deliver(seqno-1, prevhash0, blob0)``.
-  *无跳跃* 。 如果排序服务给正确的peer *p* 节点输出 ``deliver(seqno, prevhash, blob)`` ，其中 ``seqno>0`` ，那么 *p*
   节点已经接收到事件 ``deliver(seqno-1, prevhash0, blob0)``。
-  *No creation*. Any event ``deliver(seqno, prevhash, blob)`` at a
   correct peer must be preceded by a ``broadcast(blob)`` event at some
   (possibly distinct) peer;
-  *无创建* 。peer节点上的任何事件 ``deliver(seqno, prevhash, blob)`` 必须是同一个（可能其他）peer ``broadcast(blob)`` 的结果。
-  *No duplication (optional, yet desirable)*. For any two events
   ``broadcast(blob)`` and ``broadcast(blob')``, when two events
   ``deliver(seqno0, prevhash0, blob)`` and
   ``deliver(seqno1, prevhash1, blob')`` occur at correct peers and
   ``blob == blob'``, then ``seqno0==seqno1`` and
   ``prevhash0==prevhash1``.
-  *不重复性（可选，但是希望有）* 。对于任意两个事件 ``broadcast(blob)`` 和 ``broadcast(blob')`` ，正确peer
   节点上的 ``deliver(seqno0, prevhash0, blob)`` 和 ``deliver(seqno1, prevhash1, blob')`` ，有 ``blob == blob'``，
   ``seqno0==seqno1`` 和 ``prevhash0==prevhash1``。
-  *Liveness*. If a correct client invokes an event ``broadcast(blob)``
   then every correct peer "eventually" issues an event
   ``deliver(*, *, blob)``, where ``*`` denotes an arbitrary value.
-  *活性*。当一个客户端正确地调用事件 ``broadcast(blob)`` ，每个正确的peer节点"最终"接受到事件 ``deliver(*, *, blob)`` ， ``*``
   表示任意值。

2. Basic workflow of transaction endorsement - 交易背书基本流程
---------------------------------------------------------------------------------

In the following we outline the high-level request flow for a
transaction.

接下来我们会从比较高层次大致演示一个交易的基本流程。

**Remark:** *Notice that the following protocol *does not* assume that
all transactions are deterministic, i.e., it allows for
non-deterministic transactions.*

**备注：** *注意接下来的协议不假设所有的交易都是确定性的，它允许不确定性的交易*

2.1. The client creates a transaction and sends it to endorsing peers of its choice -- 客户端创建交易并发送到所选的背书节点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To invoke a transaction, the client sends a ``PROPOSE`` message to a set
of endorsing peers of its choice (possibly not at the same time - see
Sections 2.1.2. and 2.3.). The set of endorsing peers for a given
``chaincodeID`` is made available to client via peer, which in turn
knows the set of endorsing peers from endorsement policy (see Section
3). For example, the transaction could be sent to *all* endorsers of a
given ``chaincodeID``. That said, some endorsers could be offline,
others may object and choose not to endorse the transaction. The
submitting client tries to satisfy the policy expression with the
endorsers available.

为了调用交易接口，客户端发送一个 ``提案`` 消息到它选择的背书节点集（也许不是同时进行，参
考章节2.1.2和2.1.3）。peer节点通过背书策略（参考章节3）知道背书节点集合，客户端通过连
接peer来获取特定 ``链码ID`` 的背书节点集。比如，给定 ``链码ID`` 的交易可以发送到 *所有的* 背书节
点。有些背书节点离线，另外一些可能拒绝或者不对交易进行背书。提交客户端通过那些可用的
背书节点尝试去满足策略描述的规则。

In the following, we first detail ``PROPOSE`` message format and then
discuss possible patterns of interaction between submitting client and
endorsers.

接下来，我们首先介绍下 ``提案`` 的格式和详情，然后讨论提交客户端和背书节点间可能的交互模式。

2.1.1. ``PROPOSE`` message format -- ``提案`` 消息格式
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The format of a ``PROPOSE`` message is ``<PROPOSE,tx,[anchor]>``, where
``tx`` is a mandatory and ``anchor`` optional argument explained in the
following.

``提案`` 消息的格式是：``<PROPOSE,tx,[anchor]>``，``tx`` 是必须有的，``anchor`` 是一个可选参数，
解释如下。

-  ``tx=<clientID,chaincodeID,txPayload,timestamp,clientSig>``, where

   -  ``clientID`` is an ID of the submitting client,
   -  ``chaincodeID`` refers to the chaincode to which the transaction
      pertains,
   -  ``txPayload`` is the payload containing the submitted transaction
      itself,
   -  ``timestamp`` is a monotonically increasing (for every new
      transaction) integer maintained by the client,
   -  ``clientSig`` is signature of a client on other fields of ``tx``.

   -  ``clientID`` 是提交客户端的ID，
   -  ``chaincodeID`` 指向交易所属的链码，
   -  ``txPayload`` 负载包含了提交的交易内容本身，
   -  ``timestamp`` 由客户端维护的一个单调递增（对每个交易）的整数，
   -  ``clientSig`` 是客户端对 ``tx`` 之外的其他属性的签名。

   The details of ``txPayload`` will differ between invoke transactions
   and deploy transactions (i.e., invoke transactions referring to a
   deploy-specific system chaincode). For an **invoke transaction**,
   ``txPayload`` would consist of two fields

   ``txPayload`` 的详情根据是调用交易还是部署交易有所不同（比如调用交易指向一个部署类型的系统链码）。
   对于 **调用交易** ，``txPayload`` 会包含以下两个属性：

   -  ``txPayload = <operation, metadata>``, where

      -  ``operation`` denotes the chaincode operation (function) and
         arguments,
      -  ``metadata`` denotes attributes related to the invocation.

   -  ``txPayload = <operation, metadata>`` , 其中

      -  ``operation`` 表示链码的操作（函数）和参数。
      -  ``metadata`` 表示和调用相关的属性。

   For a **deploy transaction**, ``txPayload`` would consist of three
   fields

   对于 **部署交易** ，``txPayload`` 会包含以下三个属性

   -  ``txPayload = <source, metadata, policies>``, where

      -  ``source`` denotes the source code of the chaincode,
      -  ``metadata`` denotes attributes related to the chaincode and
         application,
      -  ``policies`` contains policies related to the chaincode that
         are accessible to all peers, such as the endorsement policy.
         Note that endorsement policies are not supplied with
         ``txPayload`` in a ``deploy`` transaction, but
         ``txPayload`` of a ``deploy`` contains endorsement policy ID and
         its parameters (see Section 3).

   -  ``txPayload = <source, metadata, policies>``, 其中

      -  ``source`` 表示链码的源码位置，
      -  ``metadata`` 表示和链码和应用相关的属性，
      -  ``policies`` 包含和链码相关的策略，对所有peer节点可见，比如背书策略。注意在部署
         交易中背书策略不包含 ``txPayload`` ，但是 ``deploy`` 的 ``txPayload`` 包含背书策略ID和他
         的参数（参考章节3）。

-  ``anchor`` contains *read version dependencies*, or more
   specifically, key-version pairs (i.e., ``anchor`` is a subset of
   ``KxN``), that binds or "anchors" the ``PROPOSE`` request to
   specified versions of keys in a KVS (see Section 1.2.). If the client
   specifies the ``anchor`` argument, an endorser endorses a transaction
   only upon *read* version numbers of corresponding keys in its local
   KVS match ``anchor`` (see Section 2.2. for more details).

- ``anchor`` 包含 *读版本依赖* ，或者准确地说，key-version对(比如，``anchor`` 是 ``KxN`` 的子集)。
  这样就绑定或者锚定了 ``提案`` 请求和KVS中特定版本的keys（参考章节1.2）。如果客户端指定
  了 ``anchor`` 参数，背书节点只有在锚的 *读* 版本号和本地的KVS匹配上时，才会背书一个交易。

Cryptographic hash of ``tx`` is used by all nodes as a unique
transaction identifier ``tid`` (i.e., ``tid=HASH(tx)``). The client
stores ``tid`` in memory and waits for responses from endorsing peers.

``tx`` 的哈希值被所用节点用作交易的身份表示 ``tid`` （比如 ``tid=HASH(tx)``）。客户端在内存中存
储了 ``tid`` ，并且等待背书节点的响应。

2.1.2. Message patterns -- 消息模式
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The client decides on the sequence of interaction with endorsers. For
example, a client would typically send ``<PROPOSE, tx>`` (i.e., without
the ``anchor`` argument) to a single endorser, which would then produce
the version dependencies (``anchor``) which the client can later on use
as an argument of its ``PROPOSE`` message to other endorsers. As another
example, the client could directly send ``<PROPOSE, tx>`` (without
``anchor``) to all endorsers of its choice. Different patterns of
communication are possible and client is free to decide on those (see
also Section 2.3.).

客户端决定和背书节点进行一系列的交互。比如，典型地一个客户端发送
``<PROPOSE, tx>`` （比如没有带 ``anchor`` 参数）给一个单独的背书节点，然后会产生版本依赖
（ ``anchor`` ），客户端之后用来添加到 ``提案`` 消息中，然后发送给其他的背书节点。另外一种例
子，背书节点直接发送 ``<PROPOSE, tx>`` （不带有 ``anchor`` ）给所有其他它选择的节点。不同的通
信模式都是许可的，客户端可以自行决定（参考章节2.3）。

2.2. The endorsing peer simulates a transaction and produces an endorsement signature -- 背书节点模拟交易并添加背书签名
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On reception of a ``<PROPOSE,tx,[anchor]>`` message from a client, the
endorsing peer ``epID`` first verifies the client's signature
``clientSig`` and then simulates a transaction. If the client specifies
``anchor`` then endorsing peer simulates the transactions only upon read
version numbers (i.e., ``readset`` as defined below) of corresponding
keys in its local KVS match those version numbers specified by
``anchor``.

从客户端接受到一个 ``<PROPOSE,tx,[anchor]>`` 消息，背书节点 ``epID`` 首先验证客户端的签名
``clientSig`` ，然后模拟交易。如果客户端指定了 ``anchor`` ，背书节点只需要通过读 ``anchor`` 的
keys的版本号是否和本地的KVS的版本号匹配，

Simulating a transaction involves endorsing peer tentatively *executing*
a transaction (``txPayload``), by invoking the chaincode to which the
transaction refers (``chaincodeID``) and the copy of the state that the
endorsing peer locally holds.

通过调用交易中指定的 ``链码Id`` ，引用背书节点本地的状态拷贝，可以实验性地在背书节点上
*执行* 交易（ ``txPayload`` ），

As a result of the execution, the endorsing peer computes *read version
dependencies* (``readset``) and *state updates* (``writeset``), also
called *MVCC+postimage info* in DB language.

执行的结果是背书节点计算出 *读版本依赖* （ ``读集`` ）和 *状态更新* （ ``写集`` ），在db语言中也称为
*MVCC+psotimage info*。

Recall that the state consists of key/value (k/v) pairs. All k/v entries
are versioned, that is, every entry contains ordered version
information, which is incremented every time when the value stored under
a key is updated. The peer that interprets the transaction records all
k/v pairs accessed by the chaincode, either for reading or for writing,
but the peer does not yet update its state. More specifically:

曾提过状态包括key/value（k/v）对。所有的k/v对都是版本化的，即每个记录都是包含有序的版
本信息的，每次key对应存储的value值更新，都会递增key的版本号。无论是读还是写，peer节
点把交易的条目都翻译成链码能访问的k/v对，但是没有更新peer的状态。更具体的：

-  Given state ``s`` before an endorsing peer executes a transaction,
   for every key ``k`` read by the transaction, pair
   ``(k,s(k).version)`` is added to ``readset``.
-  给定背书节点执行交易前的状态 ``s`` ，对于每个交易中读取的key ``k`` ，``(k,s(k).version)`` 会
   添加到 ``读集`` 中。
-  Additionally, for every key ``k`` modified by the transaction to the
   new value ``v'``, pair ``(k,v')`` is added to ``writeset``.
   Alternatively, ``v'`` could be the delta of the new value to previous
   value (``s(k).value``).
-  另外，对于交易中每个key ``k`` 修改成新的值 ``v'`` , ``(k,v')`` 对添加到 ``写集``。或者，``v'`` 可以
   是原来value ( ``s(k).value`` )的差异值。

If a client specifies ``anchor`` in the ``PROPOSE`` message then client
specified ``anchor`` must equal ``readset`` produced by endorsing peer
when simulating the transaction.

如果一个客户端在 ``提案`` 消息中指定了 ``anchor`` ，name客户端指定的 ``anchor`` 必须和背书节点模
拟交易中产生的 ``读集`` 相等。

Then, the peer forwards internally ``tran-proposal`` (and possibly
``tx``) to the part of its (peer's) logic that endorses a transaction,
referred to as **endorsing logic**. By default, endorsing logic at a
peer accepts the ``tran-proposal`` and simply signs the
``tran-proposal``. However, endorsing logic may interpret arbitrary
functionality, to, e.g., interact with legacy systems with
``tran-proposal`` and ``tx`` as inputs to reach the decision whether to
endorse a transaction or not.

然后，peer节点转发内部的 ``交易提案`` （也叫 ``tx`` ）到节点的其他逻辑去背书交易，称为 **背书逻
辑** 。默认情况下，peer节点的背书逻辑接受 ``交易提案`` 然后简单签了名。然而，背书逻辑可能执
行任意功能，比如把 ``交易提案`` （ ``tx`` ）作为输入和账本系统进行交互，决定是否背书这个交易
等。

If endorsing logic decides to endorse a transaction, it sends
``<TRANSACTION-ENDORSED, tid, tran-proposal,epSig>`` message to the
submitting client(\ ``tx.clientID``), where:

如果背书逻辑决定背书这个交易，会发送 ``<TRANSACTION-ENDORSED, tid, tran-proposal,epSig>`` 消
息到提交客户端（ ``tx.clientID`` ），其中：

-  ``tran-proposal := (epID,tid,chaincodeID,txContentBlob,readset,writeset)``,

   where ``txContentBlob`` is chaincode/transaction specific
   information. The intention is to have ``txContentBlob`` used as some
   representation of ``tx`` (e.g., ``txContentBlob=tx.txPayload``).
-  ``tran-proposal := (epID,tid,chaincodeID,txContentBlob,readset,writeset)`` ,
   其中 ``txContentBlob`` 是链码/交易特有的信息。它的目的是用来表示 ``tx`` （比如
   ``xContentBlob=tx.txPayload`` ）。
-  ``epSig`` is the endorsing peer's signature on ``tran-proposal``
-  ``epSig`` 是背书节点对 ``tran-proposal`` 的签名。


Else, in case the endorsing logic refuses to endorse the transaction, an
endorser *may* send a message ``(TRANSACTION-INVALID, tid, REJECTED)``
to the submitting client.

否则，如果背书逻辑拒绝背书这个交易，背书节点 *也许* 会发送一个
``(TRANSACTION-INVALID, tid, REJECTED)`` 消息到提交客户端。

Notice that an endorser does not change its state in this step, the
updates produced by transaction simulation in the context of endorsement
do not affect the state!

注意背书节点在这个步骤不会修改它的状态，背书节点中的模拟交易更新不会影响状态。

2.3. The submitting client collects an endorsement for a transaction and broadcasts it through ordering service --提交客户端收集交易背书并广播给排序服务
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The submitting client waits until it receives "enough" messages and
signatures on ``(TRANSACTION-ENDORSED, tid, *, *)`` statements to
conclude that the transaction proposal is endorsed. As discussed in
Section 2.1.2., this may involve one or more round-trips of interaction
with endorsers.

提交客户端会等待收集到足够多的消息，然后在 ``(TRANSACTION-ENDORSED, tid, *, *)`` 签名来决
断交易提案已经被背书了。在章节2.1.2中讨论过，这里可能会和背书节点有多个来回的调用。

The exact number of "enough" depend on the chaincode endorsement policy
(see also Section 3). If the endorsement policy is satisfied, the
transaction has been *endorsed*; note that it is not yet committed. The
collection of signed ``TRANSACTION-ENDORSED`` messages from endorsing
peers which establish that a transaction is endorsed is called an
*endorsement* and denoted by ``endorsement``.

"足够"多的具体数目依赖链码的背书策略（参考章节3）。如果背书策略被满足，那么交易完成 *背
书* ；注意此时还未提交。由背书节点签名的 ``TRANSACTION-ENDORSED`` 消息集合共同建立交易被签
名的事实，称之为 *endorsement* 。

If the submitting client does not manage to collect an endorsement for a
transaction proposal, it abandons this transaction with an option to
retry later.

如果提交客户端没有为一个交易提案达成收集背书，它会放弃这次交易并根据配置再重试。

For transaction with a valid endorsement, we now start using the
ordering service. The submitting client invokes ordering service using
the ``broadcast(blob)``, where ``blob=endorsement``. If the client does
not have capability of invoking ordering service directly, it may proxy
its broadcast through some peer of its choice. Such a peer must be
trusted by the client not to remove any message from the ``endorsement``
or otherwise the transaction may be deemed invalid. Notice that,
however, a proxy peer may not fabricate a valid ``endorsement``.

对于有有效背书的交易，我们开始使用排序服务。提交客户端使用 ``broadcast(blob)`` 接口调用排
序服务，其中 ``blob=endorsement`` 。如果客户端没法直接调用排序服务，它可以选择一些peer节
点作为代理来广播。这样一个peer节点必须被客户端信任不会去移除 ``endorsement`` 中的任何信
息，否则交易会被认作无效。注意，无论如何代理节点是无法伪造一个有效的 ``endorsement`` 。

2.4. The ordering service delivers a transactions to the peers -- 排序服务递送交易到peer节点
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When an event ``deliver(seqno, prevhash, blob)`` occurs and a peer has
applied all state updates for blobs with sequence number lower than
``seqno``, a peer does the following:

当peer节点接收到一个 ``deliver(seqno, prevhash, blob)`` 事件，并且已经把 ``seqno`` 之前的
所有状态持久化，它会做如下处理：

-  It checks that the ``blob.endorsement`` is valid according to the
   policy of the chaincode (``blob.tran-proposal.chaincodeID``) to which
   it refers.

-  它根据 ``blob.tran-proposal.chaincodeID`` 指定的链码的策略，检查 ``blob.endorsement`` 是否有
   效。

-  In a typical case, it also verifies that the dependencies
   (``blob.endorsement.tran-proposal.readset``) have not been violated
   meanwhile. In more complex use cases, ``tran-proposal`` fields in
   endorsement may differ and in this case endorsement policy (Section
   3) specifies how the state evolves.

-  一个经典的场景，它还校验依赖（ ``blob.endorsement.tran-proposal.readset`` ）没有被违反。
   更复杂的应用场景，背书中的 ``tran-proposal`` 属性可能不同，背书策略（章节3）指定了状态
   如何演进。

Verification of dependencies can be implemented in different ways,
according to a consistency property or "isolation guarantee" that is
chosen for the state updates. **Serializability** is a default isolation
guarantee, unless chaincode endorsement policy specifies a different
one. Serializability can be provided by requiring the version associated
with *every* key in the ``readset`` to be equal to that key's version in
the state, and rejecting transactions that do not satisfy this
requirement.

根据一致性特性或者"隔离保证"，依赖校验可以通过不同的方式来实现。除非链码背书策略指定
了其他保证，否则 **串行化** 是一个默认的隔离保证。串行化会保证 ``读集`` 中的每个key的版本和状
态中的key版本相等，并且拒绝不满足这个条件的交易。

-  If all these checks pass, the transaction is deemed *valid* or
   *committed*. In this case, the peer marks the transaction with 1 in
   the bitmask of the ``PeerLedger``, applies
   ``blob.endorsement.tran-proposal.writeset`` to blockchain state (if
   ``tran-proposals`` are the same, otherwise endorsement policy logic
   defines the function that takes ``blob.endorsement``).

-  如果通过所有的检查，交易就是一定 *有效* 或者一定 *被提交* 。这种情况下，peer节点会在 ``PeerLedger``
   的bitmask上为这个交易标注为1，将 ``blob.endorsement.tran-proposal.writeset`` 写入区块状态（
   如果 ``tran-proposals`` 是一样的，否则背书策略逻辑定义函数来处理 ``blob.endorsement`` ）。

-  If the endorsement policy verification of ``blob.endorsement`` fails,
   the transaction is invalid and the peer marks the transaction with 0
   in the bitmask of the ``PeerLedger``. It is important to note that
   invalid transactions do not change the state.

-  如果 ``blob.endorsement`` 背书策略校验失败了，peer节点会在 ``PeerLedger``
   的bitmask上为这个交易标注为0。需要着重注意的是无效的交易不会改变状态。

Note that this is sufficient to have all (correct) peers have the same
state after processing a deliver event (block) with a given sequence
number. Namely, by the guarantees of the ordering service, all correct
peers will receive an identical sequence of
``deliver(seqno, prevhash, blob)`` events. As the evaluation of the
endorsement policy and evaluation of version dependencies in ``readset``
are deterministic, all correct peers will also come to the same
conclusion whether a transaction contained in a blob is valid. Hence,
all peers commit and apply the same sequence of transactions and update
their state in the same way.

注意，以上就是peer在处理带有序号的传递的事件（区块）后，所有peer节点都有相同的状态的
充分条件。通过排序服务的担保，所有正确的peer节点互收到一致的
``deliver(seqno, prevhash, blob)`` 事件序列。因为背书策略的评估和 ``读集`` 版本依赖的评估都是
确定的，所有peer节点都会达成blob中的交易是否有效的共识。因此，所有的peer节点提交和应
用相同的交易顺序，用同样的方式更新它们的状态。

.. image:: images/flow-4.png
   :alt: Illustration of the transaction flow (common-case path).

*Figure 1. Illustration of one possible transaction flow (common-case path).*


3. Endorsement policies -- 背书策略
---------------------------------------------------------------------------------

3.1. Endorsement policy specification -- 背书策略详述
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

An **endorsement policy**, is a condition on what *endorses* a
transaction. Blockchain peers have a pre-specified set of endorsement
policies, which are referenced by a ``deploy`` transaction that installs
specific chaincode. Endorsement policies can be parametrized, and these
parameters can be specified by a ``deploy`` transaction.

一个 **背书策略** 是一个谁来 *背书* 交易的条件。区块peer节点有一个预先指定的背书策略集合，
当通过部署交易来安装指定链码时就会指定。背书策略可以用参数表示，而这些参数可以通过部署
交易来指定。

To guarantee blockchain and security properties, the set of endorsement
policies **should be a set of proven policies** with limited set of
functions in order to ensure bounded execution time (termination),
determinism, performance and security guarantees.

为了保证区块链安全特性，背书策略 **应该是被证明的策略集合** ，有限的几个功能用来确保有限执
行时间（终止），确定性，性能和安全保障。

Dynamic addition of endorsement policies (e.g., by ``deploy``
transaction on chaincode deploy time) is very sensitive in terms of
bounded policy evaluation time (termination), determinism, performance
and security guarantees. Therefore, dynamic addition of endorsement
policies is not allowed, but can be supported in future.

动态添加背书策略（如通过 ``部署`` 交易控制链码部署时间）在约束策略执行时间（终止），确定性，
性能和安全保障上非常敏感。因此，动态添加背书策略是不允许的，但未来可能支持。

3.2. Transaction evaluation against endorsement policy -- 针对背书策略评估交易
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A transaction is declared valid only if it has been endorsed according
to the policy. An invoke transaction for a chaincode will first have to
obtain an *endorsement* that satisfies the chaincode's policy or it will
not be committed. This takes place through the interaction between the
submitting client and endorsing peers as explained in Section 2.

Formally the endorsement policy is a predicate on the endorsement, and
potentially further state that evaluates to TRUE or FALSE. For deploy
transactions the endorsement is obtained according to a system-wide
policy (for example, from the system chaincode).

An endorsement policy predicate refers to certain variables. Potentially
it may refer to:

1. keys or identities relating to the chaincode (found in the metadata
   of the chaincode), for example, a set of endorsers;
2. further metadata of the chaincode;
3. elements of the ``endorsement`` and ``endorsement.tran-proposal``;
4. and potentially more.

The above list is ordered by increasing expressiveness and complexity,
that is, it will be relatively simple to support policies that only
refer to keys and identities of nodes.

**The evaluation of an endorsement policy predicate must be
deterministic.** An endorsement shall be evaluated locally by every peer
such that a peer does *not* need to interact with other peers, yet all
correct peers evaluate the endorsement policy in the same way.

3.3. Example endorsement policies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The predicate may contain logical expressions and evaluates to TRUE or
FALSE. Typically the condition will use digital signatures on the
transaction invocation issued by endorsing peers for the chaincode.

Suppose the chaincode specifies the endorser set
``E = {Alice, Bob, Charlie, Dave, Eve, Frank, George}``. Some example
policies:

-  A valid signature from on the same ``tran-proposal`` from all members
   of E.

-  A valid signature from any single member of E.

-  Valid signatures on the same ``tran-proposal`` from endorsing peers
   according to the condition
   ``(Alice OR Bob) AND (any two of: Charlie, Dave, Eve, Frank, George)``.

-  Valid signatures on the same ``tran-proposal`` by any 5 out of the 7
   endorsers. (More generally, for chaincode with ``n > 3f`` endorsers,
   valid signatures by any ``2f+1`` out of the ``n`` endorsers, or by
   any group of *more* than ``(n+f)/2`` endorsers.)

-  Suppose there is an assignment of "stake" or "weights" to the
   endorsers, like
   ``{Alice=49, Bob=15, Charlie=15, Dave=10, Eve=7, Frank=3, George=1}``,
   where the total stake is 100: The policy requires valid signatures
   from a set that has a majority of the stake (i.e., a group with
   combined stake strictly more than 50), such as ``{Alice, X}`` with
   any ``X`` different from George, or
   ``{everyone together except Alice}``. And so on.

-  The assignment of stake in the previous example condition could be
   static (fixed in the metadata of the chaincode) or dynamic (e.g.,
   dependent on the state of the chaincode and be modified during the
   execution).

-  Valid signatures from (Alice OR Bob) on ``tran-proposal1`` and valid
   signatures from ``(any two of: Charlie, Dave, Eve, Frank, George)``
   on ``tran-proposal2``, where ``tran-proposal1`` and
   ``tran-proposal2`` differ only in their endorsing peers and state
   updates.

How useful these policies are will depend on the application, on the
desired resilience of the solution against failures or misbehavior of
endorsers, and on various other properties.

4 (post-v1). Validated ledger and ``PeerLedger`` checkpointing (pruning)
------------------------------------------------------------------------

4.1. Validated ledger (VLedger)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To maintain the abstraction of a ledger that contains only valid and
committed transactions (that appears in Bitcoin, for example), peers
may, in addition to state and Ledger, maintain the *Validated Ledger (or
VLedger)*. This is a hash chain derived from the ledger by filtering out
invalid transactions.

The construction of the VLedger blocks (called here *vBlocks*) proceeds
as follows. As the ``PeerLedger`` blocks may contain invalid
transactions (i.e., transactions with invalid endorsement or with
invalid version dependencies), such transactions are filtered out by
peers before a transaction from a block becomes added to a vBlock. Every
peer does this by itself (e.g., by using the bitmask associated with
``PeerLedger``). A vBlock is defined as a block without the invalid
transactions, that have been filtered out. Such vBlocks are inherently
dynamic in size and may be empty. An illustration of vBlock construction
is given in the figure below.

.. image:: images/blocks-3.png
   :alt: Illustration of vBlock formation

*Figure 2. Illustration of validated ledger block (vBlock) formation from ledger (PeerLedger) blocks.*

vBlocks are chained together to a hash chain by every peer. More
specifically, every block of a validated ledger contains:

-  The hash of the previous vBlock.

-  vBlock number.

-  An ordered list of all valid transactions committed by the peers
   since the last vBlock was computed (i.e., list of valid transactions
   in a corresponding block).

-  The hash of the corresponding block (in ``PeerLedger``) from which
   the current vBlock is derived.

All this information is concatenated and hashed by a peer, producing the
hash of the vBlock in the validated ledger.

4.2. ``PeerLedger`` Checkpointing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ledger contains invalid transactions, which may not necessarily be
recorded forever. However, peers cannot simply discard ``PeerLedger``
blocks and thereby prune ``PeerLedger`` once they establish the
corresponding vBlocks. Namely, in this case, if a new peer joins the
network, other peers could not transfer the discarded blocks (pertaining
to ``PeerLedger``) to the joining peer, nor convince the joining peer of
the validity of their vBlocks.

To facilitate pruning of the ``PeerLedger``, this document describes a
*checkpointing* mechanism. This mechanism establishes the validity of
the vBlocks across the peer network and allows checkpointed vBlocks to
replace the discarded ``PeerLedger`` blocks. This, in turn, reduces
storage space, as there is no need to store invalid transactions. It
also reduces the work to reconstruct the state for new peers that join
the network (as they do not need to establish validity of individual
transactions when reconstructing the state by replaying ``PeerLedger``,
but may simply replay the state updates contained in the validated
ledger).

4.2.1. Checkpointing protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checkpointing is performed periodically by the peers every *CHK* blocks,
where *CHK* is a configurable parameter. To initiate a checkpoint, the
peers broadcast (e.g., gossip) to other peers message
``<CHECKPOINT,blocknohash,blockno,stateHash,peerSig>``, where
``blockno`` is the current blocknumber and ``blocknohash`` is its
respective hash, ``stateHash`` is the hash of the latest state (produced
by e.g., a Merkle hash) upon validation of block ``blockno`` and
``peerSig`` is peer's signature on
``(CHECKPOINT,blocknohash,blockno,stateHash)``, referring to the
validated ledger.

A peer collects ``CHECKPOINT`` messages until it obtains enough
correctly signed messages with matching ``blockno``, ``blocknohash`` and
``stateHash`` to establish a *valid checkpoint* (see Section 4.2.2.).

Upon establishing a valid checkpoint for block number ``blockno`` with
``blocknohash``, a peer:

-  if ``blockno>latestValidCheckpoint.blockno``, then a peer assigns
   ``latestValidCheckpoint=(blocknohash,blockno)``,
-  stores the set of respective peer signatures that constitute a valid
   checkpoint into the set ``latestValidCheckpointProof``,
-  stores the state corresponding to ``stateHash`` to
   ``latestValidCheckpointedState``,
-  (optionally) prunes its ``PeerLedger`` up to block number ``blockno``
   (inclusive).

4.2.2. Valid checkpoints
^^^^^^^^^^^^^^^^^^^^^^^^

Clearly, the checkpointing protocol raises the following questions:
*When can a peer prune its ``PeerLedger``? How many ``CHECKPOINT``
messages are "sufficiently many"?*. This is defined by a *checkpoint
validity policy*, with (at least) two possible approaches, which may
also be combined:

-  *Local (peer-specific) checkpoint validity policy (LCVP).* A local
   policy at a given peer *p* may specify a set of peers which peer *p*
   trusts and whose ``CHECKPOINT`` messages are sufficient to establish
   a valid checkpoint. For example, LCVP at peer *Alice* may define that
   *Alice* needs to receive ``CHECKPOINT`` message from Bob, or from
   *both* *Charlie* and *Dave*.

-  *Global checkpoint validity policy (GCVP).* A checkpoint validity
   policy may be specified globally. This is similar to a local peer
   policy, except that it is stipulated at the system (blockchain)
   granularity, rather than peer granularity. For instance, GCVP may
   specify that:

   -  each peer may trust a checkpoint if confirmed by *11* different
      peers.
   -  in a specific deployment in which every orderer is collocated with
      a peer in the same machine (i.e., trust domain) and where up to
      *f* orderers may be (Byzantine) faulty, each peer may trust a
      checkpoint if confirmed by *f+1* different peers collocated with
      orderers.

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
