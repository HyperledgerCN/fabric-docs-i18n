*Needs Review*

Glossary - 词汇表
===========================

Terminology is important, so that all Hyperledger Fabric users and developers
agree on what we mean by each specific term. What is chaincode, for example.
The documentation will reference the glossary as needed, but feel free to
read the entire thing in one sitting if you like; it's pretty enlightening!

专业术语很重要，所以全体“超级账本Fabric”项目的用户和开发人员，都同意我们所说的
每个特定术语的含义，比如什么是链码。该文档将会按需引用这些术语，如果你愿意的话
可以随意阅读整个文档，这会非常有启发！


.. _Anchor-Peer:

Anchor Peer - 锚节点
--------------------

A peer node on a channel that all other peers can discover and communicate with.
Each Member_ on a channel has an anchor peer (or multiple anchor peers to prevent
single point of failure), allowing for peers belonging to different Members to
discover all existing peers on a channel.

锚节点是通道中能被所有对等节点探测、并能与之进行通信的一种对等节点。通道中的每个
“成员 Member_”都有一个（或多个，以防单点故障）锚节点，允许属于不同成员身份的节点
来发现通道中存在的其它节点。

.. _Block:

Block - 区块
------------

An ordered set of transactions that is cryptographically linked to the
preceding block(s) on a channel.

区块是通道上一组有序交易的集合，通过密码学手段（哈希加密）连接到前导区块。

Zhu Jiang：区块是一组有序的交易集合，在通道中经过加密（哈希加密）后与前序区块连接。

.. _Chain:

Chain - 链
----------

The ledger's chain is a transaction log structured as hash-linked blocks of
transactions. Peers receive blocks of transactions from the ordering service, mark
the block's transactions as valid or invalid based on endorsement policies and
concurrency violations, and append the block to the hash chain on the peer's
file system.

链就是区块之间以哈希连接为结构的交易日志。对等节点从排序服务节点接收交易区块，并根据
背书策略和并发冲突标记区块上的交易是否有效，然后将该区块追加到节点文件系统中的哈希链
上。

Zhu Jiang:账本的链是一个交易区块经过“哈希连接”结构化的交易日志。对等节点从排序服务收
到交易区块，基于背书策略和并发冲突来标注区块的交易为有效或者无效状态，并且将区块追加
到对等节点文件系统的哈希链中。

.. _chaincode:

Chaincode - 链码
----------------

Chaincode is software, running on a ledger, to encode assets and the transaction
instructions (business logic) for modifying the assets.

链码是一个运行在账本上的软件，它可以对资产进行编码，其中的交易指令（或者叫业务逻辑）
也可以用来修改资产。

.. _Channel:

Channel - 通道
--------------

A channel is a private blockchain overlay which allows for data isolation and 
confidentiality. 
A channel-specific ledger is shared across the peers in the channel, and transacting 
parties must be properly authenticated to a channel in order to interact with it.  
Channels are defined by a Configuration-Block_.

通道是基于数据隔离和保密构建的一个私有区块链。特定通道的账本在该通道中的所有节点共享，
交易方必须通过该通道的正确验证才能与账本进行交互。通道是由一个
“配置块 Configuration-Block_”来定义的。

.. _Commitment:

Commitment - 提交
-----------------

Each Peer_ on a channel validates ordered blocks of
transactions and then commits (writes/appends) the blocks to its replica of the
channel Ledger_. Peers also mark each transaction in each block as valid or invalid.

一个通道中的每个“对等节点 Peer_”都会验证交易的有序区块，然后将区块提交（写或追加）
至该通道上“账本 Ledger_”的各个副本。对等节点也会标记每个区块中的每笔交易的状态是有
效或者无效。

.. _Concurrency-Control-Version-Check:

Concurrency Control Version Check - 并发控制版本检查（CCVC）
-----------------------------------------------------------

