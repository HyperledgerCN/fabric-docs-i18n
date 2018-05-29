Transaction Flow
================

交易流程
================

This document outlines the transactional mechanics that take place during a standard asset
exchange.  The scenario includes two clients, A and B, who are buying and selling
radishes.  They each have a peer on the network through which they send their
transactions and interact with the ledger.

本文概述了资产交易过程中的事务机制。该场景包含客户A和B，在进行萝卜买卖。他们各自有一个网络节点，通过节点他们发送交易并和账本进行交互。

.. image:: images/step0.png

**Assumptions**

**假设**

This flow assumes that a channel is set up and running.  The application user
has registered and enrolled with the organization’s certificate authority (CA)
and received back necessary cryptographic material, which is used to authenticate
to the network.

该流程假设通道已建立并正常运行。用户已注册并使用组织认证授权（CA）登记，同时获得必要的加密材料来进行网络验证。

The chaincode (containing a set of key value pairs representing the initial
state of the radish market) is installed on the peers and instantiated on the
channel.  The chaincode contains logic defining a set of transaction
instructions and the agreed upon price for a radish. An endorsement policy has
also been set for this chaincode, stating that both ``peerA`` and ``peerB`` must endorse
any transaction.

链码（包含一组代表萝卜市场初始状态的键值对）被安装在节点上并在通道上进行实例化。链码包含定义交易指令集合的逻辑和达成一致的萝卜价格。设置一项针对链码的背书策略，表明节点A和B都必须对任何交易进行背书。

.. image:: images/step1.png

1. **Client A initiates a transaction**

1. **客户A发起交易**

What's happening? - Client A is sending a request to purchase radishes.  The
request targets ``peerA`` and ``peerB``, who are respectively representative of
Client A and Client B. The endorsement policy states that both peers must endorse
any transaction, therefore the request goes to ``peerA`` and ``peerB``.

发生了什么？- 客户A发出萝卜购买请求。请求目标节点A和B，分别代表客户A和B。背书策略表明两个节点必须为任何交易进行背书，因而请求被发送到节点A和B。

Next, the transaction proposal is constructed.  An application leveraging a supported
SDK (Node, Java, Python) utilizes one of the available API's which generates a
transaction proposal.  The proposal is a request to invoke a chaincode function
so that data can be read and/or written to the ledger (i.e. write new key value
pairs for the assets).  The SDK serves as a shim to package the transaction proposal
into the properly architected format (protocol buffer over gRPC) and takes the user’s
cryptographic credentials to produce a unique signature for this transaction proposal.

接下来构建交易提案。一个以可用SDK（node, java, python）为支撑的应用利用有效的API来生成交易提案。这项提案作为调用链码功能的请求来完成数据到账本的读取和/或写入（即为资产写入新的键值对）。SDK有两个作用：把交易提案包装成合适架构格式的库（基于gRPC的协议缓冲）；使用用户的加密证书来创建交易提案的唯一签名。

.. image:: images/step2.png

2. **Endorsing peers verify signature & execute the transaction**

2. **背书节点验证签名&执行交易**

