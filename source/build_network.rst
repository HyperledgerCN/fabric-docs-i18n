*Needs Review*

Building Your First Network - 构建你的第一个网络
================================================================================

.. note:: These instructions have been verified to work against the
          latest stable Docker images and the pre-compiled
          setup utilities within the supplied tar file. If you run
          these commands with images or tools from the current master
          branch, it is possible that you will see configuration and panic
          errors.

          本篇指南是针对稳定版（v1.1.0） Docker 镜像和预编译好的工具进行设计和校验。
          如果你基于当前代码的 master 分支运行本篇指南提到的命令，有可能会遇到配置错误或者宕机等问题。

The build your first network (BYFN) scenario provisions a sample Hyperledger
Fabric network consisting of two organizations, each maintaining two peer
nodes, and a "solo" ordering service.

本方案 ("构建你的第一个网络"，BYFN) 提供了一个 Hyperledger Fabric 的示例网络，该网络包含两个机构 (Organization)，每个机构各自拥有两个对等节点 (peer)，并提供 “独自(solo)” 模式的排序服务 (Ordering Service)。

Install prerequisites - 安装依赖
--------------------------------------------------------------------------------

Before we begin, if you haven't already done so, you may wish to check that
you have all the :doc:`prereqs` installed on the platform(s)
on which you'll be developing blockchain applications and/or operating
Hyperledger Fabric.

在开始前，请确认你已经按照 :doc:`prereqs` 的介绍，在你准备开发和运行 Hyperledger Fabric 的电脑上安装了所有的依赖软件。

You will also need to download and install the :doc:`samples`. You will notice
that there are a number of samples included in the ``fabric-samples``
repository. We will be using the ``first-network`` sample. Let's open that
sub-directory now.

你还需要下载和安装 :doc:`samples`。你会注意到在 ``fabric-samples`` 项目中包含了一系列的示例。我们会使用 ``first-network`` 示例，下面请进入该示例的子目录：

.. code:: bash

  cd fabric-samples/first-network

.. note:: The supplied commands in this documentation
          **MUST** be run from your ``first-network`` sub-directory
          of the ``fabric-samples`` repository clone.  If you elect to run the
          commands from a different location, the various provided scripts
          will be unable to find the binaries.

          本文档中提供的命令 **必须** 在 ``fabric-samples`` 项目的 ``first-network`` 子目录下执行。如果你从其他位置执行命令，示例中提供的各种脚本将无法找到所需的可执行文件。

Want to run it now? - 是否已经迫不及待想要开始了？
--------------------------------------------------------------------------------

We provide a fully annotated script - ``byfn.sh`` - that leverages these Docker
images to quickly bootstrap a Hyperledger Fabric network comprised of 4 peers
representing two different organizations, and an orderer node. It will also
launch a container to run a scripted execution that will join peers to a
channel, deploy and instantiate chaincode and drive execution of transactions
against the deployed chaincode.

我们提供了一个包含完整注释的脚本 ``byfn.sh`` ，利用 Docker 镜像快速构建起一个 Hyperledger Fabric 网络，该网络包含 2 个机构下的共 4 个对等节点 (peer)，以及 1 个排序服务节点 (orderer)。同时，我们还会启动一个容器来运行脚本，实现如下功能：将对等节点添加到通道 (channel)、部署和初始化链码 (chaincode) 以及执行已部署链码的交易。

Here's the help text for the ``byfn.sh`` script:

如下是 ``byfn.sh`` 脚本的帮助说明

.. code:: bash

  ./byfn.sh --help
  Usage:
  byfn.sh up|down|restart|generate [-c <channel name>] [-t <timeout>] [-d <delay>] [-f <docker-compose-file>] [-s <dbtype>]
  byfn.sh -h|--help (print this message 打印本消息)
    -m <mode> - one of 'up', 'down', 'restart' or 'generate'
      - 'up' - bring up the network with docker-compose up 使用 docker-compose up 命令启动本网络
      - 'down' - clear the network with docker-compose down 使用 docker-compose down 命令关闭本网络
      - 'restart' - restart the network 重启本网络
      - 'generate' - generate required certificates and genesis block 生成需要的证书文件和初始区块
    -c <channel name> - channel name to use (defaults to "mychannel") channel 名称 (默认是 "mychannel")
    -t <timeout> - CLI timeout duration in seconds (defaults to 10) 客户端超时时间 (默认是 10 秒)
    -d <delay> - delay duration in seconds (defaults to 3) 延时时间 (默认是 3 秒)
    -f <docker-compose-file> - specify which docker-compose file use (defaults to docker-compose-cli.yaml) 指定使用的 docker-compose 文件 (默认是 docker-compose-cli.yaml)
    -s <dbtype> - the database backend to use: goleveldb (default) or couchdb 所使用的数据库: goleveldb (默认) 或者 couchdb
    -l <language> - the chaincode language: golang (default) or node chaincode 编程语言: golang (默认) 或者 node
    -a - don't ask for confirmation before proceeding 在运行前无需确认

    Typically, one would first generate the required certificates and
    genesis block, then bring up the network. e.g.:

    一般情况下，可以先生成需要的证书文件和初始区块，随后启动整个网络。例如:

	byfn.sh -m generate -c mychannel
	byfn.sh -m up -c mychannel -s couchdb

If you choose not to supply a channel name, then the
script will use a default name of ``mychannel``.  The CLI timeout parameter
(specified with the -t flag) is an optional value; if you choose not to set
it, then the CLI will give up on query requests made after the default
setting of 10 seconds.

如果你没有指定通道名称，该脚本会使用默认的通道名称 ``mychannel``。客户端超时时间 (由 -t 选项指定)是一个可选项，如果你没有设置它，客户端会在默认的 10 秒超时时间后，放弃查询请求。

Generate Network Artifacts - 生成网络配置工件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ready to give it a go? Okay then! Execute the following command:

是否已经准备好开始了？好的，开始执行如下命令

.. code:: bash

  ./byfn.sh -m generate

You will see a brief description as to what will occur, along with a yes/no command line
prompt. Respond with a ``y`` or hit the return key to execute the described action.

你会看到一个简短的描述，以及一个 yes/no 的提示。输入 ``y`` 及回车键，将会执行所描述的相应动作。

.. code:: bash

  Generating certs and genesis block for with channel 'mychannel' and CLI timeout of '10'
  Continue? [Y/n] y
  proceeding ...
  /Users/xxx/dev/fabric-samples/bin/cryptogen

  ##########################################################
  ##### Generate certificates using cryptogen tool #########
  ##########################################################
  org1.example.com
  2017-06-12 21:01:37.334 EDT [bccsp] GetDefault -> WARN 001 Before using BCCSP, please call InitFactories(). Falling back to bootBCCSP.
  ...

  /Users/xxx/dev/fabric-samples/bin/configtxgen
  ##########################################################
  #########  Generating Orderer Genesis block ##############
  ##########################################################
  2017-06-12 21:01:37.558 EDT [common/configtx/tool] main -> INFO 001 Loading configuration
  2017-06-12 21:01:37.562 EDT [msp] getMspConfig -> INFO 002 intermediate certs folder not found at [/Users/xxx/dev/byfn/crypto-config/ordererOrganizations/example.com/msp/intermediatecerts]. Skipping.: [stat /Users/xxx/dev/byfn/crypto-config/ordererOrganizations/example.com/msp/intermediatecerts: no such file or directory]
  ...
  2017-06-12 21:01:37.588 EDT [common/configtx/tool] doOutputBlock -> INFO 00b Generating genesis block
  2017-06-12 21:01:37.590 EDT [common/configtx/tool] doOutputBlock -> INFO 00c Writing genesis block

  #################################################################
  ### Generating channel configuration transaction 'channel.tx' ###
  #################################################################
  2017-06-12 21:01:37.634 EDT [common/configtx/tool] main -> INFO 001 Loading configuration
  2017-06-12 21:01:37.644 EDT [common/configtx/tool] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
  2017-06-12 21:01:37.645 EDT [common/configtx/tool] doOutputChannelCreateTx -> INFO 003 Writing new channel tx

  #################################################################
  #######    Generating anchor peer update for Org1MSP   ##########
  #################################################################
  2017-06-12 21:01:37.674 EDT [common/configtx/tool] main -> INFO 001 Loading configuration
  2017-06-12 21:01:37.678 EDT [common/configtx/tool] doOutputAnchorPeersUpdate -> INFO 002 Generating anchor peer update
  2017-06-12 21:01:37.679 EDT [common/configtx/tool] doOutputAnchorPeersUpdate -> INFO 003 Writing anchor peer update

  #################################################################
  #######    Generating anchor peer update for Org2MSP   ##########
  #################################################################
  2017-06-12 21:01:37.700 EDT [common/configtx/tool] main -> INFO 001 Loading configuration
  2017-06-12 21:01:37.704 EDT [common/configtx/tool] doOutputAnchorPeersUpdate -> INFO 002 Generating anchor peer update
  2017-06-12 21:01:37.704 EDT [common/configtx/tool] doOutputAnchorPeersUpdate -> INFO 003 Writing anchor peer update

This first step generates all of the certificates and keys for our various
network entities, the ``genesis block`` used to bootstrap the ordering service,
and a collection of configuration transactions required to configure a
:ref:`Channel`.

在第一步中，生成了如下文件：我们多个网络实体的证书和密钥、用于启动排序服务的 ``初始区块(genesis block)`` 以及配置 :ref:`Channel` 所需的一系列配置交易。