Concurrency Control Version Check is a method of keeping state in sync across
peers on a channel. Peers execute transactions in parallel, and before commitment
to the ledger, peers check that the data read at execution time has not changed.
If the data read for the transaction has changed between execution time and
commitment time, then a Concurrency Control Version Check violation has
occurred, and the transaction is marked as invalid on the ledger and values
are not updated in the state database.

CCVC是保持通道中各对等节点间状态同步的一种方法。对等节点并行的执行交易，在交易提交至
账本之前，对等节点会检查交易在执行期间读到的数据是否被修改。如果读取的数据在执行和提
交之间被改变，就会引发CCVC冲突，该交易就会在账本中被标记为无效，而且值不会更新到状态
数据库中。

.. _Configuration-Block:

Configuration Block - 配置区块
------------------------------

Contains the configuration data defining members and policies for a system
chain (ordering service) or channel. Any configuration modifications to a
channel or overall network (e.g. a member leaving or joining) will result
in a new configuration block being appended to the appropriate chain. This
block will contain the contents of the genesis block, plus the delta.

包含为系统链（排序服务）或通道定义成员和策略的配置数据。对某个通道或整个网络的配置修
改（比如，成员离开或加入）都将导致生成一个新的配置区块并追加到适当的链上。这个配置区
块会包含创始区块的内容加上增量。

.. Consensus:

Consensus - 共识
----------------

A broader term overarching the entire transactional flow, which serves to generate
an agreement on the order and to confirm the correctness of the set of transactions
constituting a block.

共识是贯穿整个交易流程的广义术语，其用于产生一个对于排序的同意书和确认构成区块的交易
集的正确性。

.. _Current-State:

Current State - 当前状态:
-------------------------

The current state of the ledger represents the latest values for all keys ever
included in its chain transaction log. Peers commit the latest values to ledger
current state for each valid transaction included in a processed block. Since
current state represents all latest key values known to the channel, it is
sometimes referred to as World State. Chaincode executes transaction proposals
against current state data.

账本当前状态表示其链上交易日志中所有键名的最新值。节点会将处理过的区块中的每个交易对
应的修改值提交到账本的当前状态，由于当前状态表示通道里现有键名的最新值，所以当前状态
也被称为世界观(World State)。链码执行交易提案就是针对的当前状态数据。

.. _Dynamic-Membership:

Dynamic Membership - 动态成员
-----------------------------

Hyperledger Fabric supports the addition/removal of members, peers, and ordering 
service nodes, without compromising the operationality of the overall network. 
Dynamic membership is critical when business relationships adjust and entities 
need to be added/removed for various reasons.

超级账本Fabric支持动态的添加或移除：成员、对等节点、排序服务节点，而不影响整个网络的
操作性。当业务关系调整或因各种原因需添加/移除实体时，动态成员至关重要。

.. _Endorsement:

Endorsement - 背书
------------------

Refers to the process where specific peer nodes execute a chaincode transaction and 
return a proposal response to the client application. The proposal response includes 
the chaincode execution response message, results (read set and write set), and events,
as well as a signature to serve as proof of the peer's chaincode execution.
Chaincode applications have corresponding endorsement policies, in which the endorsing
peers are specified.

背书是指特定节点执行一个链码交易并返回一个提案响应给客户端应用的过程。提案响应包含链
码执行后返回的消息，结果（读写集）和事件，同时也包含证明该节点执行链码的签名。
链码应用具有相应的背书策略，其中指定了背书节点。

.. _Endorsement-policy:

Endorsement policy - 背书策略
-----------------------------

Defines the peer nodes on a channel that must execute transactions attached to a
specific chaincode application, and the required combination of responses (endorsements).
A policy could require that a transaction be endorsed by a minimum number of
endorsing peers, a minimum percentage of endorsing peers, or by all endorsing
peers that are assigned to a specific chaincode application. Policies can be
curated based on the application and the desired level of resilience against
misbehavior (deliberate or not) by the endorsing peers. A transaction that is submitted
must satisfy the endorsement policy before being marked as valid by committing peers.
A distinct endorsement policy for install and instantiate transactions is also required.

