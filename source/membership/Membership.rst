.. translation documentation master file, created by
   sphinx-quickstart on Fri Aug 03 15:00:27 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
  

===============================
Membership 会员身份
===============================
If you've read through the documentation on Identity you've seen how a PKI can provide verifiable identities through a chain of trust.Now let's see how these identities can be used to represent the trusted members of a blockchain network. 

如果您已经阅读了有关Identity（身份）的文档，那么您已经了解了PKI如何通过信任链提供可验证的身份。现在让我们看看这些身份如何用于表示区块链网络的可信成员。

This is where a **Membership Service** Provider (MSP) comes into play -- **it identifies which Root CAs and Intermediate CAs are trusted to define the members of a trust domain, e.g., an organization**, either by listing the identities of their members, or by identifying which CAs are authorized to issue valid identities for their members, or -- as will usually be the case -- through a combination of both.

这是 **会员服务** 提供商（MSP）发挥作用的地方 - **它通过列出成员的身份或者通过识别哪些CA被授权为其成员发布有效身份**，或者 - 通常情况下 - 通过两者的组合，来定义哪些根CA和中间CA是被信任的是可以来定义信任域（例如组织）的成员的。

The power of an MSP goes beyond simply listing who is a network participant or member of a channel. An MSP can identify specific roles an actor might play either within the range or the organization (trust domain) the MSP represents (e.g., MSP admins, members of an organization subdivision), and sets the basis for defining access privileges in the the context of a network and channel (e.g., channel admins, readers, writers). The configuration of an MSP is advertised to all the channels, where members of the corresponding organization participate (in the form of a **channel MSP**). Peers, orderers and clients also maintain a local MSP instance (also known as **lLocal MSP**) to authenticate messages of members of their organization outside the context of a channel.In addition, an MSP can allow for the identification of a list of identities that have been revoked (we discussed this in the Identity documentation but will talk about how that process also extends to an MSP). 

MSP的强大功能不仅仅是列出谁是网络参与者或是信道的（channel）成员。MSP可以定义参与者可能在MSP所代表的范围或组织（信任域）内扮演的特定角色（例如，MSP管理员，组织分支的成员），并为在网络和信道的背景下（例如，信道管理员，阅读者，写入者）定义访问权限奠定了基础。MSP的配置被通告给所有信道，其中相应组织的成员（以一个 **信道MSP** 的形式）参与。节点，背书节点和客户端还维护一个本地MSP实例（也称为 **lLocal MSP** ），以在信道背景之外验证其组织成员的消息。此外，MSP允许识别已被撤销的身份列表（我们在身份文档中对此进行了讨论，但将讨论该流程是如何扩展到MSP的）。
We'll talk more about local and channel MSPs in a moment. For now let's talk more about what MSPs do in general.

我们稍后会详细讨论本地MSP和信道MSP。 现在让我们来谈谈通常情况下，MSP是怎么做的。

Mapping MSPs to Organizations  将MSP映射到组织
------------------------------------------------------
An organization is a managed group of members and can be something as big as a multinational corporation or as small as a flower shop. What's most important about organizations (or **orgs**) is that they manage their members under a single MSP.Note that this is different from the organization concept defined in an X.509 certificate, which we'll talk about later. 

组织是一个受管理的成员组织，可以像跨国公司那样大，也可以像花店一样小。 **组织（orgs）** 最重要的是他们在一个MSP下管理他们的成员。请注意，这与X.509证书中定义的组织概念不同，我们将在稍后讨论。

The exclusive relationship between an organization and its MSP makes it sensible to name the MSP after the organization, a convention you'll find adopted in most policy configurations. For example, organization ORG1 would have an MSP called ORG1-MSP. In some cases an organization may require multiple membership groups -- for example, where channels are used to perform very different business functions between organisations. In these cases it makes sense to have multiple MSPs and name them accordingly, e.g., ORG2-MSP-NATIONAL and ORG2-MSP-GOVERNMENT, reflecting the different membership roots of trust within ORG2 in the NATIONAL sales channel compared to the GOVERNMENT regulatory channel.

