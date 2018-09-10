Chaincode for Operators
=======================
面向运营商的链码
=======================

What is Chaincode?
------------------
什么是链码？
------------------

Chaincode is a program, written in `Go <https://golang.org>`_, and eventually
in other programming languages such as Java, that implements a
prescribed interface. Chaincode runs in a secured Docker container isolated from
the endorsing peer process. Chaincode initializes and manages ledger state
through transactions submitted by applications.

链码是一个程序，用`Go <https://golang.org>`_语言写的，并最终在其他编程语言(如Java)中实现指定的接口。Chaincode运行在一个受保护的Docker容器中，该容器与代理对等进程隔离。Chaincode通过应用程序所提交的事务来初始化和管理分类帐的状态。

A chaincode typically handles business logic agreed to by members of the
network, so it may be considered as a "smart contract". State created by a
chaincode is scoped exclusively to that chaincode and can't be accessed
directly by another chaincode. However, within the same network, given
the appropriate permission a chaincode may invoke another chaincode to
access its state.

链码通常处理由网络中成员都同意的业务逻辑，因此可以将其视为“智能合约”。由链码创建的状态的作用域仅限于该链码，不能由另一个链码直接访问。但是，在同一网络中，给定适当的权限，链码可能会调用另一个链码来访问其状态。

In the following sections, we will explore chaincode through the eyes of a
blockchain network operator, Noah. For Noah's interests, we will focus
on chaincode lifecycle operations; the process of packaging, installing,
instantiating and upgrading the chaincode as a function of the chaincode's
operational lifecycle within a blockchain network.

在下面的部分中，我们将通过区块链网络运营商Noah的眼睛探索链码。为了Noah的利益，我们将专注于链码生命周期运营; 根据区块链网络中链码的运营生命周期，打包，安装，实例化和升级链码的过程。

Chaincode lifecycle
--------------------
链码生命周期
--------------------

The Hyperledger Fabric API enables interaction with the various nodes
in a blockchain network - the peers, orderers and MSPs - and it also allows
one to package, install, instantiate and upgrade chaincode on the endorsing
peer nodes. The Hyperledger Fabric language-specific SDKs
abstract the specifics of the Hyperledger Fabric API to facilitate
application development, though it can be used to manage a chaincode's
lifecycle. Additionally, the Hyperledger Fabric API can be accessed
directly via the CLI, which we will use in this document.

Hyperledger Fabric API允许与区块链网络中的不同节点-对等节点、背书节点和MSP-进行交互，它还允许在背书对等节点上打包、安装、实例化和升级链码。Hyperledger Fabric语言特定的SDK抽象了Hyperledger Fabric API的细节，以促进应用程序的开发，尽管它可以用于管理链码的生命周期。此外，可以通过CLI直接访问Hyperledger Fabric API，我们将在本文档中使用CLI。

We provide four commands to manage a chaincode's lifecycle: ``package``,
``install``, ``instantiate``, and ``upgrade``. In a future release, we are
considering adding ``stop`` and ``start`` transactions to disable and re-enable
a chaincode without having to actually uninstall it. After a chaincode has
been successfully installed and instantiated, the chaincode is active (running)
and can process transactions via the ``invoke`` transaction. A chaincode may be
upgraded any time after it has been installed.

我们提供四个命令来管理链码的生命周期：``package``, ``install``, ``instantiate``，和 ``upgrade``。在未来的发行版中，我们正在考虑添加 ``stop`` 和``start`` 命令来禁用和重新启用链码的事务，而不必实际卸载它。在成功安装和实例化链码之后，链码是活动的(正在运行)，可以通过``invoke`` 交易。链码可在安装后随时升级。

.. _Package:

Packaging
---------
打包
---------

The chaincode package consists of 3 parts:

链码包由三个部分组成：

  - the chaincode, as defined by ``ChaincodeDeploymentSpec`` or CDS. The CDS
    defines the chaincode package in terms of the code and other properties
    such as name and version,

  - 由ChaincodeDeploymentSpec或者CDS所定义的链码。CDS根据代码和其他属性(如名称和版本)定义链码包

  - an optional instantiation policy which can be syntactically described
    by the same policy used for endorsement and described in
    :doc:`endorsement-policies`, and

  - 一种可选的实例化策略，该策略可以由用于背书的相同策略进行语法描述，并在:doc:`endorsement-policies`中进行描述

  - a set of signatures by the entities that “own” the chaincode.

  - 由“拥有”链码的实体签署的一组签名