背书策略定义了通道上，依赖于特定链码执行交易的节点，和必要的组合响应（背书）。背书策略
可指定特定链码应用的交易背书节点，以及交易背书的最小参与节点数、百分比，或全部节点。背
书策略可以基于应用程序和节点对于抵御（有意无意）不良行为的期望水平来组织管理。提交的交
易在被执行节点标记成有效前，必须符合背书策略。安装和实例化交易时，也需要一个明确的背书
策略。

.. _Fabric-ca:

Hyperledger Fabric CA - 超级账本Fabric-ca
-----------------------------------------

Hyperledger Fabric CA is the default Certificate Authority component, which
issues PKI-based certificates to network member organizations and their users.
The CA issues one root certificate (rootCert) to each member and one enrollment
certificate (ECert) to each authorized user.

超级账本Fabric CA是默认的认证授权管理组件，它向网络成员组织及其用户颁发基于PKI的证书。
CA为每个成员颁发一个根证书（rootCert），为每个授权用户颁发一个注册证书（ECert）。

.. _Genesis-Block:

Genesis Block - 初始区块
------------------------

The configuration block that initializes a blockchain network or channel, and
also serves as the first block on a chain.

初始区块是初始化区块链网络或通道的配置区块，也是链上的第一个区块。

.. _Gossip-Protocol:

Gossip Protocol - Gossip协议
----------------------------

The gossip data dissemination protocol performs three functions:
1) manages peer discovery and channel membership;
2) disseminates ledger data across all peers on the channel;
3) syncs ledger state across all peers on the channel.
Refer to the :doc:`Gossip <gossip>` topic for more details.

Gossip数据传输协议有三项功能：
1）管理“节点发现”和“通道成员”；
2）通道上的所有节点间广播账本数据；
3）通道上的所有节点间同步账本数据。
详情参考 :doc:`Gossip <gossip>` 话题.

.. _Initialize:

Initialize - 初始化
-------------------

A method to initialize a chaincode application.

一个初始化链码程序的方法。

.. _Install:

Install - 安装
--------------

The process of placing a chaincode on a peer's file system.

将链码放到节点文件系统的过程。（译注：即将ChaincodeDeploymentSpec信息存到
chaincodeInstallPath-chaincodeName.chainVersion文件中）

.. _Instantiate:

Instantiate - 实例化
--------------------

The process of starting and initializing a chaincode application on a specific channel.
After instantiation, peers that have the chaincode installed can accept chaincode
invocations.

在特定通道上启动和初始化链码应用的过程。实例化完成后，装有链码的节点可以接受链码调用。
（译注：在lccc中将链码数据保存到状态中，然后部署并执行Init方法）

.. _Invoke:

Invoke - 调用
-------------

Used to call chaincode functions. A client application invokes chaincode by
sending a transaction proposal to a peer. The peer will execute the chaincode
and return an endorsed proposal response to the client application. The client
application will gather enough proposal responses to satisfy an endorsement policy,
and will then submit the transaction results for ordering, validation, and commit.
The client application may choose not to submit the transaction results. For example
if the invoke only queried the ledger, the client application typically would not
submit the read-only transaction, unless there is desire to log the read on the ledger
for audit purpose. The invoke includes a channel identifier, the chaincode function to
invoke, and an array of arguments.

用于调用链码内的函数。客户端应用通过向节点发送交易提案来调用链码。节点会执行链码并向客
户端应用返回一个背书提案。客户端应用会收集充足的提案响应来判断是否符合背书策略，之后再
将交易结果递交到排序、验证和提交。客户端应用可以选择不提交交易结果。比如，调用只查询账
本，通常情况下，客户端应用是不会提交这种只读性交易的，除非基于审计目的，需要记录访问账
本的日志。调用包含了通道标识符，调用的链码函数，以及一个包含参数的数组。

.. _Leading-Peer:

Leading Peer - 主导节点
-----------------------

