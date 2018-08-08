.. translation documentation master file, created by
   sphinx-quickstart on Fri Aug 03 15:00:27 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
  

Upgrading Your Network Components 升级网络组件
=====================================================

Note 注意

When we use the term “upgrade” in this documentation, we’re primarily referring to changing the version of a component (for example, going from a v1.0.x binary to a v1.1 binary). 

当我们在本文档中使用术语“升级”时，我们主要是指更改组件的版本（例如，从v1.0.x二进制文件转换为v1.1二进制文件）。

The term “update,” on the other hand, refers not to versions but to configuration changes, such as updating a channel configuration or a deployment script.

另一方面，术语“更新”不是指版本，而是指配置更改，例如更新信道配置或部署脚本。

Overview   概述
==========================

Because the *:doc:`build_network` (BYFN)* tutorial defaults to the “latest” binaries, if you have run it since the release of v1.1, your machine will have v1.1 binaries and tools installed on it and you will not be able to upgrade them.

因为 *：doc：`build_network`（BYFN）* 教程默认使用“最新”二进制文件，如果你自v1.1发布以来已经运行它，你的机器上将会已经安装v1.1二进制文件和工具，你就不可能升级它们。

As a result, this tutorial will provide a network based on Hyperledger Fabric v1.0.6 binaries as well as the v1.1 binaries you will be upgrading to. In addition, we will show how to update channel configurations to recognize *:doc:`capability_requirements`*.

因此，本教程将提供基于Hyperledger Fabric v1.0.6二进制文件的网络以及您要升级到的v1.1二进制文件。 此外，我们将展示如何更新信道配置以识别 *：doc：`capability_requirements`*。

However, because BYFN does not support the following components, our script for upgrading BYFN will not cover them:

但是，由于BYFN不支持以下组件，因此我们用于升级BYFN的脚本不会涵盖它们：

**Fabric-CA**

**Kafka**

**SDK**

The process for upgrading these components will be covered in a section following the tutorial.

升级这些组件的过程将在本教程后面的部分中介绍。

At a high level, our upgrade tutorial will perform the following steps:

在较高级别，我们的升级教程将执行以下步骤：

1.Back up the ledger and MSPs.

2.Upgrade the orderer binaries to Fabric v1.1.

3.Upgrade the peer binaries to Fabric v1.1.

4.Enable v1.1 channel capability requirements.

1.备份分类帐和MSP。

2.将背书节点的二进制文件升级到Fabric v1.1。

3.将节点的二进制文件升级到Fabric v1.1。

4.启用v1.1信道功能要求。

Note   注意