The signatures serve the following purposes:

签字的目的如下：

  - to establish an ownership of the chaincode,

  - 要建立链码的所有权

  - to allow verification of the contents of the package, and

  - 允许验证包的内容

  - to allow detection of package tampering.

  - 允许检测到包裹被篡改

The creator of the instantiation transaction of the chaincode on a channel is
validated against the instantiation policy of the chaincode.

根据链码的实例化策略来验证通道上链码实例化事务的创建者。

Creating the package
^^^^^^^^^^^^^^^^^^^^
创建包
^^^^^^^^^^^^^^^^^^^^

There are two approaches to packaging chaincode. One for when you want to have
multiple owners of a chaincode, and hence need to have the chaincode package
signed by multiple identities. This workflow requires that we initially create a
signed chaincode package (a ``SignedCDS``) which is subsequently passed serially
to each of the other owners for signing.

有两种方法来打包链码。一个用于当您希望一个链码拥有多个所有者时，因此需要一个由多个身份签名的链码包。此工作流要求我们最初创建一个签名的链码包(``SignedCDS``)，随后依次传递给每个其他所有者进行签名。

The simpler workflow is for when you are deploying a SignedCDS that has only the
signature of the identity of the node that is issuing the ``install``
transaction.

更简单的工作流程适用于当部署仅具有发出 ``install`` 事务的节点标识的签名的SignedCDS的时候。

We will address the more complex case first. However, you may skip ahead to the
:ref:`Install` section below if you do not need to worry about multiple owners
just yet.

我们将首先处理更复杂的案件。但是，如果您不需要担心多个所有者问题，可以跳到下面的：ref：`Install`部分。

To create a signed chaincode package, use the following command:

若要创建签名的链码包，请使用以下命令：

.. code:: bash

    peer chaincode package -n mycc -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02 -v 0 -s -S -i "AND('OrgA.admin')" ccpack.out

The ``-s`` option creates a package that can be signed by multiple owners as
opposed to simply creating a raw CDS. When ``-s`` is specified, the ``-S``
option must also be specified if other owners are going to need to sign.
Otherwise, the process will create a SignedCDS that includes only the
instantiation policy in addition to the CDS.

``-s`` 选项创建一个可由多个所有者签名的包，而不是简单地创建原始CDS。 指定 ``-s`` 时，如果其他所有者需要签名，则还必须指定 ``-S`` 选项。 否则，该过程将创建一个SignedCDS，仅包括实例化策略而除了CDS.。

The ``-S`` option directs the process to sign the package
using the MSP identified by the value of the ``localMspid`` property in
``core.yaml``.

``-S`` 选项指示进程使用由 ``core.yaml`` 中 ``localMspid`` 属性的值标识的MSP对包进行签名。

The ``-S`` option is optional. However if a package is created without a
signature, it cannot be signed by any other owner using the
``signpackage`` command.

``-S`` 选项是可选的。 但是，如果创建的包没有签名，则任何其他所有者都无法使用 ``signpackage`` 命令对其进行签名。

The optional ``-i`` option allows one to specify an instantiation policy
for the chaincode. The instantiation policy has the same format as an
endorsement policy and specifies which identities can instantiate the
chaincode. In the example above, only the admin of OrgA is allowed to
instantiate the chaincode. If no policy is provided, the default policy
is used, which only allows the admin identity of the peer's MSP to
instantiate chaincode.

可选的 ``-i`` 选项允许为链码指定实例化策略。 实例化策略具有与背书策略相同的格式，并指定哪些身份可以实例化链代码。 在上面的示例中，只允许OrgA的管理员实例化链代码。 如果未提供策略，则使用默认策略，该策略仅允许节点的MSP的管理员标识实例化链代码。