Each Member_ can own multiple peers on each channel that
it subscribes to. One of these peers is serves as the leading peer for the channel,
in order to communicate with the network ordering service on behalf of the
member. The ordering service "delivers" blocks to the leading peer(s) on a
channel, who then distribute them to other peers within the same member cluster.

每一个“成员 Member_”在其订阅的通道上可以拥有多个节点，其中一个节点会作为通道的主导
节点，代表该成员与网络排序服务节点通信。排序服务将区块传递给通道上的主导节点，主导
节点再将此区块分发给同一成员集群下的其他节点。

.. _Ledger:

Ledger - 账本
-------------

A ledger is a channel's chain and current state data which is maintained by each
peer on the channel.

账本是通道上的链，以及由通道中每个节点维护的当前状态数据。

.. _Member:

Member - 成员
-------------

A legally separate entity that owns a unique root certificate for the network.
Network components such as peer nodes and application clients will be linked to a member.

拥有网络唯一根证书的合法独立实体。诸如节点和应用客户端这样的网络组件都会关联到一个成员。

.. _MSP:

Membership Service Provider - 成员服务提供者
--------------------------------------------

The Membership Service Provider (MSP) refers to an abstract component of the
system that provides credentials to clients, and peers for them to participate
in a Hyperledger Fabric network. Clients use these credentials to authenticate
their transactions, and peers use these credentials to authenticate transaction
processing results (endorsements). While strongly connected to the transaction
processing components of the systems, this interface aims to have membership
services components defined, in such a way that alternate implementations of
this can be smoothly plugged in without modifying the core of transaction
processing components of the system.

成员服务提供者（MSP）是指为客户端和节点加入超级账本Fabric网络，提供证书的系统抽象组件。
客户端用证书来认证他们的交易；节点用证书认证交易处理结果（背书）。该接口与系统的交易处
理组件密切相关，旨在定义成员服务组件，以这种方式可选实现平滑接入，而不用修改系统的交易
处理组件核心。

.. _Membership-Services:

Membership Services - 成员服务
------------------------------

Membership Services authenticates, authorizes, and manages identities on a
permissioned blockchain network. The membership services code that runs in peers
and orderers both authenticates and authorizes blockchain operations. It is a
PKI-based implementation of the Membership Services Provider (MSP) abstraction.

成员服务在许可的区块链网络上做认证、授权和身份管理。运行于节点和排序服务的成员服务代码均
会参与认证和授权区块链操作。它是基于PKI的抽象成员服务提供者（MSP）的实现。

.. _Ordering-Service:

Ordering Service - 排序服务
-------------------------------------

A defined collective of nodes that orders transactions into a block.  The ordering
service exists independent of the peer processes and orders transactions on a
first-come-first-serve basis for all channel's on the network.  The ordering service is
designed to support pluggable implementations beyond the out-of-the-box SOLO and Kafka varieties.
The ordering service is a common binding for the overall network; it contains the cryptographic
identity material tied to each Member_.

预先定义好的一组节点，将交易排序放入区块。排序服务独立于节点流程之外，并以先到先得的方式
为网络上所有通道做交易排序。交易排序支持可插拔实现，目前默认实现了SOLO和Kafka。排序服务是
整个网络的公用绑定，包含与每个“成员 Member_”相关的加密材料。

.. _Peer:

Peer - 节点
-----------

A network entity that maintains a ledger and runs chaincode containers in order to perform
read/write operations to the ledger.  Peers are owned and maintained by members.

一个网络实体，维护账本并运行链码容器来对账本做读写操作。节点由成员所有，并负责维护。

.. _Policy:

Policy - 策略
-------------

There are policies for endorsement, validation, chaincode management and 
network/channel management.

各种策略：背书策略，校验策略，链码管理策略，网络管理策略，通道管理策略。

.. _Proposal:

Proposal - 提案
---------------

A request for endorsement that is aimed at specific peers on a channel. Each
proposal is either an instantiate or an invoke (read/write) request.