组织与其MSP之间的独占关系使得在组织之后命名MSP是明智的，这是大多数策略配置中都会采用的约定。例如，组织ORG1将有一个名为ORG1-MSP的MSP。在某些情况下，组织可能需要多个成员组 - 例如，使用信道在组织之间执行非常不同的业务功能。在这些情况下，有多个MSP并相应地命名它们是有意义的，例如，ORG2-MSP-NATIONAL和ORG2-MSP-GOVERNMENT，反映了与GOVERNMENT监管信道相比，在NATIONAL销售信道中的ORG2信任不同成员根。

.. figure:: membership.diagram.3.png

Two different MSP configurations for an organization.The first configuration shows the typical relationship between an MSP and an organization -- a single MSP defines the list of members of an organization. In the second configuration, different MSPs are used to represent different organizational groups with national, international, and governmental affiliation. 

一个组织的两种不同MSP配置第一个配置显示了MSP和组织之间的典型关系 - 单个MSP定义了组织成员列表。 在第二种配置中，不同的MSP用于表示具有国家，国际和政府关联的不同组织组。

Organizational Units and MSPs 组织单位和MSP
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An organization is often divided up into multiple **organizational units** (OUs), each of which has a certain set of responsibilities.For example, the ORG1 organization might have both ORG1-MANUFACTURING and ORG1-DISTRIBUTION OUs to reflect these separate lines of business.When a CA issues X.509 certificates, the OU field in the certificate specifies the line of business to which the identity belongs. 

一个组织通常被划分为多个 **组织单位** （OU），每个组织单位都有一定的责任。例如，ORG1组织可能同时具有ORG1-MANUFACTURING和ORG1-DISTRIBUTION OU，以反映这些独立的业务线。当CA颁发X.509证书时，证书中的OU字段指定标识所属的业务线。

We'll see later how OUs can be helpful to control the parts of an organization who are considered to be the members of a blockchain network. For example, only identities from the ORG1-MANUFACTURING OU might be able to access a channel, whereas ORG1-DISTRIBUTION cannot.

稍后我们将看到OU如何有助于控制被视为区块链网络成员的一个组织。例如，只有ORG1-MANUFACTURING OU中的标识可能能够访问信道，而ORG1-DISTRIBUTION中的则不能。

Finally, though this is a slight misuse of OUs, they can sometimes be used by different* organizations in a consortium to distinguish each other.In such cases, the different organizations use the same Root CAs and Intermediate CAs for their chain of trust, but assign the OU field appropriately to identify members of each organization. We'll also see how to configure MSPs to achieve this later. 

最后，尽管这是对OU的轻微滥用，但它们有时可以被联盟中的不同组织用来区分彼此。在这种情况下，不同的组织对其信任链使用相同的根CA和中间CA，但适当地分配OU字段以标识每个组织的成员。 我们还将看到如何配置MSP以便以后实现此目的。


Local and Channel MSPs  本地和信道MSP
---------------------------------------------
MSPs appear in two places in a Blockchain network: channel configuration (**channel MSPs**), and locally on an actor's premise (**local MSP**). **Local MSPs defined for nodes** (peer or orderer) and **users** (administrators that use the CLI or client applications that use the SDK). **Every node and user must have a local MSP defined**, as it defines who has administrative or participatory rights at that level and outside the context of a channel (who the administrators of a peer's organization, for example).In contrast, **channel MSPs define administrative and participatory rights at the channel level**. Every organization participating in a channel must have an MSP defined for it. Peers and orders on a channel will all share the same view on channel MSPs, and will henceforth be able to authenticate correctly the channel participants. This means that if an organization wishes to join the channel, an MSP incorporating the chain of trust for the organization's members would need to be included in the channel configuration. Otherwise transactions originating from this organization's identities will be rejected.

MSP出现在区块链网络中的两个位置：信道配置（ **信道MSP** ）以及在参与者的本地（ **本地MSP** ）。 **本地MSP是为节点（节点或背书节点）和用户（使用CLI的管理员或使用SDK的客户端应用程序）定义的**。每个节点和用户都必须定义一个本地MSP，因为它定义了在该级别和在信道背景之外谁具有管理或参与的权限（例如，一个节点组织的管理员）。 **相反，信道MSP在信道级别定义了管理和参与的权利**。 **参与信道的每个组织都必须有一个为其定义的MSP**。一个信道上的节点和背书节点将在信道MSP上共享相同的视图，并且此后将能够正确地验证信道参与者。这意味着，如果一个组织希望加入该信道，则需要将包含该组织成员信任链的MSP纳入信道配置中。 否则，来自该组织的身份的交易将被拒绝。