Bring Up the Network - 启动整个网络
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, you can bring the network up with one of the following commands:

接下来，你可以使用如下命令，启动整个网络

.. code:: bash

  ./byfn.sh -m up

The above command will compile Golang chaincode images and spin up the corresponding
containers.  Go is the default chaincode language, however there is also support
for `Node.js <https://fabric-shim.github.io/>`__ chaincode.  If you'd like to run through this tutorial with node
chaincode, pass the following command instead:

上述命令会编译 Golang 链码镜像并启动相应的容器。Go 语言是默认的链码编程语言，同时还支持使用 `Node.js <https://fabric-shim.github.io/>`__ 编写链码。如果你希望基于 node 链码运行本教程，使用如下的命令：

.. code:: bash

  # we use the -l flag to specify the chaincode language
  # forgoing the -l flag will default to Golang

  ./byfn.sh -m up -l node

.. note:: View the `Hyperledger Fabric Shim <https://fabric-shim.github.io/ChaincodeStub.html>`__
          documentation for more info on the node.js chaincode shim APIs.
          
          阅读 `Hyperledger Fabric Shim <https://fabric-shim.github.io/ChaincodeStub.html>`__ 文档，获取更多关于 node.js 链码 API 的信息。

Once again, you will be prompted as to whether you wish to continue or abort.
Respond with a ``y`` or hit the return key:

和之前类似，会提示你是否继续或者终止。输入 ``y`` 以及回车键继续：

.. code:: bash

  Starting with channel 'mychannel' and CLI timeout of '10'
  Continue? [Y/n]
  proceeding ...
  Creating network "net_byfn" with the default driver
  Creating peer0.org1.example.com
  Creating peer1.org1.example.com
  Creating peer0.org2.example.com
  Creating orderer.example.com
  Creating peer1.org2.example.com
  Creating cli


   ____    _____      _      ____    _____
  / ___|  |_   _|    / \    |  _ \  |_   _|
  \___ \    | |     / _ \   | |_) |   | |
   ___) |   | |    / ___ \  |  _ <    | |
  |____/    |_|   /_/   \_\ |_| \_\   |_|

  Channel name : mychannel
  Creating channel...

The logs will continue from there. This will launch all of the containers, and
then drive a complete end-to-end application scenario. Upon successful
completion, it should report the following in your terminal window:

日志将从这里开始。随后会启动所有的容器，然后完成一个完整的端到端的应用场景。执行成功后，在终端窗口中会看到以下的内容：

.. code:: bash

    Query Result: 90
    2017-05-16 17:08:15.158 UTC [main] main -> INFO 008 Exiting.....
    ===================== Query on peer1.org2 on channel 'mychannel' is successful =====================

    ===================== All GOOD, BYFN execution completed =====================


     _____   _   _   ____
    | ____| | \ | | |  _ \
    |  _|   |  \| | | | | |
    | |___  | |\  | | |_| |
    |_____| |_| \_| |____/

You can scroll through these logs to see the various transactions. If you don't
get this result, then jump down to the :ref:`Troubleshoot` section and let's see
whether we can help you discover what went wrong.

你可以滚动浏览这些日志，查看其中的各种交易。如果你没有看到上述内容，请查看 :ref:`Troubleshoot` 章节，看看我们能否帮助你发现问题所在。

Bring Down the Network - 关闭整个网络
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Finally, let's bring it all down so we can explore the network setup one step
at a time. The following will kill your containers, remove the crypto material
and four artifacts, and delete the chaincode images from your Docker Registry:

最后，让我们关闭整个网络，以便我们可以逐步来学习网络的配置。接下来，将关闭你的容器，删除加密文件和四个配置工件，并从你的 Docker 镜像库中删除链码镜像：

.. code:: bash

  ./byfn.sh -m down

Once again, you will be prompted to continue, respond with a ``y`` or hit the return key:

再一次的，会提示你是否继续，输入 ``y`` 和回车键继续：

.. code:: bash

  Stopping with channel 'mychannel' and CLI timeout of '10'
  Continue? [Y/n] y
  proceeding ...
  WARNING: The CHANNEL_NAME variable is not set. Defaulting to a blank string.
  WARNING: The TIMEOUT variable is not set. Defaulting to a blank string.
  Removing network net_byfn
  468aaa6201ed
  ...
  Untagged: dev-peer1.org2.example.com-mycc-1.0:latest
  Deleted: sha256:ed3230614e64e1c83e510c0c282e982d2b06d148b1c498bbdcc429e2b2531e91
  ...

If you'd like to learn more about the underlying tooling and bootstrap mechanics,
continue reading.  In these next sections we'll walk through the various steps
and requirements to build a fully-functional Hyperledger Fabric network.

如果你打算了解更多底层工具和引导机制的相关信息，请继续阅读下面的章节。在接下来的部分中，我们将介绍构建完整功能 Hyperledger Fabric 网络的各个步骤和相关要求。

.. note:: The manual steps outlined below assume that the ``CORE_LOGGING_LEVEL`` in
          the ``cli`` container is set to ``DEBUG``. You can set this by modifying
          the ``docker-compose-cli.yaml`` file in the ``first-network`` directory.
          e.g.

          随后的手动步骤假设 ``cli`` 容器中的 ``CORE_LOGGING_LEVEL`` 被设置为 ``DEBUG``。
          你可以通过修改 ``first-network`` 目录下的 ``docker-compose-cli.yaml``
          文件来设置这个参数，如下所示：

          .. code::

            cli:
              container_name: cli
              image: hyperledger/fabric-tools:$IMAGE_TAG
              tty: true
              stdin_open: true
              environment:
                - GOPATH=/opt/gopath
                - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
                - CORE_LOGGING_LEVEL=DEBUG
                #- CORE_LOGGING_LEVEL=INFO

Crypto Generator - 加密文件的生成
--------------------------------------------------------------------------------

We will use the ``cryptogen`` tool to generate the cryptographic material
(x509 certs and signing keys) for our various network entities.  These certificates are
representative of identities, and they allow for sign/verify authentication to
take place as our entities communicate and transact.

我们使用 ``cryptogen`` 工具来为各种网络实体生成加密文件 (x509 证书和密钥)。这些证书代表了身份的标示，网络实体在进行通信和交易的时候，会利用这些证书来进行签名和校验身份。

How does it work? - 它是如何工作的？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cryptogen consumes a file - ``crypto-config.yaml`` - that contains the network
topology and allows us to generate a set of certificates and keys for both the
Organizations and the components that belong to those Organizations.  Each
Organization is provisioned a unique root certificate (``ca-cert``) that binds
specific components (peers and orderers) to that Org.  By assigning each
Organization a unique CA certificate, we are mimicking a typical network where
a participating :ref:`Member` would use its own Certificate Authority.
Transactions and communications within Hyperledger Fabric are signed by an
entity's private key (``keystore``), and then verified by means of a public
key (``signcerts``).

Cryptogen 使用包含了网络拓扑结构的 ``crypto-config.yaml`` 配置文件，可以为机构和属于这些机构的组件生成一组证书和密钥。每个机构都配备了一个唯一的根证书（ ``ca-cert`` ），并将指定的组件（对等节点和排序服务节点）绑定到该机构上。通过为每个机构分配一个唯一的 CA 证书，我们正在模拟一个典型的网络，其中每个指定的 :ref:`Member` 将使用自己的 CA 证书颁发机构。Hyperledger Fabric 中的交易和通信，都将由实体的密钥（ ``keystore`` ）进行签名，然后通过公钥（ ``signcerts`` ）进行校验。

You will notice a ``count`` variable within this file.  We use this to specify
the number of peers per Organization; in our case there are two peers per Org.
We won't delve into the minutiae of `x.509 certificates and public key
infrastructure <https://en.wikipedia.org/wiki/Public_key_infrastructure>`__
right now. If you're interested, you can peruse these topics on your own time.

你会注意到该文件中的 ``count`` 变量。我们用这个变量来指定每个机构中对等节点的数量。在我们的示例中，每个机构有两个对等节点。我们现在不会深入研究 `x.509 证书和公钥基础设施 <https://en.wikipedia.org/wiki/Public_key_infrastructure>`__ 的细节。如果你有兴趣，可以自己找时间来仔细阅读这些资料。

Before running the tool, let's take a quick look at a snippet from the
``crypto-config.yaml``. Pay specific attention to the "Name", "Domain"
and "Specs" parameters under the ``OrdererOrgs`` header:

在运行该工具之前，让我们快速浏览一下 ``crypto-config.yaml`` 中的部分配置。请特别注意 ``OrdererOrgs`` 标题下的 “Name” ， “Domain” 和 “Specs” 参数：

.. code:: bash

  OrdererOrgs:
  #---------------------------------------------------------
  # Orderer
  # --------------------------------------------------------
  - Name: Orderer
    Domain: example.com
    CA:
        Country: US
        Province: California
        Locality: San Francisco
    #   OrganizationalUnit: Hyperledger Fabric
    #   StreetAddress: address for org # default nil
    #   PostalCode: postalCode for org # default nil
    # ------------------------------------------------------
    # "Specs" - See PeerOrgs below for complete description
  # -----------------------------------------------------
    Specs:
      - Hostname: orderer
  # -------------------------------------------------------
  # "PeerOrgs" - Definition of organizations managing peer nodes
   # ------------------------------------------------------
  PeerOrgs:
  # -----------------------------------------------------
  # Org1
  # ----------------------------------------------------
  - Name: Org1
    Domain: org1.example.com
    EnableNodeOUs: true