Package signing
^^^^^^^^^^^^^^^
包签名
^^^^^^^^^^^^^^^
A chaincode package that was signed at creation can be handed over to other
owners for inspection and signing. The workflow supports out-of-band signing
of chaincode package.

在创建时签署的链代码包可以移交给其他所有者进行检查和签名。该工作流程支持链码包的带外签名。

The
`ChaincodeDeploymentSpec <https://github.com/hyperledger/fabric/blob/master/protos/peer/chaincode.proto#L78>`_
may be optionally be signed by the collective owners to create a
`SignedChaincodeDeploymentSpec <https://github.com/hyperledger/fabric/blob/master/protos/peer/signed_cc_dep_spec.proto#L26>`_
(or SignedCDS). The SignedCDS contains 3 elements:

`ChaincodeDeploymentSpec <https://github.com/hyperledger/fabric/blob/master/protos/peer/chaincode.proto#L78>`_ 可以选择由集体所有者签名从而来创建`SignedChaincodeDeploymentSpec <https://github.com/hyperledger/fabric/blob/master/protos/peer/signed_cc_dep_spec.proto#L26>`_ （或SignedCDS）。 SignedCDS包含3个元素：

  1. The CDS contains the source code, the name, and version of the chaincode.

  1. CDS包含链码的源代码，名称和版本

  2. An instantiation policy of the chaincode, expressed as endorsement policies.

  2. 链代码的实例化策略，表示为背书策略 

  3. The list of chaincode owners, defined by means of
     `Endorsement <https://github.com/hyperledger/fabric/blob/master/protos/peer/proposal_response.proto#L111>`_.

  3. 通过`背书 <https://github.com/hyperledger/fabric/blob/master/protos/peer/proposal_response.proto#L111>`_ 定义的链码所有者列表

.. note:: Note that this endorsement policy is determined out-of-band to
          provide proper MSP principals when the chaincode is instantiated
          on some channels. If the instantiation policy is not specified,
          the default policy is any MSP administrator of the channel.


.. note:: 请注意，此绑定策略是在带外确定的，以便在某些通道上实例化链代码时提供适当的MSP主体。 如果未指定实例化策略，则默认策略是该通道的任何MSP管理员。

Each owner endorses the ChaincodeDeploymentSpec by combining it
with that owner's identity (e.g. certificate) and signing the combined
result.

每个所有者通过将ChaincodeDeploymentSpec与该所有者的身份（例如证书）相结合并签署合并结果来认可ChaincodeDeploymentSpec。

A chaincode owner can sign a previously created signed package using the
following command:

链代码所有者可以使用以下命令对先前创建的已签名包进行签名：

.. code:: bash

    peer chaincode signpackage ccpack.out signedccpack.out

Where ``ccpack.out`` and ``signedccpack.out`` are the input and output
packages, respectively. ``signedccpack.out`` contains an additional
signature over the package signed using the Local MSP.

其中``ccpack.out`` 和``signedccpack.out`` 分别是输入和输出包。 ``signedccpack.out`` 包含使用本地MSP签名的程序包的附加签名。

.. _Install:

Installing chaincode
^^^^^^^^^^^^^^^^^^^^
安装链码
^^^^^^^^^^^^^^^^^^^^

The ``install`` transaction packages a chaincode's source code into a prescribed
format called a ``ChaincodeDeploymentSpec`` (or CDS) and installs it on a
peer node that will run that chaincode.

``install`` 事务将链代码的源代码打包成称为``ChaincodeDeploymentSpec`` （或CDS）的规定格式，并将其安装在将运行该链代码的节点上。

.. note:: You must install the chaincode on **each** endorsing peer node
          of a channel that will run your chaincode.

.. note:: 您必须在将运行您的链代码的通道的 **每个** 背书节点上安装链代码。

When the ``install`` API is given simply a ``ChaincodeDeploymentSpec``,
it will default the instantiation policy and include an empty owner list.

如果只为 ``ChaincodeDeploymentSpec`` 提供 ``install`` API，它将默认实例化策略并包含一个空的所有者列表。

.. note:: Chaincode should only be installed on endorsing peer nodes of the
          owning members of the chaincode to protect the confidentiality of 
          the chaincode logic from other members on the network. Those members
          without the chaincode, can't be the endorsers of the chaincode's
          transactions; that is, they can't execute the chaincode. However,
          they can still validate and commit the transactions to the ledger.