The key difference here between local and channel MSPs is not how they function, but their **scope**.

本地MSP和信道MSP之间的关键区别不在于它们的运作方式，而在于它们的 **范围** 。

.. figure:: membership.diagram.4.png

*Local and channel MSPs. The trust domain (e.g., organization) of each peer is defined by the peer's local MSP, e.g., ORG1 or ORG2. Representation of an organization on a channel is achieved by the inclusion of the organization's MSP in the channel.For example, the channel of this figure is managed by both ORG1 and ORG2. Similar principles apply for the network, orders, and users, but these are not shown here for simplicity*.

*本地MSP和信道MSP。 每个节点的信任域（例如，组织）由节点的本地MSP定义，例如ORG1或ORG2。通过在信道中包含组织的MSP来实现一个组织在信道上的表示。例如，该图的信道同时由ORG1和ORG2管理。类似的原则适用于网络，节点和用户，但为简单起见，此处未显示这些原则*。

**Local MSPs are only defined on the file system of the node or user** to which they apply. Therefore, physically and logically there is only one local MSP per node or user.However, **as channel MSPs are available to all nodes in the channel**, they are logically defined once in the channel in its configuration. However, **a channel MSP is instantiated on the file system of every node in the channel and kept synchronized via consensus**. So while there is a copy of each channel MSP on the local file system of every node, logically a channel MSP resides on and is maintained by the channel or the network.

**本地MSP仅在它们应用的节点或用户的文件系统上被定义**。因此，在物理上和逻辑上，每个节点或用户只有一个本地MSP。但是，**由于信道MSP可用于信道中的所有节点**，因此在其配置上，它们在信道中进行一次逻辑上的定义。然而，**信道MSP在信道中每个节点的文件系统上进行实例化，并通过共识保持同步**。因此，虽然每个节点的本地文件系统上存在每个信道MSP的副本，但逻辑上一个信道MSP驻留在该信道上并由信道或网络维护。

You may find it helpful to see how local and channel MSPs are used by seeing what happens when a blockchain administrator installs and instantiates a smart contract, as shown in the diagram above.

通过查看区块链管理员安装和实例化智能合约时会发生什么，可能会对如何使用本地MSP和信道MSP很有帮助，如上图所示。

An administrator B connects to the peer with an identity issued by RCA1 and stored in their local MSP. When B tries to install a smart contract on the peer, the peer checks its local MSP, ORG1-MSP, to verify that the identity of B is indeed a member of ORG1. A successful verification will allow the install command to complete successfully. Subsequently, B wishes to instantiate the smart contract on the channel. Because this is a channel operation, all organizations in the channel must agree to it. Therefore, the peer must check the MSPs of the channel before it can successfully commits this command. (Other things must happen too, but concentrate on the above for now.)

管理员B使用RCA1发布的身份跟节点连接并存储在其本地MSP中。当B尝试在节点上安装智能合约时，节点检查其本地MSP ，ORG1-MSP，以验证B的身份确实是ORG1的成员。成功验证将允许安装命令成功完成。 随后，B希望在信道上实例化智能合约。由于这是一个信道操作，信道中的所有组织都必须同意该操作。因此，节点必须先检查信道的MSP，然后才能成功提交此命令。 （还有其他事情也必须发生，但现在关注于上述内容。）

One can observe that channel MSPs, just like the channel itself is a **logical construct**. They only become physical once they are instantiated on the local file system of the peers of the channel orgs, and managed by it.

你可以观察到信道MSP，就像信道本身是一个 **逻辑结构**。它们只有在信道组织的节点的本地文件系统上进行实例化后才会变为物理上的，并由它进行管理。


MSP Levels   MSP层
-------------------------------
The split between channel and local MSPs reflects the needs of organizations to administer their local resources, such as a peer or orderer nodes, and their channel resources, such as ledgers, smart contracts, and consortia, which operate at the channel or network level. It's helpful to think of these MSPs as being at different **levels**, with **MSPs at a higher level relating to network administration concerns while MSPs at a lower level handle identity for the administration of private resources**. MSPs are mandatory at every level of administration -- they must be defined for the network, channel, peer, orderer and users.

