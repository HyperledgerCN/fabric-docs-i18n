Read-Write set semantics - 读写集语义
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This documents discusses the details of the current implementation about
the semantics of read-write sets.

本文档讨论了当前代码实现中读写集语义的具体细节。

Transaction simulation and read-write set - 交易模拟和读写集
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

During simulation of a transaction at an ``endorser``, a read-write set
is prepared for the transaction. The ``read set`` contains a list of
unique keys and their committed versions that the transaction reads
during simulation. The ``write set`` contains a list of unique keys
(though there can be overlap with the keys present in the read set) and
their new values that the transaction writes. A delete marker is set (in
the place of new value) for the key if the update performed by the
transaction is to delete the key.

在 ``背书节点`` 模拟执行交易期间，会生成该交易对应的一个读写集。``读集合(read set)`` 包含了在模拟执行交易期间，所读取的一组不重复的 key 及其版本号的列表。``写集合（write set）`` 包含了一组不重复的 key（这些 key 可能和读集合中的 key 有重合）以及该交易写入的这些 key 对应的值。如果交易对应的更新操作是删除某个 key，则该 key 对应的值会被设置删除标记。

Further, if the transaction writes a value multiple times for a key,
only the last written value is retained. Also, if a transaction reads a
value for a key, the value in the committed state is returned even if
the transaction has updated the value for the key before issuing the
read. In another words, Read-your-writes semantics are not supported.

进一步的，如果交易对一个 key 对应的值进行多次写入，只有最后一次的修改会被保留。同样，如果交易读取一个 key 对应的 值，只有之前已经提交的值会被返回，即使在读操作前本交易已经对该 key 的值进行了修改。换而言之，不支持读取同一交易中刚修改的值。

As noted earlier, the versions of the keys are recorded only in the read
set; the write set just contains the list of unique keys and their
latest values set by the transaction.

如前所述，key 对应的版本号只在读集合中被记录；写集合中只包含一组不重复的 key 及其在交易中设置的的最新值。

There could be various schemes for implementing versions. The minimal
requirement for a versioning scheme is to produce non-repeating
identifiers for a given key. For instance, using monotonically
increasing numbers for versions can be one such scheme. In the current
implementation, we use a blockchain height based versioning scheme in
which the height of the committing transaction is used as the latest
version for all the keys modified by the transaction. In this scheme,
the height of a transaction is represented by a tuple (txNumber is the
height of the transaction within the block). This scheme has many
advantages over the incremental number scheme - primarily, it enables
other components such as statedb, transaction simulation and validation
for making efficient design choices.

有多种方案可以用于实现读集合的版本控制。版本控制方案的最基本要求是为 key 生成一个不会重复的标识符。例如，可以使用单调递增的数字来作为版本号。在目前的代码实现中，我们采用了一个基于区块链高度的版本号方案，在该方案中，待提交交易的高度作为该交易对应写集合的所有 key 的最新版本号。交易高度可以用一个元组表示，其中 txNumber 是交易在区块中的高度。该方案相较于递增数字方案有诸多优点，它使得其他组件（例如状态数据库、交易模拟以及验证等）可以选择更高效的设计方案。

Following is an illustration of an example read-write set prepared by
simulation of a hypothetical transaction. For the sake of simplicity, in
the illustrations, we use the incremental numbers for representing the
versions.

如下是一个读写集的示例说明，该读写集通过模拟一个假设交易而生成。为了简单起见，在该示例中我们使用递增数字来表示版本号。

::

    <TxReadWriteSet>
      <NsReadWriteSet name="chaincode1">
        <read-set>
          <read key="K1", version="1">
          <read key="K2", version="1">
        </read-set>
        <write-set>
          <write key="K1", value="V1"
          <write key="K3", value="V2"
          <write key="K4", isDelete="true"
        </write-set>
      </NsReadWriteSet>
    <TxReadWriteSet>

Additionally, if the transaction performs a range query during
simulation, the range query as well as its results will be added to the
read-write set as ``query-info``.

此外，如果在交易模拟执行中进行了一个批量查询，批量查询及其结果都会被添加到读写集的 ``query-info`` 中

Transaction validation and updating world state using read-write set - 交易验证以及使用读写集更新世界状态
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

A ``committer`` uses the read set portion of the read-write set for
checking the validity of a transaction and the write set portion of the
read-write set for updating the versions and the values of the affected
keys.

``提交者(committer)`` 使用读写集中的读集合来验证交易的合法性，使用读写集中的写集合来更新对应 key 的值和版本号。

In the validation phase, a transaction is considered ``valid`` if the
version of each key present in the read set of the transaction matches
the version for the same key in the world state - assuming all the
preceding ``valid`` transactions (including the preceding transactions
in the same block) are committed (*committed-state*). An additional
validation is performed if the read-write set also contains one or more
query-info.

在验证阶段，交易被认为 ``有效(valid)`` 的条件是该交易读集合中的每一个 key 对应的版本号同最新世界状态中该 key 对应的版本号全都一致，这个最新世界状态是之前所有 ``有效`` 交易（包括同一区块中排在前面的交易）提交之后的状态。如果读写集包含一个或者多个 query-info，则需要进行一个额外的验证。