一种通道中针对特定节点的背书请求。每个提案要么是链码的实例化，要么是链码的调用（读写）请求。

.. _Query:

Query - 查询
------------

A query is a chaincode invocation which reads the ledger current state but does
not write to the ledger. The chaincode function may query certain keys on the ledger,
or may query for a set of keys on the ledger. Since queries do not change ledger state,
the client application will typically not submit these read-only transactions for ordering,
validation, and commit. Although not typical, the client application can choose to
submit the read-only transaction for ordering, validation, and commit, for example if the
client wants auditable proof on the ledger chain that it had knowledge of specific ledger
state at a certain point in time.

查询是一个链码调用，只读账本当前状态，不写入账本。链码函数可以查询账本上的特定键名，也可以
查询账本上的一组键名。由于查询不改变账本状态，因此客户端应用通常不会提交这类只读交易做排序、
验证和提交。不过，特殊情况下，客户端应用还是会选择提交只读交易做排序、验证和提交。比如，客
户需要账本链上保留可审计证据，就需要链上保留某一特定时间点的特定账本的状态。

.. _SDK:

Software Development Kit (SDK) - 软件开发包（SDK）
------------------------------

The Hyperledger Fabric client SDK provides a structured environment of libraries
for developers to write and test chaincode applications. The SDK is fully
configurable and extensible through a standard interface. Components, including
cryptographic algorithms for signatures, logging frameworks and state stores,
are easily swapped in and out of the SDK. The SDK provides APIs for transaction
processing, membership services, node traversal and event handling. The SDK
comes in multiple flavors: Node.js, Java. and Python.

超级账本Fabric客户端软件开发包（SDK）为开发人员提供了一个结构化的库环境，用于编写和测试链码
应用程序。SDK完全可以通过标准接口实现配置和扩展。它的各种组件：签名加密算法、日志框架和状态
存储，都可以轻松地被替换。SDK提供APIs进行交易处理，成员服务、节点遍历以及事件处理。目前SDK
支持Node.js、Java和Python。

.. _State-DB:

State Database - 状态数据库
---------------------------

Current state data is stored in a state database for efficient reads and queries
from chaincode. Supported databases include levelDB and couchDB.

为了从链码中高效的读写，当前状态数据存储在状态数据库中。支持的数据库包括levelDB和couchDB。

.. _System-Chain:

System Chain - 系统链
---------------------

Contains a configuration block defining the network at a system level. The
system chain lives within the ordering service, and similar to a channel, has
an initial configuration containing information such as: MSP information, policies,
and configuration details.  Any change to the overall network (e.g. a new org
joining or a new ordering node being added) will result in a new configuration block
being added to the system chain.

一个在系统层面定义网络的配置区块。系统链存在于排序服务中，与通道类似，具有包含以下信息的初始
配置：MSP（成员服务提供者）信息、策略和配置详情。全网中的任何变化（例如新的组织加入或者
新的排序节点加入）将导致新的配置区块被添加到系统链中。

The system chain can be thought of as the common binding for a channel or group
of channels.  For instance, a collection of financial institutions may form a
consortium (represented through the system chain), and then proceed to create
channels relative to their aligned and varying business agendas.

系统链可看做是一个或一组通道的公用绑定。例如，金融机构的集合可以形成一个财团（表现为系统链），
然后根据其相同或不同的业务计划创建通道。

.. _Transaction:

Transaction - 交易
------------------

Invoke or instantiate results that are submitted for ordering, validation, and commit.
Invokes are requests to read/write data from the ledger. Instantiate is a request to
start and initialize a chaincode on a channel. Application clients gather invoke or
instantiate responses from endorsing peers and package the results and endorsements
into a transaction that is submitted for ordering, validation, and commit.

调用或者实例化结果递交到排序、验证和提交。调用是从账本中读/写数据的请求。实例化是在通道中启动
并初始化链码的请求。客户端应用从背书节点收集调用或实例化响应，并将结果和背书打包到交易事务，
即递交到做排序，验证和提交。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