信道MSP和本地MSP之间的分离反映了组织管理其本地资源（例如节点或背书节点）及其信道资源（例如在信道或网络级别运营的分类帐，智能合约和联盟）的需求。将这些MSP视为处于不同 **层次** 是有帮助的，**MSP处于与网络管理问题相关的更高级别，而较低级别的MSP处理私有资源管理的身份**。MSP在每个管理层次都是必需的 -必须为网络，信道，节点，背书节点和用户定义MSP。

.. figure:: membership.diagram.2.png

*MSP Levels. The MSPs for the peer and orderer are local, whereas the MSPs for a channel (including the network configuration channel) are shared across all participants of that channel. In this figure, the network configuration channel is administered by ORG1, but another application channel can be managed by ORG1 and ORG2.The peer is a member of and managed by ORG2, whereas ORG1 manages the order of the figure. ORG1 trusts identities from RCA1, whereas ORG2 trusts identities from RCA2. Note that these are administration identities, reflecting who can administer these components. So while ORG1 administers the network, ORG2.MSP does exist in the network definition*.

*MSP层。 节点和背书节点的MSP是本地的，而信道的MSP（包括网络配置信道）在该信道的所有参与者之间共享。 在此图中，网络配置信道由ORG1管理，但另一个应用程序信道可由ORG1和ORG2管理。节点是ORG2的成员并被ORG2管理，而ORG1管理图中的背书节点。 ORG1信任来自RCA1的身份，而ORG2信任来自RCA2的身份。请注意，这些是管理身份，反映了谁可以管理这些组件。 因此，尽管ORG1管理网络，但ORG2.MSP确实存在于网络定义中*。

**Network MSP**: The configuration of a network defines who are the members in the network --- by defining the participant organizations MSPs --- as well as which of these members are authorized to perform administrative tasks (e.g., creating a channel).

**网络MSP**：网络的配置通过定义参与者组织的MSP以及哪些成员被授权执行管理任务（例如，创建信道）来定义谁是网络中的成员。

**Channel MSP**: It is important for a channel to maintain the MSPs of its members separately. A channel provides private communications between a particular set of organizations which in turn have administrative control over it. Channel policies interpreted in the context of that channel's MSPs define who has ability to participate in certain action on the channel, e.g., adding organizations, or instantiating chaincodes. Note that there is no necessary relationship between the permission to administrate a channel and the ability to administrate the network configuration channel (or any other channel). Administrative rights exist within the scope of what is being administrated (unless the rules have been written otherwise -- see the discussion of the ROLE attribute below).

**信道MSP**：信道必须能够单独维护其成员的MSP。信道在特定的一组组织之间提供私人通信，而这些组织又反过来对其进行管理控制。在该信道的MSP的环境中解释的信道策略定义谁有能力参与信道上的某些活动，例如，添加组织或实例化链代码。请注意，管理一个信道的权限与管理该网络配置信道（或任何其他信道）的能力之间没有必然的关系。管理的权限存在于管理范围内（除非规则已经另行编写 - 请参阅下面对ROLE属性的讨论）。

**Peer MSP**: This local MSP is defined on the file system of each peer and there is a single MSP instance for each peer. Conceptually, it performs exactly the same function as channel MSPs with the restriction that it only applies to the peer where it is defined. An example of an action whose authorization is evaluated using the peer's local MSP is the installation of a chaincode on the peer premise.

**节点MSP**：在每个节点的文件系统上定义本地MSP，并且每个节点都有一个MSP实例。 从概念上讲，它执行与信道MSP完全相同的功能，约束是它仅适用于定义它的节点。 使用节点的本地MSP评估其授权的操作的一个示例是在节点前提下安装链代码。

**Order MSP**: Like a peer MSP, an order local MSP is also defined on the file system of the node and only applies to that node. Like peer nodes, orders are also owned by a single organization and therefore have a single MSP to list the actors or nodes it trusts.