.. note:: Chaincode只应安装在拥有链码成员的背书节点上，以保护链码逻辑与网络上其他成员的机密性。 那些没有链码的成员，不能成为链码交易的代言人; 也就是说，他们无法执行链码。 但是，他们仍然可以验证事务并将其提交到分类帐。

To install a chaincode, send a `SignedProposal
<https://github.com/hyperledger/fabric/blob/master/protos/peer/proposal.proto#L104>`_
to the ``lifecycle system chaincode`` (LSCC) described in the `System Chaincode`_
section. For example, to install the **sacc** sample chaincode described
in section :ref:`simple asset chaincode`
using the CLI, the command would look like the following:

要安装链代码，请将 `SignedProposal
<https://github.com/hyperledger/fabric/blob/master/protos/peer/proposal.proto#L104>`_ 发送到 `System Chaincode` 中描述的 ``lifecycle system chaincode (LSCC)``。 例如，要使用CLI安装 ref：`simple asset chaincode`中描述的 **sacc** 示例链代码，命令将如下所示：

.. code:: bash

    peer chaincode install -n asset_mgmt -v 1.0 -p sacc

The CLI internally creates the SignedChaincodeDeploymentSpec for **sacc** and
sends it to the local peer, which calls the ``Install`` method on the LSCC. The
argument to the ``-p`` option specifies the path to the chaincode, which must be
located within the source tree of the user's ``GOPATH``, e.g.
``$GOPATH/src/sacc``. See the `CLI`_ section for a complete description of
the command options.

CLI在内部为 **sacc** 创建SignedChaincodeDeploymentSpec并将其发送到本地节点，后者在LSCC上调用 ``Install`` 方法。 ``-p`` 选项的参数指定了链代码的路径，该链代码必须位于用户 ``GOPATH`` 的源树中，例如，``$GOPATH/src/sacc`` 。 有关命令选项的完整说明，请参阅 `CLI`_ 部分。

Note that in order to install on a peer, the signature of the SignedProposal
must be from 1 of the peer's local MSP administrators.

请注意，为了在节点上安装，SignedProposal的签名必须来自节点的本地MSP管理员之一。

.. _Instantiate:

Instantiate
^^^^^^^^^^^
实例化
^^^^^^^^^^^

The ``instantiate`` transaction invokes the ``lifecycle System Chaincode``
(LSCC) to create and initialize a chaincode on a channel. This is a
chaincode-channel binding process: a chaincode may be bound to any number of
channels and operate on each channel individually and independently. In other
words, regardless of how many other channels on which a chaincode might be
installed and instantiated, state is kept isolated to the channel to which
a transaction is submitted.

``instantiate`` 事务调用 ``lifecycle System Chaincode``（LSCC）来创建和初始化通道上的链代码。 这是一个链码通道绑定过程：链码可以绑定到任意数量的通道，并且可以独立地在每个通道上运行。 换句话说，无论一个链代码在多少其他通道上安装和实例化，状态都与提交事务的通道保持隔离。

The creator of an ``instantiate`` transaction must satisfy the instantiation
policy of the chaincode included in SignedCDS and must also be a writer on the
channel, which is configured as part of the channel creation. This is important
for the security of the channel to prevent rogue entities from deploying
chaincodes or tricking members to execute chaincodes on an unbound channel.

``instantiate`` 事务的创建者必须满足SignedCDS中包含的链代码的实例化策略，并且还必须是通道上的写入器，其被配置为通道创建的一部分。 这对于通道的安全性非常重要，可以防止恶意实体部署链代码或欺骗成员在未绑定的通道上执行链代码。

For example, recall that the default instantiation policy is any channel MSP
administrator, so the creator of a chaincode instantiate transaction must be a
member of the channel administrators. When the transaction proposal arrives at
the endorser, it verifies the creator's signature against the instantiation
policy. This is done again during the transaction validation before committing
it to the ledger.