The naming convention for a network entity is as follows -
"{{.Hostname}}.{{.Domain}}".  So using our ordering node as a
reference point, we are left with an ordering node named -
``orderer.example.com`` that is tied to an MSP ID of ``Orderer``.  This file
contains extensive documentation on the definitions and syntax.  You can also
refer to the :doc:`msp` documentation for a deeper dive on MSP.

网络实体的命名约定如下 - "{{.Hostname}}.{{.Domain}}" 。因此，以我们的排序服务节点为例，我们设置了一个名为 - ``orderer.example.com`` 的排序服务节点，该排序服务节点绑定到名为 ``Orderer`` 的 MSP ID 上。本文档还包含了有关定义和语法的大量描述，你也可以参阅 :doc:`msp` 文档，以深入了解 MSP。

After we run the ``cryptogen`` tool, the generated certificates and keys will be
saved to a folder titled ``crypto-config``.

运行 ``cryptogen`` 工具后，生成的证书和密钥将被保存到 ``crypto-config`` 文件夹中。

Configuration Transaction Generator - 配置交易的生成
--------------------------------------------------------------------------------

The ``configtxgen tool`` is used to create four configuration artifacts:

``configtxgen`` 工具用于创建以下四个配置工件：

  * orderer ``genesis block``,
  * channel ``configuration transaction``,
  * and two ``anchor peer transactions`` - one for each Peer Org.

  * 排序服务节点的 ``初始区块(genesis block)``
  * 通道的 ``配置交易(configuration transaction)``
  * 以及两笔 ``锚节点交易(anchor peer transactions)`` - 每笔对应一个机构

Please see :doc:`configtxgen` for a complete description of this tool's functionality.

请参阅 :doc:`configtxgen` 获取本工具功能的完整描述。

The orderer block is the :ref:`Genesis-Block` for the ordering service, and the
channel configuration transaction file is broadcast to the orderer at :ref:`Channel` creation
time.  The anchor peer transactions, as the name might suggest, specify each
Org's :ref:`Anchor-Peer` on this channel.

排序服务节点区块是排序服务的 :ref:`Genesis-Block` ，通道配置交易在创建 :ref:`Channel` 时广播给排序服务节点。 锚节点交易（正如名字所描述的一样）指定了通道中每个机构的 :ref:`Anchor-Peer` 。

How does it work? - 它是如何工作的？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Configtxgen consumes a file - ``configtx.yaml`` - that contains the definitions
for the sample network. There are three members - one Orderer Org (``OrdererOrg``)
and two Peer Orgs (``Org1`` & ``Org2``) each managing and maintaining two peer nodes.
This file also specifies a consortium - ``SampleConsortium`` - consisting of our
two Peer Orgs.  Pay specific attention to the "Profiles" section at the top of
this file.  You will notice that we have two unique headers. One for the orderer genesis
block - ``TwoOrgsOrdererGenesis`` - and one for our channel - ``TwoOrgsChannel``.

Configtxgen 使用包含了示例网络定义的配置文件 ``configtx.yaml``。该文件中定义了示例网络的三个成员 - 一个排序服务节点机构 (``OrdererOrg``) 和两个对等节点机构 (``Org1`` & ``Org2``)，其中每个对等节点机构管理和维护两个对等节点。该文件还指定了由两个对等节点机构组成的联盟 ``SampleConsortium``。请特别注意文件顶部的 "Profiles" 部分。你会注意到有两个特别的部分，一个是排序节点初始区块 ``TwoOrgsOrdererGenesis``，另一个是通道配置 ``TwoOrgsChannel``。

These headers are important, as we will pass them in as arguments when we create
our artifacts.

上述两个配置参数很重要，在创建配置工件时，将会把它们作为参数传入。

.. note:: Notice that our ``SampleConsortium`` is defined in
          the system-level profile and then referenced by
          our channel-level profile.  Channels exist within
          the purview of a consortium, and all consortia
          must be defined in the scope of the network at
          large.
          
          请注意，我们的 ``SampleConsortium`` 是在系统级配置文件中定义的，然后在通道级配置文件中被引用。通道存在于一个联盟的范围内，所有联盟都必须在整个网络范围内进行界定。

This file also contains two additional specifications that are worth
noting. Firstly, we specify the anchor peers for each Peer Org
(``peer0.org1.example.com`` & ``peer0.org2.example.com``).  Secondly, we point to
the location of the MSP directory for each member, in turn allowing us to store the
root certificates for each Org in the orderer genesis block.  This is a critical
concept. Now any network entity communicating with the ordering service can have
its digital signature verified.

该文件还有两处值得引起注意。第一，我们为每个对等节点机构指定了锚节点(anchor peer)。第二，我们为每个成员指定了 MSP 目录的位置，这使得我们可以在排序服务节点初始区块中保存每个机构的根证书。这是一个非常重要的概念，这样任何与排序服务通信的网络实体都可以校验数字签名了。

Run the tools - 运行工具
--------------------------------------------------------------------------------

You can manually generate the certificates/keys and the various configuration
artifacts using the ``configtxgen`` and ``cryptogen`` commands. Alternately,
you could try to adapt the byfn.sh script to accomplish your objectives.

你可以使用 ``configtxgen`` 和 ``cryptogen`` 命令手动生成证书、密钥以及各种配置工件。此外，你还可以尝试修改 byfn.sh 脚本来达到同样的目的。

Manually generate the artifacts - 手动生成
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can refer to the ``generateCerts`` function in the byfn.sh script for the
commands necessary to generate the certificates that will be used for your
network configuration as defined in the ``crypto-config.yaml`` file. However,
for the sake of convenience, we will also provide a reference here.

你可以参考 byfn.sh 脚本中 ``generateCerts`` 函数中的命令，生成 ``crypto-config.yaml`` 文件中所使用到的证书文件。当然，为方便起见，我们这里也提供了一个参考方法。

First let's run the ``cryptogen`` tool.  Our binary is in the ``bin``
directory, so we need to provide the relative path to where the tool resides.

首先，运行 ``cryptogen`` 工具。我们的可执行文件在 ``bin`` 目录下，所以我们需要提供指向工具的相对路径。

.. code:: bash

    ../bin/cryptogen generate --config=./crypto-config.yaml

You should see the following in your terminal:

你会在终端中看到如下信息：

.. code:: bash

  org1.example.com
  org2.example.com

The certs and keys (i.e. the MSP material) will be output into a directory - ``crypto-config`` -
at the root of the ``first-network`` directory.

证书和密钥（例如 MSP 材料）等文件生成后，位于 ``first-network`` 目录的 ``crypto-config`` 子目录下。

Next, we need to tell the ``configtxgen`` tool where to look for the
``configtx.yaml`` file that it needs to ingest.  We will tell it look in our
present working directory:

随后，我们需要告诉 ``configtxgen`` 工具，在哪个目录下查找它所需要的配置文件 ``configtx.yaml`` 文件。我们会告诉它在当前的目录中查找：

.. code:: bash

    export FABRIC_CFG_PATH=$PWD

Then, we'll invoke the ``configtxgen`` tool to create the orderer genesis block:

接着，我们将调用 ``configtxgen`` 工具来创建排序节点初始区块 (the orderer genesis block)：

.. code:: bash

    ../bin/configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block

You should see an output similar to the following in your terminal:

你会在终端中看到类似如下的信息：

.. code:: bash

  2017-10-26 19:21:56.301 EDT [common/tools/configtxgen] main -> INFO 001 Loading configuration
  2017-10-26 19:21:56.309 EDT [common/tools/configtxgen] doOutputBlock -> INFO 002 Generating genesis block
  2017-10-26 19:21:56.309 EDT [common/tools/configtxgen] doOutputBlock -> INFO 003 Writing genesis block

.. note:: The orderer genesis block and the subsequent artifacts we are about to create
          will be output into the ``channel-artifacts`` directory at the root of this
          project.

          排序服务节点初始区块以及随后我们生成的配置工件，都保存在本项目根目录下的 ``channel-artifacts`` 子目录下

.. _createchanneltx:

Create a Channel Configuration Transaction - 创建通道配置交易
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, we need to create the channel transaction artifact. Be sure to replace ``$CHANNEL_NAME`` or
set ``CHANNEL_NAME`` as an environment variable that can be used throughout these instructions:

下一步，我们需要创建通道配置交易。在如下的说明中，请确认已经替换命令中的 ``$CHANNEL_NAME`` 为实际的通道名称，或者将环境变量 ``CHANNEL_NAME`` 设置为实际的通道名称 ：

.. code:: bash

    # The channel.tx artifact contains the definitions for our sample channel

    # channel.tx 文件中包含了我们示例通道的配置信息

    export CHANNEL_NAME=mychannel  && ../bin/configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME

You should see an ouput similar to the following in your terminal:

你会在终端中看到类似如下的信息：