**背书节点MSP**：与节点MSP一样，背书节点本地MSP也在节点的文件系统上定义并且仅适用于该节点。 与节点一样，背书节点也由单个组织拥有，因此只有一个MSP来列出它信任的参与者或节点。


MSP Structure   MSP结构
-------------------------------
So far, you've seen that the two most important elements of an MSP are the specification of the (root or intermediate) CAs that are used to used to establish an actor's or node's membership in the respective organization. There are, however, more elements that are used in conjunction with these two to assist with membership functions.

到目前为止，您已经看到一个MSP的两个最重要的元素是（根或中间）CA的格式，用于在相应的组织中建立参与者或节点的成员资格。但是，有更多的元素与这两个元素结合使用以协助成员身份的功能。

.. figure:: membership.diagram.5.png

*The figure above shows how a local MSP is stored on a local file system. Even though channel MSPs are not physically structured in exactly this way, it's still a helpful way to think about them*.

*上图显示了本地MSP如何存储在本地文件系统中。尽管信道MSP的物理结构并非如此，但这仍然是一种有用的方式来思考它们*。

As you can see, there are nine elements to an MSP. It's easiest to think of these elements in a directory structure, where the MSP name is the root folder name with each subfolder representing different elements of an MSP configuration.

如你所见，一个MSP有九个元素。最简单的方法是在目录结构中考虑这些元素，其中MSP名称是根文件夹名称，每个子文件夹代表MSP配置的不同元素。

Let's describe these folders in a little more detail and see why they are important.

让我们更详细地描述这些文件夹，来看看为什么它们很重要。

**Root CAs**: This folder contains a list of self-signed X.509 certificates of the Root CAs trusted by the organization represented by this MSP. There must be at least one Root CA X.509 certificate in this MSP folder.

**根CA**：此文件夹包含该MSP所表示的组织的信任的根CA的自签名X.509证书列表。 此MSP文件夹中必须至少有一个Root CA X.509证书。

This is the most important folder because it identifies the CAs from which all other certificates must be derived to be considered members of the corresponding organization.

这是最重要的文件夹，因为它标识CA，从该CA中若想被视为相应组织的成员，必须派生所有其他的证书.

**Intermediate CAs**: This folder contains a list of X.509 certificates of the Intermediate CAs trusted by this organization. Each certificate must be signed by one of the Root CAs in the MSP or by an Intermediate CA whose issuing CA chain ultimately leads back to a trusted Root CA.

**中间CA**：此文件夹包含此组织信任的中间CA的X.509证书列表。 每个证书必须由MSP中的一个根CA或中间CA签名，中间CA颁发的CA链最终会返回到受信任的根CA.

Intuitively to see the use of an intermediate CA with respect to the corresponding organization's structure, an intermediate CA may represent a different subdivision of the organization, or the organization itself (e.g., if a commercial CA is leveraged for the organization's identity management). In the latter case other intermediate CAs, lower in the CA hierarchy can be used to represent organization subdivisions. Here you may find more information on best practices for MSP configuration. Notice, that it is possible to have a functioning network that does not have any Intermediate CA, in which case this folder would be empty.

直观地看到相对于相应组织的结构使用中间CA，中间CA可以表示组织或组织本身的不同细分（例如，如果一个商业CA用于组织的身份管理）。在后一种情况下，CA层次结构中较低的其他中间CA可用于表示组织的细分。在这里，您可以找到有关MSP配置的最佳实践的更多信息。请注意，可以使一个功能正常的网络没有任何中间CA，在这种情况下，此文件夹将为空。

Like the Root CA folder, this folder defines the CAs from which certificates must be issued to be considered members of the organization.

与根CA文件夹一样，此文件夹定义了必须颁发证书才能被视为组织成员的CA.

**Organizational Units (OUs)**: These are listed in the $FABRIC_CFG_PATH/msp/config.yaml file and contain a list of organizational units, whose members are considered to be part of the organization represented by this MSP. This is particularly useful when you want to restrict the members of an organization to the ones holding an identity (signed by one of MSP designated CAs) with a specific OU in it.

**组织单位（OU）**：这些单位列在$ FABRIC_CFG_PATH / msp / config.yaml文件中，并包含一个组织单位的列表，其成员被视为该MSP所代表的组织的一部分。 当您希望将组织成员限制为拥有其中包含特定OU的身份（由MSP指定的CA之一签名）的成员时，此功能尤其有用。