In production environments, the orderers and peers can be upgraded on a rolling basis simultaneously (in other words, you don't need to upgrade your orderers before upgrading your peers). Where extra care must be taken is in enabling capabilities. All of the orderers and peers must be upgraded before that step (if only some orderers have been upgraded when capabilities have been enabled, a catastrophic state fork can be created).

在生产环境中，背书节点和节点可以同时滚动升级（换句话说，在升级节点之前，您无需升级背书节点）。 必须特别注意的是启用功能。 必须在该步骤之前升级所有背书节点和节点（如果在启用功能时仅升级了一些背书节点，则可以创建灾难性的状态分支）。

This tutorial will demonstrate how to perform each of these steps individually with CLI commands.

本教程将演示如何使用CLI命令单独执行每个步骤。

**Prerequisites  先决条件**

If you haven’t already done so, ensure you have all of the dependencies on your machine as described in *:doc:`prereqs`*.

如果您还没有这样做，请确保您机器上拥有所有依赖项，如 *：doc：`prereqs`* 中所述。

Launch a v1.0.6 Network    启动v1.0.6网络
===============================================

To begin, we will provision a basic network running Fabric v1.0.6 images. This network will consist of two organizations, each maintaining two peer nodes, and a “solo” ordering service.

首先，我们将提供运行Fabric v1.0.6映像的基本网络。 该网络将由两个组织组成，每个组织维护两个节点，以及一个“独立”的背书服务。

We will be operating from the first-network subdirectory within your local clone of fabric-samples. Change into that directory now. You will also want to open a few extra terminals for ease of use.

我们将在您的本地Fabric-samples克隆中的第一个网络子目录中运行。 立即切换到该目录。 您还需要打开一些额外的终端以方便使用。

**Clean up   清理**

We want to operate from a known state, so we will use the byfn.sh script to initially tidy up. This command will kill any active or stale docker containers and remove any previously generated artifacts. Run the following command:

我们希望在已知状态下运行，因此我们将使用byfn.sh脚本进行初步整理。 此命令将终止所有活动或过时的docker容器，并删除任何以前生成的工件。 运行以下命令：

*./byfn.sh -m down*

**Generate the Crypto and Bring Up the Network      生成Crypto并启动Network**

With a clean environment, launch our v1.0.6 BYFN network using these four commands:

以干净的环境使用以下四个命令启动我们的v1.0.6 BYFN网络：

*git fetch origin*

git checkout v1.0.6

*./byfn.sh -m generate*

*./byfn.sh -m up -t 3000 -i 1.0.6*

Note   注意

If you have locally built v1.0.6 images, then they will be used by the example. If you get errors, consider cleaning up v1.0.6 images and running the example again. This will download 1.0.6 images from docker hub.

如果您已经本地构建v1.0.6映像，则示例将使用它们。如果出现错误，请考虑清理v1.0.6映像并再次运行该示例。 这将从docker hub下载1.0.6图像。

If BYFN has launched properly, you will see:

如果BYFN正确启动，你会看到：

===================== All GOOD, BYFN execution completed =====================

We are now ready to upgrade our network to Hyperledger Fabric v1.1.

我们现在准备将我们的网络升级到Hyperledger Fabric v1.1。

**Get the newest samples  获取最新样本**

Note  注意

The instructions below pertain to whatever is the most recently published version of v1.1.x, starting with 1.1.0-rc1. Please substitute '1.1.x' with the version identifier of the published release that you are testing. e.g. replace 'v1.1.x' with 'v1.1.0'.

以下说明适用于最新发布的v1.1.x版本，从1.1.0-rc1开始。请将“1.1.x”替换为您正在测试的已发布版本的版本标识符。 例如 将'v1.1.x'替换为'v1.1.0'。

Before completing the rest of the tutorial, it's important to get the v1.1.x version of the samples, you can do this by:

在完成本教程的其余部分之前，获取样本的v1.1.x版本非常重要，您可以通过以下方式执行此操作：

*git fetch origin*

*git checkout v1.1.x*

**Want to upgrade now?   想立即升级吗？**

We have a script that will upgrade all of the components in BYFN as well as enabling capabilities. Afterwards, we will walk you through the steps in the script and describe what each piece of code is doing in the upgrade process.

To run the script, issue these commands:

我们有一个脚本可以升级BYFN中的所有组件以及启用功能。 然后，我们将引导您完成脚本中的步骤，并描述每个代码在升级过程中所执行的操作。

要运行该脚本，请发出以下命令：

# Note, replace '1.1.x' with a specific version, for example '1.1.0'.

# Don't pass the image flag '-i 1.1.x' if you prefer to default to 'latest' images.

#注意，将“1.1.x”替换为特定版本，例如“1.1.0”。＃如果您希望默认为“最新”图像，就不要图像标记'-i 1.1.x'。

*./byfn.sh upgrade -i 1.1.x*

If the upgrade is successful, you should see the following:

如果升级成功，你会看到：

========= All GOOD, End-2-End UPGRADE Scenario execution completed ============

if you want to upgrade the network manually, simply run ./byfn.sh -m down again and perform the steps up to -- but not including -- ./byfn.sh upgrade -i 1.1.x. Then proceed to the next section.

如果你想手动升级网络，只需再次运行./byfn.sh -m down 并执行以下步骤 - 但不包括 - ./byfn.sh upgrade -i 1.1.x. 然后继续下一部分。

Note   注意

Many of the commands you'll run in this section will not result in any output. In general, assume no output is good output.

您将在本节中运行的许多命令不会产生任何输出。 通常，假设没有输出就是好的输出。

Upgrade the Orderer Containers   升级背书节点容器
=========================================================================

Note   注意

Pay **CLOSE** attention to your orderer upgrades. If they are not done correctly -- specifically, if only some orderers are upgraded and not others -- a state fork could be created (meaning, ledgers would no longer be consistent). This MUST be avoided.

请密切关注您的背书节点升级。 如果它们没有正确完成 - 特别是，如果只升级了一些背书节点而其他背书节点没有升级 - 可以创建一个状态分支（意味着，分类账将不再一致）。 这必须是被避免的。

Order containers should be upgraded in a rolling fashion (one at a time). At a high level, the order upgrade process goes as follows:

1.Stop the orderer.

2.Back up the orderer’s ledger and MSP.

3.Restart the orderer with the latest images.

4.Verify upgrade completion.

背书节点容器（Order Containers）应以滚动方式升级（一次一个）。 在较高级别，背书节点升级过程如下：

1.停止背书节点。

2.收回背书节点的分类账和MSP。

3.使用最新图像重新打开背书节点。

4.验证升级完成。

As a consequence of leveraging BYFN, we have a solo orderer setup, therefore, we will only perform this process once. In a Kafka setup, however, this process will have to be performed for each orderer.

由于使用BYFN，我们有一个独立的背书节点的设置，因此，我们只会执行一次此过程。 但是，在Kafka设置中，必须为每个背书节点执行此过程。

Note  注意

This tutorial uses a docker deployment. For native deployments, replace the file orderer with the one from the release artifacts. Backup the orderer.yaml and replace it with the orderer.yaml file from the release artifacts. Then port any modified variables from the backed up orderer.yaml to the new one. Utilizing a utility like diff may be helpful. To decrease confusion, the variable General.TLS.ClientAuthEnabled has been renamed to General.TLS.ClientAuthRequired (just as it is specified in the peer configuration.). If the old name for this variable is still present in the orderer.yaml file, the new orderer binary will fail to start.

本教程使用docker部署。 对于本机部署，请将文件orderer 替换为发布工件中的。备份orderer.yaml并将其替换为发布工件中的orderer.yaml文件。然后将备份的orderer.yaml中的任何已经修改的变量移植到新的变量。 使用像diff这样的实用程序可能会有所帮助。 为了减少混淆，变量General.TLS.ClientAuthEnabled已重命名为General.TLS.ClientAuthRequired（就像在节点配置中指定的那样）。 如果orderer.yaml文件中仍存在此变量的旧名称，则新的orderer将无法启动。

Let’s begin the upgrade process by **bringing down the orderer:**

让我们通过 **停止背书节点（bringing down the orderer）** 来开始升级过程：

*docker stop orderer.example.com*

*export LEDGERS_BACKUP=./ledgers-backup*

# Note, replace '1.1.x' with a specific version, for example '1.1.0'.

# Set IMAGE_TAG to 'latest' if you prefer to default to the images 
tagged 'latest' on your system.

＃注意，将'1.1.x'替换为特定版本，例如'1.1.0'。＃如果您希望默认使用系统上标记为'latest'的图像，请将IMAGE_TAG设置为'latest'。

*export IMAGE_TAG=`uname -m`-1.1.x*

We have created a variable for a directory to put file backups into, and exported the IMAGE_TAG we'd like to move to. Once the orderer is down, you'll want to **backup its ledger and MSP:**

我们为目录创建了一个变量，用于将文件备份放入，并导出我们想要移动到的IMAGE_TAG。背书节点停机后，您需要 **备份其分类帐和MSP：**

*mkdir -p $LEDGERS_BACKUP*

*docker cp orderer.example.com:/var/hyperledger/production/orderer/ ./$LEDGERS_BACKUP/orderer.example.com*

In a production network this process would be repeated for each of the Kafka-based orderers in a rolling fashion.Now **download and restart the orderer** with our new fabric image:

在生产网络中，将以滚动方式为每个基于Kafka的背书节点重复该过程。 现在使用我们的新结构图像 **下载并重新启动背书节点**：

*docker-compose -f docker-compose-cli.yaml up -d --no-deps orderer.example.com*

Because our sample uses a "solo" ordering service, there are no other orderers in the network that the restarted orderer must sync up to. However, in a production network leveraging Kafka, it will be a best practice to issue peer channel fetch <blocknumber> after restarting the orderer to verify that it has caught up to the other orderers.

由于我们的示例使用“独立”背书服务，因此重新启动的背书节点必须同步到网络中没有其他背书节点。但是，在利用Kafka的生产网络中，最佳做法是在重新启动背书节点之后发行 peer channel fetch <blocknumber>，以验证它是否已赶上其他背书节点。

Upgrade the Peer Containers   升级节点容器（Peer Containers）
========================================================================

Next, let's look at how to upgrade peer containers to Fabric v1.1. Peer containers should, like the orderers, be upgraded in a rolling fashion (one at a time). As mentioned during the orderer upgrade, orderers and peers may be upgraded in parallel, but for the purposes of this tutorial we’ve separated the processes out. At a high level, we will perform the following steps:

接下来，我们来看看如何将节点容器升级到Fabric v1.1。 与背书节点一样，节点容器应以滚动方式升级（一次一个）。 正如背书节点升级期间提到的那样，背书节点和节点可以并行升级，但是为了本教程的目的，我们已经将这些进程分开了。 在较高级别，我们将执行以下步骤：

1.Stop the peer.                                     

2.Back up the peer’s ledger and MSP.                 

3.Remove chaincode containers and images.             

4.Restart the peer with with latest image.            

5.Verify upgrade completion.                         

1.停止节点

2.备份节点的分类账和MSP

3.删除链码容器和图像

4.用最新图像重新启动节点

5.验证升级完成

We have four peers running in our network. We will perform this process once for each peer, totaling four upgrades.

我们的网络中有四个节点。 我们将为每个节点执行一次此过程，总共进行四次升级。

Note   注意

Again, this tutorial utilizes a docker deployment.For **native** deployments, replace the file peer with the one from the release artifacts. Backup your core.yaml and replace it with the one from the release artifacts. Port any modified variables from the backed up core.yaml to the new one. Utilizing a utility like diff may be helpful.

同样，本教程使用了docker部署。 对于 **本机** 部署，请将peer文件替换为发布工件中的文件。 备份您的core.yaml并将其替换为发布工件中的那个。 将备份的core.yaml中的任何已修改变量移植到新的变量。使用像diff这样的实用程序可能会有所帮助。

Let’s **bring down the first peer** with the following command:

让我们用以下命令 **停止第一个节点：**

*export PEER=peer0.org1.example.com*

*docker stop $PEER*

We can then **backup the peer’s ledger and MSP:**

然后 **备份节点的分类账和MSP**

*mkdir -p $LEDGERS_BACKUP*

*docker cp $PEER:/var/hyperledger/production ./$LEDGERS_BACKUP/$PEER*

With the peer stopped and the ledger backed up, **remove the peer chaincode containers:**

在节点停止并备份分类帐后，**删除节点链码容器：**

*CC_CONTAINERS=$(docker ps | grep dev-$PEER | awk '{print $1}')if [ -n "$CC_CONTAINERS" ] ; then docker rm -f $CC_CONTAINERS ; fi*

And the peer chaincode images:

和节点链码图像：

*CC_IMAGES=$(docker images | grep dev-$PEER | awk '{print $1}')if [ -n "$CC_IMAGES" ] ; then docker rmi -f $CC_IMAGES ; fi*

Now we'll re-launch the peer using the v1.1 image tag:

现在我们将使用v1.1图像标记重新启动节点：

*docker-compose -f docker-compose-cli.yaml up -d --no-deps $PEER*

Note  注意

Although, BYFN supports using CouchDB, we opted for a simpler implementation in this tutorial. If you are using CouchDB, however, follow the instructions in the **Upgrading CouchDB** section below at this time and then issue this command instead of the one above:

尽管BYFN支持使用CouchDB，但我们在本教程中选择了更简单的实现。 但是，如果您使用的是CouchDB，请按照下面的 **“升级CouchDB”** 部分中的说明进行操作，然后发出此命令而不是上面的命令：

*docker-compose -f docker-compose-cli.yaml -f docker-compose-couch.yaml up -d --no-deps $PEER*

We'll talk more generally about how to update CouchDB after the tutorial.

我们将在该教程之后更全面地讨论如何更新CouchDB。

**Verify Upgrade Completion   验证升级完成**

We’ve completed the upgrade for our first peer, but before we move on let’s check to ensure the upgrade has been completed properly with a chaincode invoke. Let’s move 10 from a to b using these commands:

我们已完成第一个节点的升级，但在我们进行之前，请通过调用链代码正确以确保完成了升级。 让我们使用以下命令将10从a移动到b：

*docker-compose -f docker-compose-cli.yaml up -d --no-deps cli*
*docker exec -it cli bash*
*peer chaincode invoke -o orderer.example.com:7050  --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem  -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'*

Our query earlier revealed a to have a value of 90 and we have just removed 10 with our invoke. Therefore, a query against a should reveal 80. Let’s see:

我们之前的查询显示a值为90，我们刚刚使用调用移动了10。 因此，对a的查询应该显示80.让我们看看：

*peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'*

We should see the following:

我们会看到一下：

*Query Result: 80*

After verifying the peer was upgraded correctly, make sure to issue an exit to leave the container before continuing to upgrade your peers. You can do this by repeating the process above with a different peer name exported.

在验证节点已正确升级后，请确保在继续升级节点之前执行退出以离开容器。 您可以通过重复上述过程并导出不同的节点名称来完成此操作。

*export PEER=peer1.org1.example.com*

*export PEER=peer0.org2.example.com*

*export PEER=peer1.org2.example.com*

Note  注意

All peers must be upgraded BEFORE enabling capabilities.

在启用功能之前，必须升级所有节点。

Enable Capabilities for the Channels    启用信道功能
=======================================================

Because v1.0.x Fabric binaries do not understand the concept of channel capabilities, extra care must be taken when initially enabling capabilities for a channel.

由于v1.0.x Fabric二进制文件不了解信道功能的概念，因此在初始启用信道功能时必须格外小心。

Although Fabric binaries can and should be upgraded in a rolling fashion, **it is critical that the ordering admins not attempt to enable v1.1 capabilities until all orderer binaries are at v1.1.x+** . If any orderer is executing v1.0.x code, and capabilities are enabled for a channel, the blockchain will fork as v1.0.x orderers invalidate the change and v1.1.x+ orderers accept it. This is an exception for the v1.0 to v1.1 upgrade. For future upgrades, such as v1.1 to v1.2, the ordering network will handle the upgrade more gracefully and prevent the state fork.

尽管Fabric二进制文件可以并且应该以滚动方式进行升级，**但是背书节点管理员不要尝试启用v1.1功能直到所有背书节点二进制文件都是v1.1.x +，这是至关重要的一点**。
如果任何背书节点正在执行v1.0.x代码且又为一个信道启用了功能，则区块链将会分叉是由于v1.0.x的背书节点使更改无效并且v1.1.x + 的背书节点接受了它。这是v1.0到v1.1升级的例外。对于未来的升级，例如v1.1到v1.2，背书节点网络将更优雅地处理升级并防止状态分叉。

In order to minimize the chance of a fork, attempts to enable the application or channel v1.1 capabilities before enabling the orderer v1.1 capability will be rejected.

为了最小化分叉的可能性，在启用背书节点v1.1功能之前，尝试启用应用程序或信道v1.1功能将被拒绝。

Since the orderer v1.1 capability can only be enabled by the ordering admins, making it a prerequisite for the other capabilities prevents application admins from accidentally enabling capabilities before the orderer is ready to support them.

由于背书节点v1.1功能只能由背书节点管理员启用，使其成为其他功能的先决条件，可以防止应用程序管理员在背书节点准备好支持它们之前意外启用功能。

Note   注意

Once a capability has been enabled, disabling it is not recommended or supported.

一但功能被启用，是不建议或不支持禁用它的。

Once a capability has been enabled, it becomes part of the permanent record for that channel. This means that even after disabling the capability, old binaries will not be able to participate in the channel because they cannot process beyond the block which enabled the capability to get to the block which disables it.

一但功能被启用，它将成为该信道的永久记录的一部分。这意味着即使在禁用该功能之后，旧的二进制文件也将无法参与到该信道中，因为它们无法处理超过启用功能的块进入到禁用的块这个过程。

For this reason, think of enabling channel capabilities as a point of no return. Please experiment with the new capabilities in a test setting and be confident before proceeding to enable them in production.

因此，将信道功能视为不归路。 请在测试设置中尝试新功能，这样就能够在生产中启用它们时充满信心。

Note that enabling capability requirements on a channel which a v1.0.0 peer is joined to will result in a crash of the peer. This crashing behavior is deliberate because it indicates a misconfiguration which might result in a state fork.

请注意，在v1.0.0节点加入的通道上启用功能要求将导致节点崩溃。 这种崩溃行为是故意的，因为它表明这是一个可能导致状态分叉的配置错误。

The error message displayed by failing v1.0.x peers will say:

失败的v1.0.x节点显示的错误消息是：

*Cannot commit block to the ledger due to Error validating config which passed initial validity checks: ConfigEnvelope LastUpdate did not produce the supplied config result*

We will enable capabilities in the following order:

我们将按以下顺序启用功能：

1.Orderer System Channel     1.背书节点系统信道 

 1）Orderer Group            1）背书节点组
 
 2）Channel Group            2）信道组

2.Individual Channels         2 个人信道

 1）Orderer Group             1）背书节点组

 2）Channel Group             2）信道组

 3）Application Group           3）应用组

Note  注意

In order to minimize the chance of a fork a best practice is to enable the orderer system capability first and then enable individual channel capabilities.

为了最大限度地减少分叉的可能性，最佳做法是首先启用背书节点系统功能，然后启用个人信道功能。

For each group, we will enable the capabilities in the following order:

对于每一组，我们将按以下顺序启用这些功能：

1.Get the latest channel config

2.Create a modified channel config

3.Create a config update transaction

1.获取最新的信道配置

2.创建修改后的信道配置

3.创建配置更新事务

Note  注意

This process will be accomplished through a series of config update transactions, one for each channel group. In a real world production network, these channel config updates would be handled by the admins for each channel. Because BYFN all exists on a single machine, it is possible for us to update each of these channels.

此过程将通过一系列配置更新事务完成，每个信道组一个。 在现实生产网络中，这些信道配置更新将由每个信道的管理员处理。 由于BYFN都存在于一台机器上，因此更新每个通道是有可能的。

For more information on updating channel configs, click on *:doc:`channel_update_tutorial`* or the doc on *:doc:`config_update`*.

有关更新信道配置的更多信息，请单击 *：doc：`channel_update_tutorial`* 或 *doc on：doc：`config_update`*。

Get back into the cli container by reissuing *docker exec -it cli bash*.

通过重新执行 *docker exec -it cli bash* 返回cli容器。

Now let’s check the set environment variables with:

现在让我们检查设置的环境变量：

*env|grep PEER*

You'll also need to install jq:

你还需要安装jq：

*apt-get update*

*apt-get install -y jq*

**Orderer System Channel Capabilities    背书节点系统信道功能**

Let’s set our environment variables for the orderer system channel. Issue each of these commands:

让我们为背书节点系统信道设置环境变量。发出以下每个命令：

*CORE_PEER_LOCALMSPID="OrdererMSP"*

*CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.examp
le.com/msp/tlscacerts/tlsca.example.com-cert.pem*

*CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/users/Admin@example.com/msp*

*ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscac
erts/tlsca.example.com-cert.pem*

And let’s set our channel name to testchainid:

让我们将我们的信道名称设置为testchainid：

*CH_NAME=testchainid*

**Orderer Group  背书节点组**

The first step in updating a channel configuration is getting the latest config block:

更新信道配置的第一步是获取最新的配置块：

*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CH_NAME  --tls --cafile $ORDERER_CA*

Note  注意

We require configtxlator v1.0.0 or higher for this next step.To make our config easy to edit, let’s convert the config block to JSON using configtxlator:

我们需要configtxlator v1.0.0或更高版本才能完成下一步。 为了使我们的配置易于编辑，让我们使用configtxlator将配置块转换为JSON：

*configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json*

This command uses jq to remove the headers, metadata, and signatures from the config:

此命令使用jq从配置中删除标头，元数据和签名：

*jq .data.data[0].payload.data.config config_block.json > config.json*

Next, add capabilities to the orderer group. The following command will create a copy of the config file and add our new capabilities to it:

接下来，为背书节点组添加功能。 以下命令将创建配置文件的副本并向其添加新功能：

*jq -s '.[0] * {"channel_group":{"groups":{"Orderer": {"values": {"Capabilities": .[1]}}}}}' config.json ./scripts/capabilities.json > modified_config.json*

Note what we’re changing here: Capabilities are being added as a value of the orderer group under channel_group. The specific channel we’re working in is not noted in this command, but recall that it’s the orderer system channel testchainid. It should be updated first because it is this channel’s configuration that will be copied by default during the creation of any new channel.Now we can create the config update:

注意我们在这里要改变的内容：功能做为channel_group下的背书节点组的一个值被添加进来。我们正在使用的特定信道未在此命令中注明，但请记住它是名为testchainid的背书节点系统信道。它应该首先更新，因为正是这个信道的配置将在创建任何其他新通道时被默认复制。现在我们可以创建配置更新：

*configtxlator proto_encode --input config.json --type common.Config --output config.pb*

*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*

*configtxlator compute_update --channel_id $CH_NAME --original config.pb --updated modified_config.pb --output config_update.pb*

Package the config update into a transaction:

将配置更新打包到事务中：

*configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json*

*echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CH_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json*

*configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb*

Submit the config update transaction:

提交配置更新事务：

Note    注意
The command below both signs and submits the transaction to the ordering service.

下面的命令都是用来签署并将交易提交给背书服务的。

*peer channel update -f config_update_in_envelope.pb -c $CH_NAME -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA*

Our config update transaction represents the difference between the original config and the modified one, but the orderer will translate this into a full channel config.

我们的配置更新事务表示原始配置和修改后的配置之间的差异，但是背书节点会将其转换为完整的信道配置。

**Channel Group  信道组**

Now let’s move on to enabling capabilities for the channel group at the orderer system level.

现在让我们继续在背书系统级别为信道组启用功能。

The first step, as before, is to get the latest channel configuration.

与以前一样，第一步是获取最新的信道配置。

Note   注意
 
This set of commands is exactly the same as the steps from the orderer group.

这组命令与背书节点组中的步骤完全相同。

*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CH_NAME --tls --cafile $ORDERER_CA*

*configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json*

*jq .data.data[0].payload.data.config config_block.json > config.json*

Next, create a modified channel config:

接下来，创建一个修改后的信道配置：

*jq -s '.[0] * {"channel_group":{"values": {"Capabilities": .[1]}}}' config.json ./scripts/capabilities.json > modified_config.json*

Note what we’re changing here: Capabilities are being added as a value of the top level channel_group (in the testchainid channel, as before).Create the config update transaction:

注意我们在这里要改变的地方：功能作为一个顶级channel_group的值（在testchainid信道中，如前所述）被添加。
创建配置更新事务：

Note   注意

This set of commands is exactly the same as the third step from the orderer group.

这组命令与背书节点组的第三步完全相同。

*configtxlator proto_encode --input config.json --type common.Config --output config.pb*

*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*

*configtxlator compute_update --channel_id $CH_NAME --original config.pb --updated modified_config.pb --output config_update.pb*

Package the config update into a transaction:

将配置更新打包到事务中：

*configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json*

*echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CH_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | 
jq . > config_update_in_envelope.json*

*configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb*

Submit the config update transaction:

提交配置更新事务：

*peer channel update -f config_update_in_envelope.pb -c $CH_NAME -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA*

Enabling Capabilities on Existing Channels 在现有信道上启用功能
====================================================================

Set the channel name to mychannel:

将信道名称设置为mychannel：

*CH_NAME=mychannel*

**Orderer Group    背书节点组**

Get the channel config:

获得信道配置：

*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CH_NAME  --tls --cafile $ORDERER_CA*

*configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json*

*jq .data.data[0].payload.data.config config_block.json > config.json*

Let’s add capabilities to the orderer group. The following command will create a copy of the config file and add our new capabilities to it:

让我们为背书节点组添加功能。 以下命令将创建配置文件的副本并向其添加新功能：

*jq -s '.[0] * {"channel_group":{"groups":{"Orderer": {"values": {"Capabilities": .[1]}}}}}' config.json ./scripts/capabilities.json > 
modified_config.json*

Note what we’re changing here: Capabilities are being added as a value of the orderer group under channel_group. This is exactly what we changed before, only now we’re working with the config to the channel mychannel instead of testchainid.

注意我们在这里要改变的内容：功能作为channel_group下的背书节点组的值被添加。 这正是我们之前改变的，只是现在我们正在使用的是对信道mychannel的配置而不是testchainid的。

Create the config update:

创建配置更新：

*configtxlator proto_encode --input config.json --type common.Config --output config.pb*

*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*

*configtxlator compute_update --channel_id $CH_NAME --original config.pb --updated modified_config.pb --output config_update.pb*

Package the config update into a transaction:

将配置更新打包到事务中：

*configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json*

*echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CH_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | 
jq . > config_update_in_envelope.json*

*configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb*

Submit the config update transaction:

提交配置更新事务：

*peer channel update -f config_update_in_envelope.pb -c $CH_NAME -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA*

**Channel Group    信道组**

Note

注意

While this may seem repetitive, remember that we're performing the same process on different groups. In a production network, as we've said, this process would likely be split up among the various channel admins.
Fetch, decode, and scope the config:

虽然这看似重复，但请记住，我们在不同的组上执行相同的过程。 在生产网络中，正如我们所说，这个过程可能会在各个信道管理员之间分开。
获取，解码和范围配置：

*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CH_NAME --tls --cafile $ORDERER_CA*
*configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json*
*jq .data.data[0].payload.data.config config_block.json > config.json*

Create a modified config:

创建修改后的配置：

*jq -s '.[0] * {"channel_group":{"values": {"Capabilities": .[1]}}}' config.json ./scripts/capabilities.json > modified_config.json*

Note what we’re changing here: Capabilities are being added as a value of the top level channel_group (in mychannel, as before).

注意我们在这里要改变的内容：功能被添加为顶级channel_group的值（在mychannel中，如前所述）。

Create the config update:

创建配置更新：

*configtxlator proto_encode --input config.json --type common.Config --output config.pb*

*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*

*configtxlator compute_update --channel_id $CH_NAME --original config.pb --updated modified_config.pb --output config_update.pb*

Package the config update into a transaction:

将配置更新打包到事务中：

*configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json*

*echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CH_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | 
jq . > config_update_in_envelope.json*

*configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb*

Because we're updating the config of the channel group, the relevant orgs -- Org1, Org2, and the OrdererOrg -- need to sign it. This task would usually be performed by the individual org admins, but in BYFN, as we've said, this task falls to us.

因为我们正在更新信道组的配置，所以相关的orgs - Org1，Org2和OrdererOrg - 需要对其进行签名。 这项任务通常由个别组织管理员执行，但在BYFN中，正如我们所说，这项任务落在我们身上。

First, switch into Org1 and sign the update:

首先，切换到Org1并签署更新：

CORE_PEER_LOCALMSPID="Org1MSP"

*CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt*

*CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp*

*CORE_PEER_ADDRESS=peer0.org1.example.com:7051*

*peer channel signconfigtx -f config_update_in_envelope.pb*

And do the same as Org2:

对Org1做同样的操作：

*CORE_PEER_LOCALMSPID="Org2MSP"*

*CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt*

*CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp*

*CORE_PEER_ADDRESS=peer0.org1.example.com:7051*
*peer channel signconfigtx -f config_update_in_envelope.pb*

And as the OrdererOrg:

对OrdererOrg做同样操作：

*CORE_PEER_LOCALMSPID="OrdererMSP"*

*CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem*

*CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/users/Admin@example.com/msp*

*peer channel update -f config_update_in_envelope.pb -c $CH_NAME -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA*

**Application Group  应用组**

For the application group, we will need to reset the environment variables as one organization:

对于应用程序组，我们需要将环境变量重置为一个组织：

*CORE_PEER_LOCALMSPID="Org1MSP"*

*CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt*

*CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp*

*CORE_PEER_ADDRESS=peer0.org1.example.com:7051*

Now, get the latest channel config (this process should be very familiar by now):

现在，获取最新的信道配置（此过程现在应该非常熟悉）：

*peer channel fetch config config_block.pb -o orderer.example.com:7050 -c $CH_NAME --tls --cafile $ORDERER_CA*

*configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json*

*jq .data.data[0].payload.data.config config_block.json > config.json*

Create a modified channel config:

创建修改后的信道配置：

*jq -s '.[0] * {"channel_group":{"groups":{"Application": {"values": {"Capabilities": .[1]}}}}}' config.json ./scripts/capabilities.json > modified_config.json*

Note what we’re changing here: Capabilities are being added as a value of the Application group under channel_group(in mychannel).

注意我们在这里要改变的内容：功能作为channel_group（在mychannel中）下的应用程序组的值而被添加。

Create a config update transaction:

创建配置更新事务：

*configtxlator proto_encode --input config.json --type common.Config --output config.pb*

*configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb*

*configtxlator compute_update --channel_id $CH_NAME --original config.pb --updated modified_config.pb --output config_update.pb*

Package the config update into a transaction:

将配置更新打包到事务中：

*configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json*

*echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CH_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | 
jq . > config_update_in_envelope.json*

*configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb*

Org1 signs the transaction:

Org1对事务进行签名：

*peer channel signconfigtx -f config_update_in_envelope.pb*

Set the environment variables as Org2:

将环境变量设置为Org2：

export CORE_PEER_LOCALMSPID="Org2MSP"

*export
CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt*

*export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp*

*export CORE_PEER_ADDRESS=peer0.org2.example.com:7051*

Org2 submits the config update transaction with its signature:

Org2使用其签名提交配置更新事务：

*peer channel update -f config_update_in_envelope.pb -c $CH_NAME -o orderer.example.com:7050 --tls true --cafile $ORDERER_CA*

Congratulations! You have now enabled capabilities on all of your channels.

恭喜！ 您现在已在所有信道上启用了功能。

**Verify that Capabilities are Enabled       验证功能是否已启用**

But let's test just to make sure by moving 10 from a to b, as before:

但是让我们测试只是为了确保将10从a移动到b，如前所述：

*peer chaincode invoke -o orderer.example.com:7050  --tls --cafile 
/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.
example.com-cert.pem  -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'*

And then querying the value of a, which should reveal a value of 70. Let’s see:

然后查询a的值，它应该显示70的值。让我们看看：

*peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'*

We should see the following:

我们应该看到以下内容：

*Query Result: 70*

In which case we have successfully added capabilities to all of our channels.

在这种情况下，我们已成功为所有信道添加功能。

Note    注意

Although all peer binaries in the network should have been upgraded prior to this point, enabling capability requirements on a channel which a v1.0.0 peer is joined to will result in a crash of the peer. This crashing behavior is deliberate because it indicates a misconfiguration which might result in a state fork.

虽然网络中的所有节点二进制文件都应该在此之前进行升级，但是在一个v1.0.0节点加入的信道上启用功能的要求将导致节点崩溃。 这种崩溃行为是故意的，因为它表明可能导致状态分叉的配置错误。

Upgrading Components BYFN Does Not Support         升级BYFN不支持的组件
============================================================================

Although this is the end of our update tutorial, there are other components that exist in production networks that are not supported by the BYFN sample. In this section, we’ll talk through the process of updating them.

虽然这是我们的更新教程的结束，但生产网络中还存在BYFN示例不支持的其他组件。 在本节中，我们将讨论更新它们的过程。

**Fabric CA Container   Fabric CA 容器**

To learn how to upgrade your Fabric CA server, click over to the CA documentation.

要了解如何升级Fabric CA服务器，请单击CA文档。

**Upgrade Node SDK Clients    升级节点SDK客户端**

Note   注意

Upgrade Fabric CA before upgrading Node SDK Clients.

升级Node SDK客户端之前升级Fabric CA.

Use NPM to upgrade any Node.js client by executing these commands in the root directory of your application:

使用NPM通过在应用程序的根目录中执行以下命令来升级任何Node.js客户端：

*npm install fabric-client@1.1*

*npm install fabric-ca-client@1.1*

These commands install the new version of both the Fabric client and Fabric-CA client and write the new versions package.json.

这些命令安装了Fabric客户端和Fabric-CA客户端的新版本，并编写新版本package.json。

**Upgrading the Kafka Cluster   升级Kafka集群**

It is not required, but it is recommended that the Kafka cluster be upgraded and kept up to date along with the rest of Fabric. Newer versions of Kafka support older protocol versions, so you may upgrade Kafka before or after the rest of Fabric.

这不是必需的，但建议升级Kafka集群并与Fabric的其余部分保持同步。 较新版本的Kafka支持较旧的协议版本，因此您可以在Fabric的其余部分之前或之后升级Kafka。

If your Kafka cluster is older than Kafka v0.11.0, this upgrade is especially recommended as it hardens replication in order to better handle crash faults.

如果您的Kafka集群早于Kafka v0.11.0，则特别推荐此升级，因为它会加强复制以便更好地处理崩溃故障。

Refer to the official Apache Kafka documentation on *upgrading Kafka from previous versions* to upgrade the Kafka cluster brokers.

有关 *从先前版本升级Kafka* 以升级Kafka集群代理的信息，请参阅官方Apache Kafka文档。

Please note that the Kafka cluster might experience a negative performance impact if the orderer is configured to use a Kafka protocol version that is older than the Kafka broker version. The Kafka protocol version is set using either the Kafka.Versionkey in the orderer.yaml file or via the ORDERER_KAFKA_VERSION environment variable in a Docker deployment. Fabric v1.0 provided sample Kafka docker images containing Kafka version 0.9.0.1. Fabric v1.1 provides sample Kafka docker images containing Kafka version v1.0.0.

请注意，如果将背书节点配置为使用早于Kafka代理版本的Kafka协议版本，则Kafka集群可能会对性能产生负面影响。 使用orderer.yaml文件中的Kafka.Versionkey或Docker部署中的ORDERER_KAFKA_VERSION环境变量设置Kafka协议版本。 Fabric v1.0提供了包含Kafka0.9.0.1版本的示例的Kafka docker镜像。 Fabric v1.1提供了包含Kafkav1.0.0版本的示例的Kafka docker镜像。

Note   注意

You must configure the Kafka protocol version used by the orderer to match your Kafka cluster version, even if it was not set before. For example, if you are using the sample Kafka images provided with Fabric v1.0.x, either set the ORDERER_KAFKA_VERSION environment variable, or the Kafka.Version key in the orderer.yaml to 0.9.0.1. If you are unsure about your Kafka cluster version, you can configure the orderer's Kafka protocol version to 0.9.0.1 for maximum compatibility and update the setting afterwards when you have determined your Kafka cluster version.

您必须配置背书节点使用的Kafka协议版本以匹配您的Kafka集群版本，即使之前未设置它。 例如，如果您使用Fabric v1.0.x提供的示例Kafka映像，请将ORDERER_KAFKA_VERSION环境变量或orderer.yaml中的Kafka.Version键设置为0.9.0.1。 如果您不确定Kafka集群版本，可以将背书节点的Kafka协议版本配置为0.9.0.1以获得最大兼容性，并在确定Kafka集群版本后更新设置。

**Upgrading Zookeeper   升级Zookeeper**

An Apache Kafka cluster requires an Apache Zookeeper cluster. The Zookeeper API has been stable for a long time and, as such, almost any version of Zookeeper is tolerated by Kafka. Refer to the Apache Kafka upgrade documentation in case there is a specific requirement to upgrade to a specific version of Zookeeper. If you would like to upgrade your Zookeeper cluster, some information on upgrading Zookeeper cluster can be found in the Zookeeper FAQ.

Apache Kafka集群需要Apache Zookeeper集群。 Zookeeper API已经稳定了很长时间，因此，Kafka几乎可以容忍任何版本的Zookeeper。 如果有特定要求升级到特定版本的Zookeeper，请参阅Apache Kafka升级文档。 如果您想升级Zookeeper集群，可以在Zookeeper FAQ中找到有关升级Zookeeper集群的一些信息。

**Upgrading CouchDB  升级 CouchDB**

If you are using CouchDB as state database, upgrade the peer's CouchDB at the same time the peer is being upgraded. To upgrade CouchDB:
如果您使用CouchDB作为状态数据库，请在升级节点的同时升级节点的CouchDB。 要升级CouchDB：

1.Stop CouchDB.

#.Backup CouchDB data directory.

#.Delete CouchDB data directory.

#.Install CouchDB v2.1.1 binaries or update deployment scripts to use a new Docker image (CouchDB v2.1.1 pre-configured Docker image is provided alongside Fabric v1.1).

#.Restart CouchDB. 

1.停止CouchDB。

#.备份CouchDB数据目录。

#.删除CouchDB数据目录。

#.安装CouchDB v2.1.1二进制文件或更新部署脚本以使用新的Docker镜像（CouchDB v2.1.1预配置的Docker镜像与Fabric v1.1一起提供）。
   
#.重启CouchDB

The reason to delete the CouchDB data directory is that upon startup the v1.1 peer will rebuild the CouchDB state databases from the blockchain transactions. Starting in v1.1, there will be an internal CouchDB database for each channel_chaincodecombination (for each chaincode instantiated on each channel that the peer has joined).

删除CouchDB数据目录的原因是，在启动时，v1.1 节点将从区块链事务重建CouchDB状态数据库。 从v1.1开始，每个channel_chaincodecombination都会有一个内部CouchDB数据库（对于已经加入的节点的每个信道上实例化的每个链代码）。

#.Restart CouchDB. 

**Upgrade Chaincodes With Vendored Shim    使用Vendored Shim升级Chaincodes**

A number of third party tools exist that will allow you to vendor a chaincode shim. If you used one of these tools, use the same one to update your vendoring and re-package your chaincode.
If your chaincode vendors the shim, after updating the shim version, you must install it to all peers which already have the chaincode. Install it with the same name, but a newer version. Then you should execute a chaincode upgrade on each channel where this chaincode has been deployed to move to the new version.

存在许多第三方工具，允许您提供chaincode shim。 如果您使用其中一种工具，请使用相同的工具更新您的Vendore并重新打包您的链码。
如果你的chaincode vendor是shim，在更新shim版本之后，你必须将它安装到已经拥有链码的所有节点中。 使用相同的名称安装它，但是更新版本。 然后，您应该在已部署此链代码的每个信道上执行链代码升级，以转移到新版本。

If you did not vendor your chaincode, you can skip this step entirely.

如果您没有提供链码，则可以完全跳过此步骤。