The endorsing peers verify (1) that the transaction proposal is well formed,
(2) it has not been submitted already in the past (replay-attack protection),
(3) the signature is valid (using MSP), and (4) that the
submitter (Client A, in the example) is properly authorized to perform
the proposed operation on that channel (namely, each endorsing peer ensures that
the submitter satisfies the channel's *Writers* policy).
The endorsing peers take the transaction proposal inputs as
arguments to the invoked chaincode's function. The chaincode is then
executed against the current state database to produce transaction
results including a response value, read set, and write set.  No updates are
made to the ledger at this point. The set of these values, along with the
endorsing peer’s signature is passed back as a “proposal response” to the SDK
which parses the payload for the application to consume.

背书节点验证（1）交易提案是完整的，（2）在过去没有被提交过（重放攻击保护），（3）签名是有效的（使用MSP），并且（4）提交者（在本例中的客户A）得到正确的授权在通道中来执行提案（即，每一个背书节点保证提交者满足通道的“写”策略。背书节点以交易提案凭证为输入，基于当前状态的数据库执行来生成交易结果，输出包括反馈值、读取集合和写入集合。截止现在账本还未进行更新。这些值的集合，背书节点的签名以及是/否的背书声明一同作为“提案反馈”被传输回到SDK，SDK对应用消耗的载荷进行解析。

*{The MSP is a peer component that allows them to verify
transaction requests arriving from clients and to sign transaction results(endorsements).
The Writing policy is defined at channel creation time, and determines
which user is entitled to submit a transaction to that channel.}*

{MSP是在节点组件允许节点验证客户的交易请求和签订交易结果（背书）。写策略在通道创建时定义，决定哪些用户有资格在通道中提交一个交易。}


.. image:: images/step3.png

3. **Proposal responses are inspected**

3. **审查提案反馈**

The application verifies the endorsing peer signatures and compares the proposal
responses to determine if the proposal responses are the same. If the chaincode only queried
the ledger, the application would inspect the query response and would typically not
submit the transaction to Ordering Service. If the client application intends to submit the
transaction to Ordering Service to update the ledger, the application determines if the specified
endorsement policy has been fulfilled before submitting (i.e. did peerA and peerB both endorse).
The architecture is such that even if an application chooses not to inspect responses or otherwise
forwards an unendorsed transaction, the endorsement policy will still be enforced by peers
and upheld at the commit validation phase.

应用对背书节点签名进行验证，比较提案反馈来决定是否一致。如果链码只需要账本，应用将会对查询结果进行检查并且一般不会提交交易到排序服务。如果客户程序打算提交一个交易到排序服务来更新账本，程序会决定在提交前指定的背书策略是否被执行（即节点A和B都进行了背书）。这种架构可以保证即使一个应用选择不进行反馈审查或者转发了没有背书的交易，背书策略依然会被节点执行并在验证提交阶段维持。

.. image:: images/step4.png

4. **Client assembles endorsements into a transaction**

4. **客户组合交易背书**

The application “broadcasts” the transaction proposal and response within a
“transaction message” to the Ordering Service. The transaction will contain the
read/write sets, the endorsing peers signatures and the Channel ID.  The
Ordering Service does not need to inspect the entire content of a transaction in order to perform
its operation, it simply receives
transactions from all channels in the network, orders them chronologically by
channel, and creates blocks of transactions per channel.

应用对交易提案进行广播，以“交易信息”对排序服务实现反馈。交易包含读/写集合，背书节点签名和通道ID。排序服务不读取交易细节，只是从网络中所有通道接收交易，根据每个通道按时间顺序调用，创建每个通道的交易区块。 

.. image:: images/step5.png

5. **Transaction is validated and committed**

5. **交易验证和提交**

The blocks of transactions are “delivered” to all peers on the channel.  The
transactions within the block are validated to ensure endorsement policy is
fulfilled and to ensure that there have been no changes to ledger state for read
set variables since the read set was generated by the transaction execution.
Transactions in the block are tagged as being valid or invalid.

交易区块被“发布”到通道中的所有节点。区块中的交易被验证来确保背书策略被执行并且账本的读取集合变量没有发生变化，因为读取集合是执行交易生成的。区块中的交易被标记为有效或无效。

.. image:: images/step6.png

6. **Ledger updated**

6. **账本更新**

Each peer appends the block to the channel’s chain, and for each valid transaction
the write sets are committed to current state database. An event is emitted, to
notify the client application that the transaction (invocation) has been
immutably appended to the chain, as well as notification of whether the
transaction was validated or invalidated.

每个节点都把区块追加到通道的链中，对每项有效交易，写集合被提交到当前状态的数据库。发出一个事务通知客户端应用，交易（调用）被永久追加到链中，并且通知交易是有效或者无效的。

**Note**: See the :ref:`swimlane` diagram to better understand the server side flow and the
protobuffers.

备注：参照链码泳道图以获得服务端流程和协议缓冲的更好理解。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