例如，回想一下默认实例化策略是任何通道MSP管理员，因此链代码实例化事务的创建者必须是通道管理员的成员。 当交易提议到达背书时，它会根据实例化策略验证创建者的签名。 在将其提交到分类账之前，在事务验证期间再次执行此操作。

The instantiate transaction also sets up the endorsement policy for that
chaincode on the channel. The endorsement policy describes the attestation
requirements for the transaction result to be accepted by members of the
channel.

实例化事务还为通道上的该链代码设置了背书策略。 背书策略描述了通道成员接受交易结果的认证要求。

For example, using the CLI to instantiate the **sacc** chaincode and initialize
the state with ``john`` and ``0``, the command would look like the following:

例如，使用CLI实例化 **sacc** 链代码并使用 ``john`` 和 ``0`` 初始化状态，该命令将如下所示：

.. code:: bash

    peer chaincode instantiate -n sacc -v 1.0 -c '{"Args":["john","0"]}' -P "OR ('Org1.member','Org2.member')"

.. note:: Note the endorsement policy (CLI uses polish notation), which requires an
          endorsement from either member of Org1 or Org2 for all transactions to
          **sacc**. That is, either Org1 or Org2 must sign the
          result of executing the `Invoke` on **sacc** for the transactions to
          be valid.

.. note:: 请注意背书策略（CLI使用波兰表示法），这需要得到Org1或Org2成员对所有 **sacc** 交易的认可。 也就是说，Org1或Org2必须对在 **sacc** 上执行 `Invoke` 的结果进行签署才能使事务有效。

After being successfully instantiated, the chaincode enters the active state on
the channel and is ready to process any transaction proposals of type
`ENDORSER_TRANSACTION <https://github.com/hyperledger/fabric/blob/master/protos/common/common.proto#L42>`_.
The transactions are processed concurrently as they arrive at the endorsing
peer.

成功实例化后，链代码在通道上进入活动状态，并准备处理 `ENDORSER_TRANSACTION <https://github.com/hyperledger/fabric/blob/master/protos/common/common.proto#L42>`_ 类型的任何交易提议。 事务在到达背书节点时被并发处理。

.. _Upgrade:

Upgrade
^^^^^^^
升级
^^^^^^^
A chaincode may be upgraded any time by changing its version, which is
part of the SignedCDS. Other parts, such as owners and instantiation policy
are optional. However, the chaincode name must be the same; otherwise it
would be considered as a totally different chaincode.

可以通过更改其版本来随时升级链码，版本是SignedCDS的一部分。 其他部分，例如所有者和实例化策略是可选的。 但是，链代码名称必须相同; 否则它将被视为完全不同的链码。

Prior to upgrade, the new version of the chaincode must be installed on
the required endorsers. Upgrade is a transaction similar to the instantiate
transaction, which binds the new version of the chaincode to the channel. Other
channels bound to the old version of the chaincode still run with the old
version. In other words, the ``upgrade`` transaction only affects one channel
at a time, the channel to which the transaction is submitted.

在升级之前，必须在所需的背书上安装新版本的链代码。 升级是一种类似于实例化事务的事务，它将新版本的链码绑定到信道上。 绑定到旧版链代码的其他信道仍然使用旧版本运行。 换句话说，``upgrade`` 事务一次只影响一个通道，即提交事务的通道。

.. note:: Note that since multiple versions of a chaincode may be active
          simultaneously, the upgrade process doesn't automatically remove the
          old versions, so user must manage this for the time being.

.. note:: 请注意，由于链代码的多个版本可能同时处于活动状态，升级过程不会自动删除旧版本，因此用户必须暂时对其进行管理。

There's one subtle difference with the ``instantiate`` transaction: 

``instantiate`` 事务有一个细微的区别：

the ``upgrade`` transaction is checked against the current chaincode instantiation
policy, not the new policy (if specified). This is to ensure that only existing
members specified in the current instantiation policy may upgrade the chaincode.

根据当前的链代码实例化策略检查 ``upgrade`` 事务，而不是新策略（如果指定）。 这是为了确保只有当前实例化策略中指定的现有成员才能升级链代码。

.. note:: Note that during upgrade, the chaincode ``Init`` function is called to
          perform any data related updates or re-initialize it, so care must be
          taken to avoid resetting states when upgrading chaincode.