.. code:: bash

  2017-10-26 19:24:05.324 EDT [common/tools/configtxgen] main -> INFO 001 Loading configuration
  2017-10-26 19:24:05.329 EDT [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
  2017-10-26 19:24:05.329 EDT [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 003 Writing new channel tx

Next, we will define the anchor peer for Org1 on the channel that we are
constructing. Again, be sure to replace ``$CHANNEL_NAME`` or set the environment variable
for the following commands.  The terminal output will mimic that of the channel transaction artifact:

接着，我们会指定机构 Org1 的锚节点。再一次注意，请确保替换命令中的 ``$CHANNEL_NAME`` 为实际的通道名称，或者将环境变量 ``CHANNEL_NAME`` 设置为实际的通道名称。 终端中会显示出通道交易工件：

.. code:: bash

    ../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP

Now, we will define the anchor peer for Org2 on the same channel:

现在，我们接着指定机构 Org2 的锚节点：

.. code:: bash

    ../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org2MSP

Start the network - 启动网络
--------------------------------------------------------------------------------

We will leverage a script to spin up our network. The
docker-compose file references the images that we have previously downloaded,
and bootstraps the orderer with our previously generated ``genesis.block``.

我们会使用一个脚本来启动我们的网络。docker-compose 文件引用了之前我们已经下载好的镜像文件，启动了一个排序服务节点，该排序服务节点使用的是前文生成的 ``genesis.block`` 。

We want to go through the commands manually in order to expose the
syntax and functionality of each call.

我们希望手动输入一遍所有命令，以便了解每个调用的语法和功能。

First let's start your network:

首先，让我们启动网络：

.. code:: bash

    docker-compose -f docker-compose-cli.yaml up -d

If you want to see the realtime logs for your network, then do not supply the ``-d`` flag.
If you let the logs stream, then you will need to open a second terminal to execute the CLI calls.

如果你希望看到网络的实时输出，请不要添加 ``-d`` 参数。此时，日志将会持续打印，你需要打开另一个终端来执行 CLI 调用。

The CLI container will stick around idle for 1000 seconds. If it's gone when you need it you can restart it with a simple command:

CLI 容器会持续闲置等待 1000 秒。如果该容器被关闭，可以使用如下命令重启：

.. code:: bash

    docker start cli

.. _peerenvvars:

Environment variables - 环境变量
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For the following CLI commands against ``peer0.org1.example.com`` to work, we need
to preface our commands with the four environment variables given below.  These
variables for ``peer0.org1.example.com`` are baked into the CLI container,
therefore we can operate without passing them.  **HOWEVER**, if you want to send
calls to other peers or the orderer, then you will need to provide these
values accordingly.  Inspect the ``docker-compose-base.yaml`` for the specific
paths:

随后的 CLI 命令是针对 ``peer0.org1.example.com`` 的，在执行命令前，我们需要先设置 4 个环境变量。这些针对 ``peer0.org1.example.com`` 的环境变量在 CLI 容器已经被预设好，因此我们此时也可以不设置这些环境变量。**但是**，如果需要调用其他的对等节点或者是排序服务节点，必须设置好相应的环境变量。查看 ``docker-compose-base.yaml`` 文件可以看到具体的路径：

.. code:: bash

    # Environment variables for PEER0

    # 针对 PEER0 的环境变量

    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

.. _createandjoin:

Create & Join Channel - 创建和加入通道
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Recall that we created the channel configuration transaction using the
``configtxgen`` tool in the :ref:`createchanneltx` section, above. You can
repeat that process to create additional channel configuration transactions,
using the same or different profiles in the ``configtx.yaml`` that you pass
to the ``configtxgen`` tool. Then you can repeat the process defined in this
section to establish those other channels in your network.

回忆下，我们在 :ref:`createchanneltx` 一节中使用 ``configtxgen`` 工具生成了通道配置交易。你可以重复上述过程，在使用 ``configtxgen`` 工具时使用和之前 ``configtx.yaml`` 文件中相同或者不同的配置，生成新的通道配置交易。随后，你可以重复本节所提的过程，在网络中创建其他的通道。

We will enter the CLI container using the ``docker exec`` command:

我们使用 ``docker exec`` 命令进入 CLI 容器内：

.. code:: bash

        docker exec -it cli bash

If successful you should see the following:

如果成功，你会看到如下信息：

.. code:: bash

        root@0d78bb69300d:/opt/gopath/src/github.com/hyperledger/fabric/peer#

Next, we are going to pass in the generated channel configuration transaction
artifact that we created in the :ref:`createchanneltx` section (we called
it ``channel.tx``) to the orderer as part of the create channel request.

随后，我们会将 :ref:`createchanneltx` 一节中生成的通道配置交易工件 ``channel.tx`` 传给排序服务节点，用于创建通道。

We specify our channel name with the ``-c`` flag and our channel configuration
transaction with the ``-f`` flag. In this case it is ``channel.tx``, however
you can mount your own configuration transaction with a different name.  Once again
we will set the ``CHANNEL_NAME`` environment variable within our CLI container so that
we don't have to explicitly pass this argument:

我们可以使用 ``-c`` 参数指定我们的通道名称，以及使用 ``-f`` 参数指定通道配置交易工件。在本示例中，通道配置交易工件是 ``channel.tx``，当然，你可以使用自己生成的任意名字的通道配置交易工件。再一次提醒，我们需要在 CLI 容器中设置环境变量 ``CHANNEL_NAME``，这样就不必在每次输入命令时再显式的输入该参数：

.. code:: bash

        export CHANNEL_NAME=mychannel

        # the channel.tx file is mounted in the channel-artifacts directory within your CLI container
        # as a result, we pass the full path for the file
        # we also pass the path for the orderer ca-cert in order to verify the TLS handshake
        # be sure to export or replace the $CHANNEL_NAME variable appropriately

        # channel.tx 文件被挂载在 CLI 容器的 channel-artifacts 目录下
        # 因此，我们需要传入该文件的绝对路径
        # 同时，我们还传入了用于校验 TLS 握手的排序服务节点的 ca 证书
        # 请确保设置了环境变量 $CHANNEL_NAME，或者将命令中的 $CHANNEL_NAME 修改为实际值

        peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

.. note:: Notice the ``-- cafile`` that we pass as part of this command.  It is
          the local path to the orderer's root cert, allowing us to verify the
          TLS handshake.

          请注意命令中的 ``-- cafile`` 参数，它指向了排序服务节点的根证书，用于完成 TLS 握手的校验。


This command returns a genesis block - ``<channel-ID.block>`` - which we will use to join the channel.
It contains the configuration information specified in ``channel.tx``  If you have not
made any modifications to the default channel name, then the command will return you a
proto titled ``mychannel.block``.

该命令返回一个初始区块 ``<channel-ID.block>``，我们使用它来加入通道。它包含了 ``channel.tx`` 中指定的配置信息。如果你没有修改默认的通道名称，该命令会返回名为 ``mychannel.block`` 的文件。

.. note:: You will remain in the CLI container for the remainder of
          these manual commands. You must also remember to preface all commands
          with the corresponding environment variables when targeting a peer other than
          ``peer0.org1.example.com``.

          随后手动执行的命令，都需要在 CLI 容器内执行。同时需要注意，如果希望连接除 ``peer0.org1.example.com`` 外的其他对等节点，需要将环境变量设置为相应的值。


Now let's join ``peer0.org1.example.com`` to the channel.

现在，让我们把 ``peer0.org1.example.com`` 加入到通道中。

.. code:: bash

        # By default, this joins ``peer0.org1.example.com`` only
        # the <channel-ID.block> was returned by the previous command
        # if you have not modified the channel name, you will join with mychannel.block
        # if you have created a different channel name, then pass in the appropriately named block

        # 下述命令默认将 ``peer0.org1.example.com`` 加入 mychannel.block 对应的通道中
        # 如果你修改了通道名称，请传入相应的 .block 文件名

         peer channel join -b mychannel.block

You can make other peers join the channel as necessary by making appropriate
changes in the four environment variables we used in the :ref:`peerenvvars`
section, above.

通过修改 :ref:`peerenvvars` 节中的环境变量为相应的值，你可以将所需要的对等节点都加入到通道中。

Rather than join every peer, we will simply join ``peer0.org2.example.com`` so that
we can properly update the anchor peer definitions in our channel.  Since we are
overriding the default environment variables baked into the CLI container, this full
command will be the following:

我们并不添加所有的对等节点到该通道中，我们只添加 ``peer0.org2.example.com`` ，这样我们就可以更新通道的锚节点信息。我们需要覆盖 CLI 容器中的默认环境变量，完整的命令如下：

.. code:: bash

  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:7051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel join -b mychannel.block

Alternatively, you could choose to set these environment variables individually
rather than passing in the entire string.  Once they've been set, you simply need
to issue the ``peer channel join`` command again and the CLI container will act
on behalf of ``peer0.org2.example.com``.

另一种方式，你还可以选择单独设置这些环境变量，而不必在执行命令时传入整个设置串。一旦设置好这些环境变量，你需要执行 ``peer channel join`` 命令，此时 CLI 容器会连接 ``peer0.org2.example.com`` 进行相应操作。

Update the anchor peers - 更新锚节点
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following commands are channel updates and they will propagate to the definition
of the channel.  In essence, we adding additional configuration information on top
of the channel's genesis block.  Note that we are not modifying the genesis block, but
simply adding deltas into the chain that will define the anchor peers.

如下的命令用于更新通道，会修改通道的定义。总体而言，我们会在通道的初始区块基础上，增加额外的配置信息。值得注意的是，我们并未修改初始区块，而是在链上增加增量信息，用于定义锚节点。

Update the channel definition to define the anchor peer for Org1 as ``peer0.org1.example.com``:

更新通道配置信息，将机构 Org1 的锚节点设置为 ``peer0.org1.example.com``：

.. code:: bash

  peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

Now update the channel definition to define the anchor peer for Org2 as ``peer0.org2.example.com``.
Identically to the ``peer channel join`` command for the Org2 peer, we will need to
preface this call with the appropriate environment variables.

现在，更新通道配置信息，将机构 Org2 的锚节点设置为 ``peer0.org2.example.com``。和结构 Org2 节点的 ``peer channel join`` 命令类似，我们需要在此命令前增加相应的环境变量。

.. code:: bash

  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp CORE_PEER_ADDRESS=peer0.org2.example.com:7051 CORE_PEER_LOCALMSPID="Org2MSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

Install & Instantiate Chaincode - 安装和实例化链码
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: We will utilize a simple existing chaincode. To learn how to write
          your own chaincode, see the :doc:`chaincode4ade` tutorial.

          我们会使用一个简单的既有链码。如果想学习如何编写链码，请参考 :doc:`chaincode4ade` 教程。

Applications interact with the blockchain ledger through ``chaincode``.  As
such we need to install the chaincode on every peer that will execute and
endorse our transactions, and then instantiate the chaincode on the channel.

应用通过 ``链码(chaincode)`` 和区块链账本进行交互。因此，我们需要在每个执行交易或为交易背书的对等节点上安装链码，随后在通道中实例化该链码。

First, install the sample Go or Node.js chaincode onto one of the four peer nodes.  These commands
place the specified source code flavor onto our peer's filesystem.

首先，在 4 个对等节点中的 1 个节点上安装示例的 Go 或者 Node.js 链码。这些命令指定了对等节点文件系统上的源码路径。

.. note:: You can only install one version of the source code per chaincode name
          and version.  The source code exists on the peer's file system in the
          context of chaincode name and version; it is language agnostic.  Similarly
          the instantiated chaincode container will be reflective of whichever
          language has been installed on the peer.

          对于每个链码的每个版本，只允许安装相同的源代码。源文件基于链码名称和版本号保存在对等节点文件系统上，编程语言此时是未知的。类似的，实例化后的链码容器会反映出是哪种编程语言被安装在该对等节点上。

**Golang**

.. code:: bash

    # this installs the Go chaincode

    # 安装 Go 链码
    peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_example02/go/

**Node.js**

.. code:: bash

    # this installs the Node.js chaincode
    # make note of the -l flag; we use this to specify the language

    # 安装 Node.js 链码
    # 注意 -l 参数，我们使用它来指定编程语言
    peer chaincode install -n mycc -v 1.0 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

Next, instantiate the chaincode on the channel. This will initialize the
chaincode on the channel, set the endorsement policy for the chaincode, and
launch a chaincode container for the targeted peer.  Take note of the ``-P``
argument. This is our policy where we specify the required level of endorsement
for a transaction against this chaincode to be validated.

下一步，实例化通道中的链码，即初始化通道中的链码、设置链码的背书策略以及为每个目标对等节点启动一个链码容器。注意 ``-P`` 参数，这是我们指定的背书策略级别，用于校验链码的交易。

In the command below you’ll notice that we specify our policy as
``-P "OR ('Org0MSP.peer','Org1MSP.peer')"``. This means that we need
“endorsement” from a peer belonging to Org1 **OR** Org2 (i.e. only one endorsement).
If we changed the syntax to ``AND`` then we would need two endorsements.

在下面的命令中，你可以看到我们指定的策略为 ``-P "OR ('Org0MSP.peer','Org1MSP.peer')"``。这表示我们需要机构 Org1 **或者** 机构 Org2 中一个节点的 "背书"（即只需要一个背书）。如果我们将策略改为 ``AND``，则我们需要两个背书。

**Golang**

.. code:: bash

    # be sure to replace the $CHANNEL_NAME environment variable if you have not exported it
    # if you did not install your chaincode with a name of mycc, then modify that argument as well

    # 请确保替换了 $CHANNEL_NAME 环境变量，或已经提前设置好
    # 如果你安装链码时指定的名称不是 mycc，请修改如下参数为对应值

    peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('Org1MSP.peer','Org2MSP.peer')"

**Node.js**

.. note::  The instantiation of the Node.js chaincode will take roughly a minute.
           The command is not hanging; rather it is installing the fabric-shim
           layer as the image is being compiled.

           Node.js 链码的实例化会大概耗时 1 分钟。此时命令并没有被挂起，而是在安装 fabric-shim 层以及编译镜像。

.. code:: bash

    # be sure to replace the $CHANNEL_NAME environment variable if you have not exported it
    # if you did not install your chaincode with a name of mycc, then modify that argument as well
    # notice that we must pass the -l flag after the chaincode name to identify the language

    # 请确保替换了 $CHANNEL_NAME 环境变量，或已经提前设置好
    # 如果你安装链码时指定的名字不是 mycc，请修改如下参数为对应值
    # 注意必须在链码名称后传入 -l 参数来指定链码编程语言

    peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc -l node -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('Org1MSP.peer','Org2MSP.peer')"

See the `endorsement
policies <http://hyperledger-fabric.readthedocs.io/en/latest/endorsement-policies.html>`__
documentation for more details on policy implementation.

如果想了解背书策略实现的具体细节，请参考 `endorsement
policies <http://hyperledger-fabric.readthedocs.io/en/latest/endorsement-policies.html>`__ 文档。

If you want additional peers to interact with ledger, then you will need to join
them to the channel, and install the same name, version and language of the
chaincode source onto the appropriate peer's filesystem.  A chaincode container
will be launched for each peer as soon as they try to interact with that specific
chaincode.  Again, be cognizant of the fact that the Node.js images will be slower
to compile.

如果你想让其他节点和账本进行交互，你需要将它们添加到通道中，然后在相应的对等节点文件系统上，安装同名称、同版本以及同编程语言的链码源文件。对于每个对等节点，当它们试图和指定链码进行交互时，会立即启动该链码对应的容器。再一次提醒，Node.js 镜像的编译速度会比较慢。

Once the chaincode has been instantiated on the channel, we can forgo the ``l``
flag.  We need only pass in the channel identifier and name of the chaincode.

一旦链码在通道中实例化后，我们可以不再添加 ``l`` 参数。我们只需要添加通道标识以及链码名称即可。

Query - 查询
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's query for the value of ``a`` to make sure the chaincode was properly
instantiated and the state DB was populated. The syntax for query is as follows:

让我们通过查询 ``a`` 对应的值，来确保链码已经被正确的实例化，以及状态数据库 (state DB) 已经被设置好。查询的语法如下：

.. code:: bash

  # be sure to set the -C and -n flags appropriately

  peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

Invoke - 调用
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now let's move ``10`` from ``a`` to ``b``.  This transaction will cut a new block and
update the state DB. The syntax for invoke is as follows:

现在让我们从 ``a`` 转移 ``10`` 到 ``b``。这个交易会生成一个新区块并更新状态数据库。执行的语法如下：

.. code:: bash

    # be sure to set the -C and -n flags appropriately

    peer chaincode invoke -o orderer.example.com:7050  --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem  -C $CHANNEL_NAME -n mycc -c '{"Args":["invoke","a","b","10"]}'

Query - 查询
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's confirm that our previous invocation executed properly. We initialized the
key ``a`` with a value of ``100`` and just removed ``10`` with our previous
invocation. Therefore, a query against ``a`` should reveal ``90``. The syntax
for query is as follows.

下面确认我们之前的操作被正确执行了。我们将 ``a`` 的值初始化为 ``100``，然后在上个操作中转移了 ``10``。因此，再次查询 ``a`` 的值应该为 ``90``。查询的语法如下：

.. code:: bash

  # be sure to set the -C and -n flags appropriately

  peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

We should see the following:

我们应该会看到如下信息：

.. code:: bash

   Query Result: 90

Feel free to start over and manipulate the key value pairs and subsequent
invocations.

请任意的从头执行本节内容、修改键值以及后续调用。

.. _behind-scenes:

What's happening behind the scenes? - 背后发生了什么？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: These steps describe the scenario in which
          ``script.sh`` is run by './byfn.sh up'.  Clean your network
          with ``./byfn.sh down`` and ensure
          this command is active.  Then use the same
          docker-compose prompt to launch your network again

          后续的步骤描述了由 './byfn.sh up' 启动 ``script.sh`` 后的场景。使用 ``./byfn.sh down`` 清空原先的网络，确保该命令执行成功。随后使用相同的 docker-compose 命令再次启动网络。

-  A script - ``script.sh`` - is baked inside the CLI container. The
   script drives the ``createChannel`` command against the supplied channel name
   and uses the channel.tx file for channel configuration.

-  脚本 - ``script.sh`` - 包含在 CLI 容器中。该脚本使用提供的通道名调用 ``createChannel`` 命令，并使用 channel.tx 文件作为通道配置文件。

-  The output of ``createChannel`` is a genesis block -
   ``<your_channel_name>.block`` - which gets stored on the peers' file systems and contains
   the channel configuration specified from channel.tx.

-  ``createChannel`` 的输出是一个初始区块 ``<your_channel_name>.block``，该文件保存在对等节点的文件系统下，其中包含了 channel.tx 中指定的通道配置信息。

-  The ``joinChannel`` command is exercised for all four peers, which takes as
   input the previously generated genesis block.  This command instructs the
   peers to join ``<your_channel_name>`` and create a chain starting with ``<your_channel_name>.block``.

-  ``joinChannel`` 命令以上一步生成的初始区块作为输入，在四个对等节点上都进行了执行。该命令将对等节点加入 ``<your_channel_name>`` 通道，同时创建了一个基于 ``<your_channel_name>.block`` 的链。

-  Now we have a channel consisting of four peers, and two
   organizations.  This is our ``TwoOrgsChannel`` profile.

- 现在我们拥有一个包含了 2 个结构、4 个对等节点的通道。这就是我们的 ``TwoOrgsChannel`` 配置。

-  ``peer0.org1.example.com`` and ``peer1.org1.example.com`` belong to Org1;
   ``peer0.org2.example.com`` and ``peer1.org2.example.com`` belong to Org2

-  ``peer0.org1.example.com`` 和 ``peer1.org1.example.com`` 属于机构 Org1;
   ``peer0.org2.example.com`` 和 ``peer1.org2.example.com`` 属于机构 Org2

-  These relationships are defined through the ``crypto-config.yaml`` and
   the MSP path is specified in our docker compose.

-  上述关系是在 ``crypto-config.yaml`` 中进行定义，MSP 路径则是在我们的 docker-compose 配置文件中指定。

-  The anchor peers for Org1MSP (``peer0.org1.example.com``) and
   Org2MSP (``peer0.org2.example.com``) are then updated.  We do this by passing
   the ``Org1MSPanchors.tx`` and ``Org2MSPanchors.tx`` artifacts to the ordering
   service along with the name of our channel.

-  随后更新了 Org1MSP 的锚节点 (``peer0.org1.example.com``) 以及 Org2MSP 的锚节点 (``peer0.org2.example.com``) 。我们通过将 ``Org1MSPanchors.tx`` 和 ``Org2MSPanchors.tx`` 文件提交给排序服务（其中需要指定我们的通道名称）实现了上述更新。

-  A chaincode - **chaincode_example02** - is installed on ``peer0.org1.example.com`` and
   ``peer0.org2.example.com``

-  一个链码 - **chaincode_example02** - 被安装在 ``peer0.org1.example.com`` 和
   ``peer0.org2.example.com`` 上

-  The chaincode is then "instantiated" on ``peer0.org2.example.com``. Instantiation
   adds the chaincode to the channel, starts the container for the target peer,
   and initializes the key value pairs associated with the chaincode.  The initial
   values for this example are ["a","100" "b","200"]. This "instantiation" results
   in a container by the name of ``dev-peer0.org2.example.com-mycc-1.0`` starting.

-  该链码随后在 ``peer0.org2.example.com`` 上进行了 "实例化"。实例化的过程中，将该链码添加到通道中、在目标对等节点上启动容器以及初始化了链码中的 key-value 对。本例的初始化值是 ["a","100" "b","200"]。 "初始化" 过程完成后，一个名为 ``dev-peer0.org2.example.com-mycc-1.0`` 的容器被启动。

-  The instantiation also passes in an argument for the endorsement
   policy. The policy is defined as
   ``-P "OR    ('Org1MSP.peer','Org2MSP.peer')"``, meaning that any
   transaction must be endorsed by a peer tied to Org1 or Org2.

-  实例化过程同时还传入一个参数，作为背书策略。本例中的背书策略是 ``-P "OR    ('Org1MSP.peer','Org2MSP.peer')"``，表示每一次交易都必须由机构 Org1 或者机构 Org2 的对等节点进行背书。

-  A query against the value of "a" is issued to ``peer0.org1.example.com``. The
   chaincode was previously installed on ``peer0.org1.example.com``, so this will start
   a container for Org1 peer0 by the name of ``dev-peer0.org1.example.com-mycc-1.0``. The result
   of the query is also returned. No write operations have occurred, so
   a query against "a" will still return a value of "100".

-  向 ``peer0.org1.example.com`` 发起了一个查找 "a" 对应值的查询。链码已经在 ``peer0.org1.example.com`` 上安装完毕，此时会启动一个名为 ``dev-peer0.org1.example.com-mycc-1.0`` 的容器（针对机构 Org1 以及对等节点 peer0）。查询的结果随后被返回。此时还没有写入操作发生，所以查询 "a" 值的返回结果为 "100"。

-  An invoke is sent to ``peer0.org1.example.com`` to move "10" from "a" to "b"

-  向 ``peer0.org1.example.com`` 发送了一个调用操作，将从 "a" 转移 "10" 给 "b"。

-  The chaincode is then installed on ``peer1.org2.example.com``

-  随后，链码被安装在 ``peer1.org2.example.com`` 上

-  A query is sent to ``peer1.org2.example.com`` for the value of "a". This starts a
   third chaincode container by the name of ``dev-peer1.org2.example.com-mycc-1.0``. A
   value of 90 is returned, correctly reflecting the previous
   transaction during which the value for key "a" was modified by 10.

-  向 ``peer1.org2.example.com`` 发起了一个查找 "a" 对应值的查询。此时会启动第 3 个容器，名为 ``dev-peer1.org2.example.com-mycc-1.0``。返回值为 90，正确的反映了之前的交易结果，其中 "a" 对应的值被转移了 "10"。

What does this demonstrate? - 演示了哪些内容？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Chaincode **MUST** be installed on a peer in order for it to
successfully perform read/write operations against the ledger.
Furthermore, a chaincode container is not started for a peer until an ``init`` or
traditional transaction - read/write - is performed against that chaincode (e.g. query for
the value of "a"). The transaction causes the container to start. Also,
all peers in a channel maintain an exact copy of the ledger which
comprises the blockchain to store the immutable, sequenced record in
blocks, as well as a state database to maintain a snapshot of the current state.
This includes those peers that do not have chaincode installed on them
(like ``peer1.org1.example.com`` in the above example) . Finally, the chaincode is accessible
after it is installed (like ``peer1.org2.example.com`` in the above example) because it
has already been instantiated.

链码 **必须** 被安装在对等节点上后，才具备了成功读写账本的能力。进一步的，链码容器只有在 ``初始化 (init)`` 或者传统读写交易（例如查询 "a" 对应的值）发生时才会启动。 是由交易启动了容器。同时，通道内的所有对等节点各自都维护了账本的一份准确的拷贝，该账本中包含了区块链（用于存储不可变且有序的区块），还包含了状态数据库（用于维护当前状态快照）。并不是每个对等节点都需要安装链码（例如上述例子中的 ``peer1.org1.example.com``）。最后，链码如果已经被实例化过一次，
则在新的对等节点上被安装后即可直接被访问（例如上述例子中的 ``peer1.org2.example.com``）。

How do I see these transactions? - 如何查看交易的具体信息？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Check the logs for the CLI Docker container.

查看 CLI Docker 容器的日志。

.. code:: bash

        docker logs -f cli

You should see the following output:

你会看到如下输出：

.. code:: bash

      2017-05-16 17:08:01.366 UTC [msp] GetLocalMSP -> DEBU 004 Returning existing local MSP
      2017-05-16 17:08:01.366 UTC [msp] GetDefaultSigningIdentity -> DEBU 005 Obtaining default signing identity
      2017-05-16 17:08:01.366 UTC [msp/identity] Sign -> DEBU 006 Sign: plaintext: 0AB1070A6708031A0C08F1E3ECC80510...6D7963631A0A0A0571756572790A0161
      2017-05-16 17:08:01.367 UTC [msp/identity] Sign -> DEBU 007 Sign: digest: E61DB37F4E8B0D32C9FE10E3936BA9B8CD278FAA1F3320B08712164248285C54
      Query Result: 90
      2017-05-16 17:08:15.158 UTC [main] main -> INFO 008 Exiting.....
      ===================== Query on peer1.org2 on channel 'mychannel' is successful =====================

      ===================== All GOOD, BYFN execution completed =====================


       _____   _   _   ____
      | ____| | \ | | |  _ \
      |  _|   |  \| | | | | |
      | |___  | |\  | | |_| |
      |_____| |_| \_| |____/

You can scroll through these logs to see the various transactions.

你可以滚动屏幕，看到各个交易的具体信息。

How can I see the chaincode logs? - 如何查看链码的日志？
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Inspect the individual chaincode containers to see the separate
transactions executed against each container. Here is the combined
output from each container:

通过查看各个链码容器，可以看到该容器相关的交易信息。如下是各个容器的日志输出：

.. code:: bash

        $ docker logs dev-peer0.org2.example.com-mycc-1.0
        04:30:45.947 [BCCSP_FACTORY] DEBU : Initialize BCCSP [SW]
        ex02 Init
        Aval = 100, Bval = 200

        $ docker logs dev-peer0.org1.example.com-mycc-1.0
        04:31:10.569 [BCCSP_FACTORY] DEBU : Initialize BCCSP [SW]
        ex02 Invoke
        Query Response:{"Name":"a","Amount":"100"}
        ex02 Invoke
        Aval = 90, Bval = 210

        $ docker logs dev-peer1.org2.example.com-mycc-1.0
        04:31:30.420 [BCCSP_FACTORY] DEBU : Initialize BCCSP [SW]
        ex02 Invoke
        Query Response:{"Name":"a","Amount":"90"}

Understanding the Docker Compose topology - 理解 Docker Compose 的拓扑结构
--------------------------------------------------------------------------------

The BYFN sample offers us two flavors of Docker Compose files, both of which
are extended from the ``docker-compose-base.yaml`` (located in the ``base``
folder).  Our first flavor, ``docker-compose-cli.yaml``, provides us with a
CLI container, along with an orderer, four peers.  We use this file
for the entirety of the instructions on this page.

本示例（BYFN）提供了两种方案的 Docker Compose 配置文件，两个方案都是基于 ``docker-compose-base.yaml`` （位于 ``base`` 目录下）扩展而来。第一种方案的配置文件为 ``docker-compose-cli.yaml``，提供了 1 个 CLI 容器、1 个排序服务节点以及 4 个对等节点。在本篇介绍中，我们使用的都是此方案。

.. note:: the remainder of this section covers a docker-compose file designed for the
          SDK.  Refer to the `Node SDK <https://github.com/hyperledger/fabric-sdk-node>`__
          repo for details on running these tests.

          本节的剩余内容涵盖了一份用于 SDK 的 docker-compose 配置文件。如果想运行这些测试案例，请参考 `Node SDK <https://github.com/hyperledger/fabric-sdk-node>`__。

The second flavor, ``docker-compose-e2e.yaml``, is constructed to run end-to-end tests
using the Node.js SDK.  Aside from functioning with the SDK, its primary differentiation
is that there are containers for the fabric-ca servers.  As a result, we are able
to send REST calls to the organizational CAs for user registration and enrollment.

第二种方案的配置文件为 ``docker-compose-e2e.yaml``，该方案构建了一个端到端的测试场景，用于运行 Node.js SDK。除了和 SDK 的交互功能外，该方案最大的区别是包含了作为 fabric-ca 服务器的容器。因此，我们可以通过发送 REST 请求给 CA，实现用户的注册和登记。

If you want to use the ``docker-compose-e2e.yaml`` without first running the
byfn.sh script, then we will need to make four slight modifications.
We need to point to the private keys for our Organization's CA's.  You can locate
these values in your crypto-config folder.  For example, to locate the private
key for Org1 we would follow this path - ``crypto-config/peerOrganizations/org1.example.com/ca/``.
The private key is a long hash value followed by ``_sk``.  The path for Org2
would be - ``crypto-config/peerOrganizations/org2.example.com/ca/``.

如果你希望不通过 byfn.sh 脚本而直接使用 ``docker-compose-e2e.yaml`` 的话，需要做 4 处修改。我们需要指定机构的 CA 的密钥。你可以指定 crypto-config 文件夹中的值。例如，机构 Org1 的密钥值，可以指定为 ``crypto-config/peerOrganizations/org1.example.com/ca/``。密钥包含了一个长哈希值，并以 ``_sk`` 结尾。机构 Org2 的密钥可以是 ``crypto-config/peerOrganizations/org2.example.com/ca/``。

In the  update the FABRIC_CA_SERVER_TLS_KEYFILE variable
for ca0 and ca1.  You also need to edit the path that is provided in the command
to start the ca server.  You are providing the same private key twice for each
CA container.

更新 ``docker-compose-e2e.yaml`` 中 ca0 和 ca1 的 FABRIC_CA_SERVER_TLS_KEYFILE 值。你还需要修改启动 ca 服务器的命令中的路径。对每一个 CA 容器，你需要提供两次相同的密钥。

Using CouchDB - 使用 CouchDB
--------------------------------------------------------------------------------

The state database can be switched from the default (goleveldb) to CouchDB.
The same chaincode functions are available with CouchDB, however, there is the
added ability to perform rich and complex queries against the state database
data content contingent upon the chaincode data being modeled as JSON.

状态数据库可以从默认的 goleveldb 切换到 CouchDB。CouchDB 提供了相同的链码函数，此外，对于采用 JSON 结构的链码数据，CouchDB 还提供了进行富查询和复杂查询的能力。

To use CouchDB instead of the default database (goleveldb), follow the same
procedures outlined earlier for generating the artifacts, except when starting
the network pass ``docker-compose-couch.yaml`` as well:

要想使用 CouchDB 替换默认的数据库 (goleveldb)，按照之前完全相同的步骤生成相关配置工件，只是在启动网络时，如下所示增加 ``docker-compose-couch.yaml`` 文件：

.. code:: bash

    docker-compose -f docker-compose-cli.yaml -f docker-compose-couch.yaml up -d

**chaincode_example02** should now work using CouchDB underneath.

**chaincode_example02** 此时就是基于 CouchDB 运行。

.. note::  If you choose to implement mapping of the fabric-couchdb container
           port to a host port, please make sure you are aware of the security
           implications. Mapping of the port in a development environment makes the
           CouchDB REST API available, and allows the
           visualization of the database via the CouchDB web interface (Fauxton).
           Production environments would likely refrain from implementing port mapping in
           order to restrict outside access to the CouchDB containers.

           如果你将 fabric-couchdb 容器的端口映射到主机端口，请确保你明白其中的安全问题。在开发环境中，端口映射后可以直接访问 CouchDB 的 REST API，还可以通过 CouchDB 网页接口 (Fauxton) 查看可视化后的数据。在生产环境中，应该尽量避免端口映射，严格限制外界对 CouchDB 容器的访问。

You can use **chaincode_example02** chaincode against the CouchDB state database
using the steps outlined above, however in order to exercise the CouchDB query
capabilities you will need to use a chaincode that has data modeled as JSON,
(e.g. **marbles02**). You can locate the **marbles02** chaincode in the
``fabric/examples/chaincode/go`` directory.

基于上述步骤，你可以基于 CouchDB 状态数据库运行 **chaincode_example02** 链码，但是，如果想利用 CouchDB 的查询能力，你还需要一个采用 JSON 结构保存数据的链码 (例如 **marbles02**)。你可以在 ``fabric/examples/chaincode/go`` 目录下找到 **marbles02** 链码的源文件。

We will follow the same process to create and join the channel as outlined in the
:ref:`createandjoin` section above.  Once you have joined your peer(s) to the
channel, use the following steps to interact with the **marbles02** chaincode:

我们将会采用和 :ref:`createandjoin` 一节相同的步骤去创建和加入通道。在将你的对等节点（们）加入到通道后，采用如下步骤去和 **marbles02** 链码进行交互：

-  Install and instantiate the chaincode on ``peer0.org1.example.com``:

- 在 ``peer0.org1.example.com`` 上安装和实例化链码：

.. code:: bash

       # be sure to modify the $CHANNEL_NAME variable accordingly for the instantiate command

       # 请确保将 $CHANNEL_NAME 修改为实例化命令中对应的值

       peer chaincode install -n marbles -v 1.0 -p github.com/chaincode/marbles02/go
       peer chaincode instantiate -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -v 1.0 -c '{"Args":["init"]}' -P "OR ('Org0MSP.peer','Org1MSP.peer')"

-  Create some marbles and move them around:

- 创建一些弹珠 (marble) 并移动它们：

.. code:: bash

        # be sure to modify the $CHANNEL_NAME variable accordingly

        # 请确保将 $CHANNEL_NAME 修改为合适的值

        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble1","blue","35","tom"]}'
        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble2","red","50","tom"]}'
        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble3","blue","70","tom"]}'
        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["transferMarble","marble2","jerry"]}'
        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["transferMarblesBasedOnColor","blue","jerry"]}'
        peer chaincode invoke -o orderer.example.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["delete","marble1"]}'