Specifying OUs is optional. If no OUs are listed, all the identities that are part of an MSP -- as identified by the Root CA and Intermediate CA folders -- will be considered members of the organization.

特定OU是可选的。 如果未列出任何OU，则作为MSP一部分的所有身份（由根CA和中间CA文件夹标识）将被视为组织的成员。

**Administrators**: This folder contains a list of identities that define the actors who have the role of administrators for this organization. For the standard MSP type, there should be one or more X.509 certificates in this list.

管理员：此文件夹包含一个标识列表，用于定义具有此组织管理员角色的参与者。 对于标准MSP类型，此列表中应该有一个或多个X.509证书。

It's worth noting that just because a actor has the role of an administrator it doesn't mean that they can administer particular resources! 

值得注意的是，仅仅因为一个参与者具有管理员的角色，并不意味着他们可以管理特定的资源！

The actual power a given identity has with respect to administering the system, is determined by the policies that manage system resources. For example, a channel policy might specify that ORG1-MANUFACTURINGadministrators have the rights to add new organizations to the channel, whereas the ORG1-DISTRIBUTION administrators have no such rights.

给定身份在管理系统方面的实际功效由管理系统资源的策略决定。例如，信道策略可能指定ORG1-MANUFACTURING管理员有权将新组织添加到信道，而ORG1-DISTRIBUTION管理员则没有此类权限。

Even though an X.509 certificate has a ROLE attribute (specifying, for example, that a actor is an admin), this refers to a actor's role within its organization rather than on the blockchain network. This is similar to the purpose of the OU attribute, which -- if it has been defined -- refers to a actor's place in the organization.

即使X.509证书具有ROLE属性（例如，指定某个参与者是管理员），这也是指其在组织内而不是在区块链网络中的角色。这类似于OU属性的目的，如果已经定义，则指的是组织中参与者的位置。

The ROLE attribute can be used to confer administrative rights at the channel level if the policy for that channel has been written to allow any administrator from an organization (or certain organizations) permission to perform certain channel functions (such as instantiating chaincode). In this way, an organization role can confer a network role. This is conceptually similar to how having a driver's license issued by the US state of Florida entitles someone to drive in every state in the US.

如果已编写该信道的策略以允许组织（或某些组织）的任何管理员执行某些信道功能（例如实例化链代码），则ROLE属性可用于在信道层授予管理权限。 通过这种方式，组织角色可以赋予网络角色。 这在概念上类似于美国佛罗里达州颁发的驾驶执照如何授权某人在美国的每个州开车。

**Revoked Certificates**: If the identity of a actor has been revoked, identifying information about the identity -- not the identity itself -- is held in this folder. For X.509-based identities, these identifiers are pairs of strings known as Subject Key Identifier (SKI) and Authority Access Identifier (AKI), and are checked whenever the X.509 certificate is being used to make sure the certificate has not been revoked.

**撤销证书**：如果参与者的身份已被撤销，则在此文件夹中保存有关身份的信息 - 而不是身份本身。 对于基于X.509的身份，这些身份是称为主题密钥标识符（SKI）和授权访问标识符（AKI）的字符串对，并且每当使用X.509证书来确认证书尚未被撤销时，就会对其进行检查。

This list is conceptually the same as a CA's Certificate Revocation List (CRL), but it also relates to revocation of membership from the organization. As a result, the administrator of an MSP, local or channel, can quickly revoke a actor or node from an organization by advertising the updated CRL of the CA the revoked certificate as issued by. This "list of lists" is optional. It will only become populated as certificates are revoked.

此列表在概念上与CA的证书吊销列表（CRL）相同，但它也与从组织中撤消成员身份有关。 因此，MSP的管理员（本地或信道）可以通过向CA发布的已撤销证书来发布CA更新的CRL来快速撤销组织中的参与者或节点。 这个是可选的。 它只会在证书被撤销时填充。