.. note:: 请注意，在升级期间，调用链代码 ``Init`` 函数以执行任何与数据相关的更新或重新初始化它的操作，因此必须注意避免在升级链代码时重置状态。

.. _Stop-and-Start:

Stop and Start
^^^^^^^^^^^^^^
停止和启动
^^^^^^^^^^^^^^
Note that ``stop`` and ``start`` lifecycle transactions have not yet been
implemented. However, you may stop a chaincode manually by removing the
chaincode container and the SignedCDS package from each of the endorsers. This
is done by deleting the chaincode's container on each of the hosts or virtual
machines on which the endorsing peer nodes are running, and then deleting
the SignedCDS from each of the endorsing peer nodes:

请注意，尚未实现 ``stop`` 和 ``start`` 生命周期事务。 但是，您可以通过从每个背书中删除链代码容器和SignedCDS包来手动停止链代码。 这是通过删除每个主机或虚拟机上的链码容器来完成的，这些主机或虚拟机上正在运行背书节点，然后从每个背书节点中删除签名dCDS：

.. note:: TODO - in order to delete the CDS from the peer node, you would need
          to enter the peer node's container, first. We really need to provide
          a utility script that can do this.

.. note:: TODO - 为了从节点中删除CDS，首先需要进入节点的容器。 我们真的需要提供一个可以执行此操作的实用程序脚本。

.. code:: bash

    docker rm -f <container id>
    rm /var/hyperledger/production/chaincodes/<ccname>:<ccversion>

Stop would be useful in the workflow for doing upgrade in controlled manner,
where a chaincode can be stopped on a channel on all peers before issuing an
upgrade.

在工作流中，停止将有助于以受控的方式进行升级，在发出升级之前，可以在所有节点上的通道上停止链码。

.. _CLI:

CLI
^^^

.. note:: We are assessing the need to distribute platform-specific binaries
          for the Hyperledger Fabric ``peer`` binary. For the time being, you
          can simply invoke the commands from within a running docker container.

.. note:: 我们正在评估为Hyperledger Fabric ``peer`` 二进制文件分发特定于平台的二进制文件的需求。 目前，您只需从正在运行的docker容器中调用命令即可。

To view the currently available CLI commands, execute the following command from
within a running ``fabric-peer`` Docker container:

要查看当前可用的CLI命令，请在正在运行的 ``fabric-peer`` Docker容器中执行以下命令：

.. code:: bash

    docker run -it hyperledger/fabric-peer bash
    # peer chaincode --help

Which shows output similar to the example below:

其中显示的输出类似于以下示例：

.. code:: bash

    Usage:
      peer chaincode [command]

    Available Commands:
      install     Package the specified chaincode into a deployment spec and save it on the peer's path.
      instantiate Deploy the specified chaincode to the network.
      invoke      Invoke the specified chaincode.
      list        Get the instantiated chaincodes on a channel or installed chaincodes on a peer.
      package     Package the specified chaincode into a deployment spec.
      query       Query using the specified chaincode.
      signpackage Sign the specified chaincode package
      upgrade     Upgrade chaincode.

    Flags:
          --cafile string      Path to file containing PEM-encoded trusted certificate(s) for the ordering endpoint
      -h, --help               help for chaincode
      -o, --orderer string     Ordering service endpoint
          --tls                Use TLS when communicating with the orderer endpoint
          --transient string   Transient map of arguments in JSON encoding

    Global Flags:
          --logging-level string       Default logging level and overrides, see core.yaml for full syntax
          --test.coverprofile string   Done (default "coverage.cov")
      -v, --version

    Use "peer chaincode [command] --help" for more information about a command.

To facilitate its use in scripted applications, the ``peer`` command always
produces a non-zero return code in the event of command failure.

为了便于在脚本应用程序中使用它，``peer`` 命令总是在发生命令失败时生成非零返回代码。

Example of chaincode commands:

链码命令示例：