-  If you chose to map the CouchDB ports in docker-compose, you can now view
   the state database through the CouchDB web interface (Fauxton) by opening
   a browser and navigating to the following URL:

-  如果你在 docker-compose 中对 CouchDB 进行了端口映射，你可以使用 CouchDB 网页接口 (Fauxton) 去查看状态数据库的内容，需要打开浏览器并输入如下网址：

   ``http://localhost:5984/_utils``

You should see a database named ``mychannel`` (or your unique channel name) and
the documents inside it.

你会看到一个名为 ``mychannel`` （或你所指定的通道名称）的数据库及其内部的文档。

.. note:: For the below commands, be sure to update the $CHANNEL_NAME variable appropriately.
          
          对于如下命令，请确保将 $CHANNEL_NAME 修改为合适的值

You can run regular queries from the CLI (e.g. reading ``marble2``):

你可以从 CLI 发起常规查询（例如读取 ``marble2``）：

.. code:: bash

      peer chaincode query -C $CHANNEL_NAME -n marbles -c '{"Args":["readMarble","marble2"]}'

The output should display the details of ``marble2``:

输出为 ``marble2`` 的详细信息：

.. code:: bash

       Query Result: {"color":"red","docType":"marble","name":"marble2","owner":"jerry","size":50}

You can retrieve the history of a specific marble - e.g. ``marble1``:

你可以获取指定弹珠的历史信息 - 例如 ``marble1``：

.. code:: bash

      peer chaincode query -C $CHANNEL_NAME -n marbles -c '{"Args":["getHistoryForMarble","marble1"]}'

The output should display the transactions on ``marble1``:

输出为 ``marble1`` 相关的所有交易：

.. code:: bash

      Query Result: [{"TxId":"1c3d3caf124c89f91a4c0f353723ac736c58155325f02890adebaa15e16e6464", "Value":{"docType":"marble","name":"marble1","color":"blue","size":35,"owner":"tom"}},{"TxId":"755d55c281889eaeebf405586f9e25d71d36eb3d35420af833a20a2f53a3eefd", "Value":{"docType":"marble","name":"marble1","color":"blue","size":35,"owner":"jerry"}},{"TxId":"819451032d813dde6247f85e56a89262555e04f14788ee33e28b232eef36d98f", "Value":}]

You can also perform rich queries on the data content, such as querying marble fields by owner ``jerry``:

你还可以基于数据内容发起一个富查询，例如查询所有者 (owner) 为 ``jerry`` 的弹珠：

.. code:: bash

      peer chaincode query -C $CHANNEL_NAME -n marbles -c '{"Args":["queryMarblesByOwner","jerry"]}'