This additional validation should ensure that no key has been
inserted/deleted/updated in the super range (i.e., union of the ranges)
of the results captured in the query-info(s). In other words, if we
re-execute any of the range queries (that the transaction performed
during simulation) during validation on the committed-state, it should
yield the same results that were observed by the transaction at the time
of simulation. This check ensures that if a transaction observes phantom
items during commit, the transaction should be marked as invalid. Note
that the this phantom protection is limited to range queries (i.e.,
``GetStateByRange`` function in the chaincode) and not yet implemented
for other queries (i.e., ``GetQueryResult`` function in the chaincode).
Other queries are at risk of phantoms, and should therefore only be used
in read-only transactions that are not submitted to ordering, unless the
application can guarantee the stability of the result set between
simulation and validation/commit time.

该额外验证需要确保在一定范围之内（例如多个范围的联合）没有 key 被插入、删除或更新。换句话说，如果我们在验证阶段重新执行批量查询（正如在交易模拟执行阶段一样），得到的查询结果应该和在交易模拟执行阶段完全一致。此校验确保交易在提交时如果出现幻读会被认为是无效的。值得注意的是，这种针对幻读的保护只被用于部分批量查询（例如链码中的 ``GetStateByRange`` 函数），并没有被用于其他的批量查询（例如链码中的 ``GetQueryResult`` 函数）。没有保护的批量查询方法会有幻读风险，因此这种查询应该只用于不会被提交到排序服务的 ``只读交易``，除非应用方能保证交易模拟和交易验证提交两阶段之间结果集是稳定不变的。

If a transaction passes the validity check, the committer uses the write
set for updating the world state. In the update phase, for each key
present in the write set, the value in the world state for the same key
is set to the value as specified in the write set. Further, the version
of the key in the world state is changed to reflect the latest version.

如果一个交易通过了有效性检查，提交者使用写集合更新世界状态。在更新阶段，对于写集合中的每一个 key，世界状态中该 key 对应的值都被设置为写集合中的值。进一步的，世界状态中该 key 对应的版本号也会被修改为最新的版本号。

Example simulation and validation - 模拟和验证示例
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''

This section helps with understanding the semantics through an example
scenario. For the purpose of this example, the presence of a key, ``k``,
in the world state is represented by a tuple ``(k,ver,val)`` where
``ver`` is the latest version of the key ``k`` having ``val`` as its
value.

本节通过一个具体的实例来帮助理解读写集语义。为了理解实例方便，世界状态中 key ``k`` 使用如下元组来表示 ``(k,ver,val)``，其中 ``ver`` 表示该 key ``k`` 对应的最新版本号，``val`` 表示其对应的值。

Now, consider a set of five transactions ``T1, T2, T3, T4, and T5``, all
simulated on the same snapshot of the world state. The following snippet
shows the snapshot of the world state against which the transactions are
simulated and the sequence of read and write activities performed by
each of these transactions.

现在，考虑 5 个交易的集合 ``T1, T2, T3, T4 和 T5``，每个交易都是基于相同的世界状态快照进行模拟执行。如下代码展示了交易模拟执行对应的世界状态，以及每个交易中所包含的读写动作。

::

    World state: (k1,1,v1), (k2,1,v2), (k3,1,v3), (k4,1,v4), (k5,1,v5)
    T1 -> Write(k1, v1'), Write(k2, v2')
    T2 -> Read(k1), Write(k3, v3')
    T3 -> Write(k2, v2'')
    T4 -> Write(k2, v2'''), read(k2)
    T5 -> Write(k6, v6'), read(k5)

Now, assume that these transactions are ordered in the sequence of
T1,..,T5 (could be contained in a single block or different blocks)

现在，假设这些交易的排序如下 T1,...,T5（可能包含在同一区块或多个区块中）

1. ``T1`` passes validation because it does not perform any read.
   Further, the tuple of keys ``k1`` and ``k2`` in the world state are
   updated to ``(k1,2,v1'), (k2,2,v2')``

1. ``T1`` 通过验证，因为它没有任何的读操作。
   随后，世界状态中 ``k1`` 和 ``k2`` 对应的元组被更新为 ``(k1,2,v1'), (k2,2,v2')``

2. ``T2`` fails validation because it reads a key, ``k1``, which was
   modified by a preceding transaction - ``T1``

2. ``T2`` 验证失败，因为它读取了 key ``k1``，而 ``k1`` 在之前的 ``T1`` 交易中被修改

3. ``T3`` passes the validation because it does not perform a read.
   Further the tuple of the key, ``k2``, in the world state is updated
   to ``(k2,3,v2'')``

3. ``T3`` 通过验证，因为它没有任何的读操作。
   随后，世界状态中 ``k2`` 对应的元组被更新为 ``(k2,3,v2'')``

4. ``T4`` fails the validation because it reads a key, ``k2``, which was
   modified by a preceding transaction ``T1``

4. ``T4`` 验证失败，因为它读取了 key ``k2``，而 ``k2`` 在之前的 ``T1`` 交易中被修改

5. ``T5`` passes validation because it reads a key, ``k5,`` which was
   not modified by any of the preceding transactions

5. ``T5`` 通过验证，因为它只读取了 key ``k5``，而 ``k5`` 在之前的所有交易中都并未被修改   

**Note**: Transactions with multiple read-write sets are not yet supported.

**注意**: 暂不支持包含多个读写集的交易。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/