**Node Identity**: This folder contains the identity of the node, i.e., cryptographic material that --- in combination to the content of KeyStore -- would allow the node to authenticate itself in the messages that is sends to other participants of its channels and network. For X.509 based identities, this folder contains an **X.509 certificate**. This is the certificate a peer places in a transaction proposal response, for example, to indicate that the peer has endorsed it -- which can subsequently be checked against the resulting transaction's endorsement policy at validation time.

**节点标识**：此文件夹包含节点的标识，即，与KeyStore组合的加密材料将允许节点在发送给其信道和网络的其他参与者的消息中对自身进行身份验证。 对于基于X.509的标识，此文件夹包含 **X.509证书**。 这是节点在交易提议响应中放置的证书，例如，用于指示节点已经认可它 - 随后可以在可验证时间内对结果交易的认可策略进行检查。

This folder is mandatory for local MSPs, and there must be exactly one X.509 certificate for the node. It is not used for channel MSPs.

此文件夹对于本地MSP是必需的，并且该节点必须只有一个X.509证书。 它不用于信道MSP。

**KeyStore for Private Key**: This folder is defined for the local MSP of a peer or orderer node (or in an client's local MSP), and contains the node's **signing key**. This key matches cryptographically the node's identity included in Node Identity folder and is used to sign data -- for example to sign a transaction proposal response, as part of the endorsement phase.

**私钥的KeyStore**：此文件夹是为节点或背书节点的本地MSP（或客户端的本地MSP）定义的，包含节点的 **签名密钥**。 此密钥以加密方式匹配在节点标识文件夹中包含的节点标识，并用于签署数据 - 例如签署交易提议响应，作为认可阶段的一部分。

This folder is mandatory for local MSPs, and must contain exactly one private key. Obviously, access to this folder must be limited only to the identities, users who have administrative responsibility on the peer.

此文件夹对于本地MSP是必需的，并且必须只包含一个私钥。 显然，对此文件夹的访问权限必须仅限于节点上具有管理职责的用户身份。

Configuration of a **channel MSPs** does not include this part, as channel MSPs aim to offer solely identity validation functionalities, and not signing abilities.

**信道** MSP的配置不包括此部分，因为信道MSP旨在仅提供身份验证功能，而不是签名功能。

**TLS Root CA**: This folder contains a list of self-signed X.509 certificates of the Root CAs trusted by this organization **for TLS communications**. An example of a TLS communication would be when a peer needs to connect to an orderer so that it can receive ledger updates.

**TLS根CA**：此文件夹包含此组织信任的用于 **TLS通信** 的根CA的自签名X.509证书列表。 TLS通信的一个示例是当节点需要连接到背书节点以便它可以接收分类帐更新时。

MSP TLS information relates to the nodes inside the network, i.e., the peers and the orderers, rather than those that consume the network -- applications and administrators.

MSP TLS信息涉及网络内的节点，即节点和背书节点，而不是那些消耗网络的节点 - 应用程序和管理员。

There must be at least one TLS Root CA X.509 certificate in this folder.

此文件夹中必须至少有一个TLS根CA X.509证书。

**TLS Intermediate CA**: This folder contains a list intermediate CA certificates CAs trusted by the organization represented by this MSP for TLS communications. This folder is specifically useful when commercial CAs are used for TLS certificates of an organization. Similar to membership intermediate CAs, specifying intermediate TLS CAs is optional.

**TLS中间CA**：该文件夹包含一个列表，中间CA证书被此MSP所代表的组织所信任，并用于TLS通信。当商业CA用于一个组织的TLS证书时，此文件夹特别有用。与成员中间CA类似，中间TLS CA是可选的。

For more information on TLS, click here.

有关TLS的更多信息，请单击此处。

If you've read this doc as well as our doc on Identity), you should have a pretty good grasp of how identities and membership work in Hyperledger Fabric. You've seen how a PKI and MSPs are used to identify the actors collaborating in a blockchain network. You've learned how certificates, public/private keys, and roots of trust work, in addition to how MSPs are physically and logically structured.

如果您已经阅读过本文档以及我们的身份文档，那么您应该非常了解Hyperledger Fabric中的身份和成员身份。 您已经了解了如何使用PKI和MSP来识别在区块链网络中协作的参与者。 除了MSP的物理和逻辑结构之外，您还了解了证书，公钥/私钥和信任根的工作原理。