The output should display the two marbles owned by ``jerry``:

输出为 ``jerry`` 拥有的两个弹珠信息：

.. code:: bash

       Query Result: [{"Key":"marble2", "Record":{"color":"red","docType":"marble","name":"marble2","owner":"jerry","size":50}},{"Key":"marble3", "Record":{"color":"blue","docType":"marble","name":"marble3","owner":"jerry","size":70}}]


Why CouchDB - 为何使用 CouchDB
--------------------------------------------------------------------------------
CouchDB is a kind of NoSQL solution. It is a document oriented database where document fields are stored as key-value mpas. Fields can be either a simple key/value pair, list, or map.
In addition to keyed/composite-key/key-range queries which are supported by LevelDB, CouchDB also supports full data rich queries capability, such as non-key queries against the whole blockchain data,
since its data content is stored in JSON format and fully queryable. Therefore, CouchDB can meet chaincode, auditing, reporting requirements for many use cases that not supported by LevelDB.

CouchDB 是 NoSQL 的一种解决方案。它是一种面向文档的数据库，其中文档字段以键-值 (key-value) 的形式保存。字段可以是简单的键值对 (key/value pair)、列表 (list) 或者图 (map)。CouchDB 不仅支持 LevelDB 所支持的 keyed/composite-key/key-range 等查询方式，还提供了全数据富查询的能力，例如对于整个区块链数据的无键查询 (non-key queries)，这是因为 CouchDB 的数据内容使用了 JSON 格式进行存储，提供了全方位的查询能力。因此，在一些  LevelDB 无法支持的应用场景下，CouchDB 还可以满足链码、审计和报告等需求。

