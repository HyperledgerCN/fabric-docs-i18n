Ledger - 账本
================

The ledger is the sequenced, tamper-resistant record of all state transitions. State
transitions are a result of chaincode invocations ("transactions") submitted by participating
parties.  Each transaction results in a set of asset key-value pairs that are committed to the
ledger as creates, updates, or deletes.

账本是所有状态转换的有序的、防篡改的记录。状态转换是链码执行（即交易）的结果，这些交易由多方进行提交。每个交易会产生一组资产键值对，这些键值对以“创建”、“更新”或者“删除” 的方式提交给账本。

The ledger is comprised of a blockchain ('chain') to store the immutable, sequenced record in
blocks, as well as a state database to maintain current state.  There is one ledger per
channel. Each peer maintains a copy of the ledger for each channel of which they are a member.

账本由一个区块链（链）构成，并将不可变的、有序的记录存放在区块中；同时包含一个状态数据库来记录当前的状态。每个通道中各有一个账本。各个节点对于它所属的每个通道，都会保存一份该通道的账本副本。

Chain - 链
--------------

The chain is a transaction log, structured as hash-linked blocks, where each block contains a
sequence of N transactions. The block header includes a hash of the block's transactions, as
well as a hash of the prior block's header. In this way, all transactions on the ledger are
sequenced and cryptographically linked together. In other words, it is not possible to tamper with
the ledger data, without breaking the hash links. The hash of the latest block represents every
transaction that has come before, making it possible to ensure that all peers are in a consistent
and trusted state.

链是一个交易日志，它由哈希值链接的区块构造而成，每个区块包含 N 个有序的交易。区块头中包含了本区块内所有交易的总哈希值，以及上一个区块头的哈希值。通过这种方式，账本中的所有交易都以有序的、加密的形式串联在了一起。换而言之，在不破坏哈希链的情况下，是无法篡改账本数据的。最新区块的哈希是之前每一笔交易的体现，从而可以保证所有的节点处于一致的可信任的状态。

The chain is stored on the peer file system (either local or attached storage), efficiently
supporting the append-only nature of the blockchain workload.

链被存放于对等节点的文件系统中（本地的或者挂载的），有效地支持着区块链工作量只追加的特性。

State Database - 状态数据库
-------------------------------

The ledger's current state data represents the latest values for all keys ever included in the chain
transaction log. Since current state represents all latest key values known to the channel, it is
sometimes referred to as World State.

账本的当前状态信息呈现的是链交易日志中记录过的所有键的最新值。由于当前状态表示的是通道已知的所有键的最新值，因此也常被称作世界状态。

Chaincode invocations execute transactions against the current state data. To make these
chaincode interactions extremely efficient, the latest values of all keys are stored in a state
database. The state database is simply an indexed view into the chain's transaction log, it can
therefore be regenerated from the chain at any time. The state database will automatically get
recovered (or generated if needed) upon peer startup, before transactions are accepted.

链码调用基于当前的状态数据执行交易。为了使链码调用高效运行，所有键的最新值被存储在状态数据库中。状态数据库是链的交易日志的索引视图，因此它可以随时从链中重新导出。节点启动的时候，在接受交易之前，状态数据库将被自动恢复（或者根据需要产生）。

State database options include LevelDB and CouchDB. LevelDB is the default state database
embedded in the peer process and stores chaincode data as key/value pairs. CouchDB is an optional
alternative external state database that provides addition query support when your chaincode data
is modeled as JSON, permitting rich queries of the JSON content. See
:doc:`couchdb_as_state_database` for more information on CouchDB.

状态数据库的可选项包括 LevelDB 和 CouchDB。LevelDB 是对等节点中内嵌的默认状态数据库，将链码数据以键值对的形式保存。 CouchDB 是一个可选的外部状态数据库，当链码数据以 JSON 格式建模时，CouchDB 可以提供额外的查询支持，允许使用富查询的方式检索 JSON 内容。阅读 :doc:`couchdb_as_state_database` 获取更多的关于 CouchDB 的信息。

Transaction Flow - 交易流程
----------------------------------

At a high level, the transaction flow consists of a transaction proposal sent by an application
client to specific endorsing peers.  The endorsing peers verify the client signature, and execute
a chaincode function to simulate the transaction. The output is the chaincode results,
a set of key/value versions that were read in the chaincode (read set), and the set of keys/values
that were written in chaincode (write set). The proposal response gets sent back to the client
along with an endorsement signature.

从总体看，交易流程包括了应用客户端发送交易提案给背书节点。背书节点验证客户端的签名，然后执行链码来模拟交易。产生的输出就是链码结果，一组链码读取的键值版本（读集合），和一组被写入链码的键值集合（写集合）。交易提案的响应被发送回客户端，同时包含了背书签名。

The client assembles the endorsements into a transaction payload and broadcasts it to an ordering
service. The ordering service delivers ordered transactions as blocks to all peers on a channel.

客户端汇总所有的背书到一个交易有效载荷中，并将它广播到排序服务。排序服务将排好序的交易放入区块并发送到通道内的所有对等节点。

Before committal, peers will validate the transactions. First, they will check the endorsement
policy to ensure that the correct allotment of the specified peers have signed the results, and they
will authenticate the signatures against the transaction payload.

在提交之前，节点们会验证交易。首先它们会检查背书策略来保证足够的指定节点正确地对结果进行了签名，并且会认证交易有效载荷中的签名。

Secondly, peers will perform a versioning check against the transaction read set, to ensure
data integrity and protect against threats such as double-spending. Hyperledger Fabric has concurrency
control whereby transactions execute in parallel (by endorsers) to increase throughput, and upon
commit (by all peers) each transaction is verified to ensure that no other transaction has modified
data it has read. In other words, it ensures that the data that was read during chaincode execution
has not changed since execution (endorsement) time, and therefore the execution results are still
valid and can be committed to the ledger state database. If the data that was read has been changed
by another transaction, then the transaction in the block is marked as invalid and is not applied to
the ledger state database. The client application is alerted, and can handle the error or retry as
appropriate.

其次，节点们会对交易的读集合进行版本检查，从而保证数据的一致性并防范一些攻击，比如双花。Hyperledger Fabric 拥有并发控制，从而交易可以（被背书节点）并行运行来提高吞吐量，而且当交易（被所有对等节点）提交时，每个交易都会被验证来保证它所读取的数据没有被其他交易更改。换言之，它确保链码执行期间所读取的数据从执行（背书）开始后没有变动。如果读取的数据被其他交易改动了，那么区块中的交易将被标记成无效的，也不会被应用到账本状态数据库。客户端应用会收到提醒，从而进行纠错或适当重试。

See the :doc:`txflow`, :doc:`readwrite`, and :doc:`couchdb_as_state_database` topics for a deeper
dive on transaction structure, concurrency control, and the state DB.

要进一步了解交易的结构、并发控制和状态数据库的相关内容，可以参考 :doc:`txflow`、 :doc:`readwrite` 和 :doc:`couchdb_as_state_database`。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