.. code:: bash

    peer chaincode install -n mycc -v 0 -p path/to/my/chaincode/v0
    peer chaincode instantiate -n mycc -v 0 -c '{"Args":["a", "b", "c"]}' -C mychannel
    peer chaincode install -n mycc -v 1 -p path/to/my/chaincode/v1
    peer chaincode upgrade -n mycc -v 1 -c '{"Args":["d", "e", "f"]}' -C mychannel
    peer chaincode query -C mychannel -n mycc -c '{"Args":["query","e"]}'
    peer chaincode invoke -o orderer.example.com:7050  --tls --cafile $ORDERER_CA -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'

.. _System Chaincode:

System chaincode
----------------
系统链码
----------------

System chaincode has the same programming model except that it runs within the
peer process rather than in an isolated container like normal chaincode.
Therefore, system chaincode is built into the peer executable and doesn't follow
the same lifecycle described above. In particular, **install**, **instantiate**
and **upgrade** do not apply to system chaincodes.

系统链码具有相同的编程模型，只不过它在节点进程中运行，而不是像普通链码那样在孤立的容器中运行。因此，系统链码被内置到节点可执行文件中，并且不遵循上述相同的生命周期。特别是，**安装**, **实例化** 和 **升级** 不适用于系统链码。

The purpose of system chaincode is to shortcut gRPC communication cost between
peer and chaincode, and tradeoff the flexibility in management. For example, a
system chaincode can only be upgraded with the peer binary. It must also
register with a `fixed set of parameters
<https://github.com/hyperledger/fabric/blob/master/core/scc/importsysccs.go>`_
compiled in and doesn't have endorsement policies or endorsement policy
functionality.

系统链码的目的是缩短节点和链码之间的GRPC通信开销，并权衡管理的灵活性。例如，系统链码只能使用节点二进制文件进行升级。它还必须向 `固定参数集
<https://github.com/hyperledger/fabric/blob/master/core/scc/importsysccs.go>`_ 编译且不具有背书策略或背书策略功能。

System chaincode is used in Hyperledger Fabric to implement a number of
system behaviors so that they can be replaced or modified as appropriate
by a system integrator.

系统链码用于Hyperledger Fabric中，以实现多个系统行为，以便系统集成商可以适当地替换或修改这些行为。

The current list of system chaincodes:

当前的系统链码列表：

1. `LSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/lscc>`_
   Lifecycle system chaincode handles lifecycle requests described above.
2. `CSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/cscc>`_
   Configuration system chaincode handles channel configuration on the peer side.
3. `QSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/qscc>`_
   Query system chaincode provides ledger query APIs such as getting blocks and
   transactions.
4. `ESCC <https://github.com/hyperledger/fabric/tree/master/core/scc/escc>`_
   Endorsement system chaincode handles endorsement by signing the transaction
   proposal response.
5. `VSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/vscc>`_
   Validation system chaincode handles the transaction validation, including
   checking endorsement policy and multiversioning concurrency control.

1. `LSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/lscc>`_ 生命周期系统链码处理上面描述的生命周期请求。

2. `CSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/cscc>`_ 配置系统链码处理对等端的信道配置。

3. `QSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/qscc>`_ 查询系统链码提供分类账查询API，例如获取块和事务。

4. `ESCC <https://github.com/hyperledger/fabric/tree/master/core/scc/escc>`_ 背书系统链码通过签署交易建议书响应来处理背书。

5. `VSCC <https://github.com/hyperledger/fabric/tree/master/core/scc/vscc>`_ 验证系统链码处理事务验证，包括检查批注策略和多版本控制并发控制。

Care must be taken when modifying or replacing these system chaincodes,
especially LSCC, ESCC and VSCC since they are in the main transaction execution
path. It is worth noting that as VSCC validates a block before committing it to
the ledger, it is important that all peers in the channel compute the same
validation to avoid ledger divergence (non-determinism). So special care is
needed if VSCC is modified or replaced.

在修改或替换这些系统链码时必须小心，特别是LSCC、ESCC和VSCC，因为它们处于主要事务执行路径。值得注意的是，当VSCC在将块提交到分类帐之前对其进行验证时，重要的是信道中的所有节点都要计算相同的验证，以避免分类帐差异(非确定性)。因此，如果VSCC被修改或替换，就需要特别的注意。


.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