CouchDB can also enhance the security for compliance and data protection in the blockchain. As it is able to implement field-level security through the filtering and masking of individual attributes within a transaction, and only authorizing the read-only permission if needed.

CouchDB 还可以增强区块链中合规和数据保护的安全性。通过过滤和提取交易中的独立属性，它具备了字段级别的安全性，在需要时授予只读权限即可。

In addition, CouchDB falls into the AP-type (Availability and Partition Tolerance) of the CAP theorem. It uses a master-master replication model with ``Eventual Consistency``.
More information can be found on the
`Eventual Consistency page of the CouchDB documentation <http://docs.couchdb.org/en/latest/intro/consistency.html>`__.
However, under each fabric peer, there is no database replicas, writes to database are guaranteed consistent and durable (not ``Eventual Consistency``).

此外，CouchDB 是 CAP 理论中的 AP 类型 (可用性和分区容错性，Availability and Partition Tolerance)。它利用主 - 主复制模型 (master-master replication model) 实现了 ``最终一致性 (Eventual Consistency)``。更详细的信息请参考 `Eventual Consistency page of the CouchDB documentation <http://docs.couchdb.org/en/latest/intro/consistency.html>`__ 。然而，在每个 fabric 的对等节点内，并不包含数据库拷贝，对数据库的写入是担保形式的一致性和持久性 (非 ``最终一致性`` )。

CouchDB is the first external pluggable state database for Fabric, and there could and should be other external database options. For example, IBM enables the relational database for its blockchain.
And the CP-type (Consistency and Partition Tolerance) databases may also in need, so as to enable data consistency without application level guarantee.

CouchDB 是 Fabric 的第一个可插拔的外部状态数据库，可以有也应该有其他的外部数据库选项。例如，IBM 在它的区块链中采用了关系型数据库。此外，CP 类型 (一致性和分区容错性，Consistency and Partition Tolerance) 的数据库也是需要的，这样可以在不需要应用层担保的情况下实现数据的一致性。


A Note on Data Persistence - 数据持久化的注意事项
--------------------------------------------------------------------------------

If data persistence is desired on the peer container or the CouchDB container,
one option is to mount a directory in the docker-host into a relevant directory
in the container. For example, you may add the following two lines in
the peer container specification in the ``docker-compose-base.yaml`` file:

如果对等节点的容器或者 CouchDB 容器需要进行数据持久化，一种方法是将主机的目录挂载到容器内的相关目录。例如你可以将如下两行添加到 ``docker-compose-base.yaml`` 文件中对等节点容器的配置中：

.. code:: bash

       volumes:
        - /var/hyperledger/peer0:/var/hyperledger/production

For the CouchDB container, you may add the following two lines in the CouchDB
container specification:

对于 CouchDB 容器，你可以添加如下两行到 CouchDB 容器的配置中：

.. code:: bash

       volumes:
        - /var/hyperledger/couchdb0:/opt/couchdb/data

.. _Troubleshoot:

Troubleshooting - 疑难解答
--------------------------------------------------------------------------------

-  Always start your network fresh.  Use the following command
   to remove artifacts, crypto, containers and chaincode images:

-  总是在全新的环境下启动你的网络。 使用如下命令来删除工件、加密文件、容器以及链码镜像：

   .. code:: bash

      ./byfn.sh -m down

   .. note:: You **will** see errors if you do not remove old containers
             and images.

             如果你没有删除旧的容器和镜像，你 **将会** 看到错误

-  If you see Docker errors, first check your docker version (:doc:`prereqs`),
   and then try restarting your Docker process.  Problems with Docker are
   oftentimes not immediately recognizable.  For example, you may see errors
   resulting from an inability to access crypto material mounted within a
   container.

-  如果你看到 Docker 错误，首先检查你的 docker 版本 (:doc:`prereqs`)，随后尝试重启你的 Docker 进程。Docker 相关的问题有时并不能很直观的被发现。例如，如果容器内没有权限读取挂载的加密文件，你会看到错误。

   If they persist remove your images and start from scratch:

   如果这些错误始终存在，删除你的镜像，随后从头开始：

   .. code:: bash

       docker rm -f $(docker ps -aq)
       docker rmi -f $(docker images -q)

-  If you see errors on your create, instantiate, invoke or query commands, make
   sure you have properly updated the channel name and chaincode name.  There
   are placeholder values in the supplied sample commands.

-  如果在执行创建、实例化、调用或查询链码命令时遇到错误，请确保你已经正确更新了通道名称和链码名称。在提供的示例命令中包含一些占位的变量。

-  If you see the below error:

-  如果你看到如下错误

   .. code:: bash

       Error: Error endorsing chaincode: rpc error: code = 2 desc = Error installing chaincode code mycc:1.0(chaincode /var/hyperledger/production/chaincodes/mycc.1.0 exits)

   You likely have chaincode images (e.g. ``dev-peer1.org2.example.com-mycc-1.0`` or
   ``dev-peer0.org1.example.com-mycc-1.0``) from prior runs. Remove them and try
   again.

   很有可能是还有之前运行网络时生成的链码镜像（例如 ``dev-peer1.org2.example.com-mycc-1.0`` 或
   ``dev-peer0.org1.example.com-mycc-1.0`` ）。把它们删除后重试。

   .. code:: bash

       docker rmi -f $(docker images | grep peer[0-9]-peer[0-9] | awk '{print $3}')

-  If you see something similar to the following:

-  如果你看到类似如下的内容：

   .. code:: bash

      Error connecting: rpc error: code = 14 desc = grpc: RPC failed fast due to transport failure
      Error: rpc error: code = 14 desc = grpc: RPC failed fast due to transport failure

   Make sure you are running your network against the "1.0.0" images that have
   been retagged as "latest".

   请确保你是基于 "1.1.0" 版本的镜像运行你的网络，并且这些镜像都被标记为 "latest"。

-  If you see the below error:

-  如果你看到如下错误：

   .. code:: bash

     [configtx/tool/localconfig] Load -> CRIT 002 Error reading configuration: Unsupported Config Type ""
     panic: Error reading configuration: Unsupported Config Type ""

   Then you did not set the ``FABRIC_CFG_PATH`` environment variable properly.  The
   configtxgen tool needs this variable in order to locate the configtx.yaml.  Go
   back and execute an ``export FABRIC_CFG_PATH=$PWD``, then recreate your
   channel artifacts.

   这是由于你没有设置环境变量 ``FABRIC_CFG_PATH`` 导致的。configtxgen 工具需要依赖这个变量来访问 configtx.yaml。执行 ``export FABRIC_CFG_PATH=$PWD`` ，然后重新创建你的通道配置工件。

-  To cleanup the network, use the ``down`` option:

-  使用 ``down`` 选项清理网络：

   .. code:: bash

       ./byfn.sh -m down

-  If you see an error stating that you still have "active endpoints", then prune
   your Docker networks.  This will wipe your previous networks and start you with a
   fresh environment:

-  如果你看到错误提示是还有 "active endpoints"，请删除你的 Docker 网络。如下命令会清空之前的网络，使你可以从一个全新环境开始：

   .. code:: bash

        docker network prune

   You will see the following message:

   你会看到如下信息：

   .. code:: bash

      WARNING! This will remove all networks not used by at least one container.
      Are you sure you want to continue? [y/N]

   Select ``y``.

   选择 ``y``。

.. note:: If you continue to see errors, share your logs on the
          **fabric-questions** channel on
          `Hyperledger Rocket Chat <https://chat.hyperledger.org/home>`__
          or on `StackOverflow <https://stackoverflow.com/questions/tagged/hyperledger-fabric>`__.

          如果你还是遇到错误，请将你的日志分享到 `Hyperledger Rocket Chat <https://chat.hyperledger.org/home>`__ 的 **fabric-questions** 频道下，或者 `StackOverflow <https://stackoverflow.com/questions/tagged/hyperledger-fabric>`__ 。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
