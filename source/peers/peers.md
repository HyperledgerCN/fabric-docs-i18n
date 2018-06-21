# Peers
# peer节点
A blockchain network is primarily comprised of a set of peer nodes. Peers are a
fundamental element of the network because they host ledgers and smart
contracts. Recall that a ledger immutably records all the transactions generated
by smart contracts. Smart contracts and ledgers are used to encapsulate the
shared *processes* and shared *information* in a network, respectively. These
aspects of a peer make them a good starting point to understand Hyperledger
Fabric network.
一个区块链网络主要由peer节点组成。peer节点是区块链网络的基本元素，因为它们存贮着账本和智能合约。请注意账本记录了由智能合约生成的所有交易，且记录后不可更改。智能合约和账本被分别用来封装一个网络中的共享*进程*和共享*信息*。了解节点的这些特性是我们了解Hyperledger Fabric网络的一个良好开始。

Other elements of the blockchain network are of course important: ledgers and
smart contracts, orderers, policies, channels, applications, organizations,
identities, and membership and you can read more about them in their own
dedicated topics. This topic focusses on peers, and their relationship to those
other elements in a Hyperledger Fabric blockchain network.
当然，区块链网络中的其他组成元素同样重要：账本和智能合约，排序节点，策略，通道，应用，组织（机制），身份，以及成员资格，你能在这些元素的专题中读到更多关于它们的信息。本专题主要关注peer节点及其与超级账本区块链网络中其他元素的关系。

![Peer1](./peers.diagram.1.png)

*A blockchain network is formed from peer nodes, each of which can hold copies
of ledgers and copies of smart contracts. In this example, the network N is
formed by peers P1, P2 and P3. P1, P2 and P3 each maintain their own instance
of the ledger L1. P1, P2 and P3 use chaincode S1 to access their copy of
the ledger L1.*
*一个区块链网络由peer节点构成，每个节点都保存账本和智能合约的副本。在本例中，节点P1，
P2和P3组成了网络N，且每个节点都各自保存了账本L1的实例。三个peer节点使用链码来访问它们各自的账本L1副本
。*

Peers can be created, started, stopped, reconfigured, and even deleted. They
expose a set of APIs that enable administrators and applications to interact
with the services that they provide. We'll learn more about these services in
this topic.
peer节点可以被创建，激活，终止，重新配置，终止甚至删除。节点对外有一组应用程序编程接口，这些接口
使管理员和应用程序能够与它们提供的服务交互。在本专题中我们会学习更多有关这些服务的知识。

### A word on terminology
### 一个专业术语
Hyperledger Fabric implements smart contracts with a technology concept it calls
**chaincode** -- simply a piece of code that accesses the ledger, written in one
of the supported programming languages. In this topic, we'll usually use the
term **chaincode**, but feel free to read it as **smart contract** if you're
more used to this term. It's the same thing!

Hyperledger Fabric通过一种称为**链码**的技术来执行智能合约，链码简单说就是以某种Fabric支持的编程
语言编写的一段与账本连接的代码。在本专题中，我们会很频繁地用到链码这个概念，不过若你对**智能
合约**比较熟悉，你也可以自由地将前者当作后者。因为它们之间并无区别！

## Ledgers and Chaincode
##账本与链码

Let's look at a peer in a little more detail. We can see that it's the peer that
hosts both the ledger and chaincode. More accurately, the peer actually hosts
*instances* of the ledger, and *instances* of chaincode. Note that this provides
a deliberate redundancy in a Fabric network -- it avoids single points of
failure. We'll learn more about the distributed and decentralized nature of a
blockchain network later in this topic.
让我们更详细地研究一下peer节点。我们可以看到它是一种保存保存了账本和链码的节点。更准确地说，
peer节点实际上保存保存的是账本和链码的*实例*。注意这在Fabric网络中提供了有意的冗余以避免单点故障。
在本专题接下来的部分，我们会学到更多关于区块链网络分布式和去中心化的特性。

![Peer2](./peers.diagram.2.png)

*A peer hosts instances of ledgers and instances of chaincodes. In this example,
P1 hosts an instance of ledger L1 and an instance of chaincode S1. There
can be many ledgers and chaincodes hosted on an individual peer.*
*一个peer节点保存了账本和链码的实例。在本例中，节点P1保存了账本L1和链码S1的各一个实例。
一个独立节点其实可以保存多个账本和链码。*

Because a peer is a *host* for ledgers and chaincodes, applications and
administrators must interact with a peer if they want to access these resources.
That's why peers are considered the most fundamental building blocks of a
Hyperledger Fabric blockchain network. When a peer is first created, it has
neither ledgers nor chaincodes. We'll see later how ledgers get created,
and chaincodes get installed, on peers.
由于peer节点*保存*了账本和链码，所以如果应用程序和管理员想访问这些资源，他们就必须和节点交互。
这也是为什么节点被看作组成Hyperledger Fabric区块链网络中区块的最基本元素。一个节点在创建之初
并不保存账本或链码。稍后我们会看到节点上的账本如何被创建以及链码如何被安装。

### Multiple Ledgers
### 多个账本

A peer is able to host more than one ledger, which is helpful because it allows
for a flexible system design. The simplest peer configuration is to have a
single ledger, but it's absolutely appropriate for a peer to host two or more
ledgers when required.
一个peer节点可以保存多个账本，这是十分有用的，因为这允许了灵活的系统设计。最简单的节点配置
是一个节点保存一个账本，但是，当有需要时，一个peer节点保存两个或多个账本是绝对合理的。

![Peer3](./peers.diagram.3.png)

*A peer hosting multiple ledgers. Peers host one or more ledgers, and each
ledger has zero or more chaincodes that apply to them. In this example, we
can see that the peer P1 hosts ledgers L1 and L2. Ledger L1 is accessed using
chaincode S1. Ledger L2 on the other hand can be accessed using chaincodes S1 and S2.*
*保存多个账本的节点。节点保存一个或多个账本，且每个账本具有适用于它们的零个或多个链码。
在本例中，我们能发现节点P1保存了账本L1和L2。使用链码S1可以访问账本L1，另一方面使用链码S1和S2
可以访问账本L2。*

Although it is perfectly possible for a peer to host a ledger instance without
hosting any chaincodes which access it, it's very rare that peers are configured
this way. The vast majority of peers will have at least one chaincode installed
on it which can query or update the peer's ledger instances. It's worth
mentioning in passing whether or not users have installed chaincodes for use by
external applications, peers also have special **system chaincodes** that are
always present. These are not discussed in detail in this topic.
尽管即使不保存访问账本的链码，一个peer节点依然完全有可能保存一个账本的实例，却很少有节点以
这种方式配置。绝大多数节点上都会至少安装一个链码，这个链码可以查询或更新节点账本的实例。值得一提的是，
通过用户是否通过外部应用程序安装了链码以供使用，节点上也总存在特殊的**系统链码**。这些在本专题
中不再详细讨论。

### Multiple Chaincodes
###多个链码

There isn't a fixed relationship between the number of ledgers a peer has and
the number of chaincodes that can access that ledger. A peer might have
many chaincodes and many ledgers available to it.
一个peer节点所保存的账本数目与能访问该账本的链码数目并无固定关系。一个peer节点可能有多个链码和
多个能访问的账本。

![Peer4](./peers.diagram.4.png)

*An example of a peer hosting multiple chaincodes. Each ledger can have
many chaincodes which access it. In this example, we can see that peer P1
hosts ledgers L1 and L2. L1 is accessed by chaincodes S1 and S2, whereas
L2 is accessed by S3 and S1. We can see that S1 can access both L1 and L2.*
*单个peer节点保存多个链码的例子。每个账本都能有多个能对其进行访问的链码。在本例中，
我们能看到节点P1保存了账本L1和L2。L1由链码S1和S2访问，而L2由链码S3和S1访问。我们
能看到S1既能访问L1也能访问L2。*

We'll see a little later why the concept of **channels** in Hyperldeger Fabric
is important when hosting multiple ledgers or multiple chaincodes on a
peer.
稍后我们会看到在一个peer节点保存多个账本或多个链码的情况下，通道这个概念在
Hyperledger Fabric中的重要性。

## Applications and Peers
##应用程序和节点

We're now going to show how applications interact with peers to access the
ledger. Ledger-query interactions involve a simple three step dialogue between
an application and a peer; ledger-update interactions are a little more
involved, and require two extra steps. We've simplified these steps a little to
help you get started with Hyperledger Fabric, but don't worry -- what's most
important to understand is the difference in application-peer interactions for
ledger-query compared to ledger-update transaction styles.
现在我们要展示应用程序如何通过与peer节点交互来实现访问账本。账本查询交互涉及应用程序和
peer节点间简单的三步会话；账本更新交互与之联系更加密切，它还要求额外的两步。我们已经对这些
步骤稍作简化以便你开始了解Hyperledger Fabric，不过请勿担心——最有必要理解的是程序—节点交互在执行账本查询时相比于账本更新时呈现的差异。

Applications always connect to peers when they need to access ledgers and
chaincodes. The Hyperledger Fabric Software Development Kit (SDK) makes this
easy for programmers -- its APIs enable applications to connect to peers, invoke
chaincodes to generate transactions, submit transactions to the network that
will get ordered and committed to the distributed ledger, and receive events
when this process is complete.
当需要访问账本和链码时，应用程序都会连接peer节点。Hyperledger Fabric软件开发工具包使其对
程序员而言更加容易——它的应用编程接口使应用程序能连接到节点，调用链码来生成交易，并将交易
上传到网络中进行排序然后提交到分布式账本中，并且在处理完成时接收事件。

Through a peer connection, applications can execute chaincodes to query or
update the ledger. The result of a ledger query transaction is returned
immediately, whereas ledger updates involve a more complex interaction between
applications, peers, and orderers. Let's investigate in a little more detail.
通过与节点的连接，应用程序可以执行链码以查询或更新账本。账本查询交易的结果会立即反馈，
而账本更新则涉及应用程序，peer节点和排序节点之间更复杂的交互，让我们更详细地研究一下。

![Peer6](./peers.diagram.6.png)

*Peers, in conjunction with orderers, ensure that the ledger is kept up-to-date
on every peer. In this example application A connects to P1 and invokes
chaincode S1 to query or update the ledger L1. P1 invokes S1 to generate a
proposal response that contains a query result or a proposed ledger update.
Application A receives the proposal response, and for queries
the process is now complete. For updates, A builds a transaction
from the all the responses, which it sends it to O1 for ordering. O1 collects
transactions from across the network into blocks, and distributes these to all
peers, including P1. P1 validates the transaction before applying to L1. Once L1
is updated, P1 generates an event, received by A, to signify completion.*
*peer节点与排序节点相连，保证每一个peer节点上的账本都是最新的。在本例中应用程序A连接
peer节点P1且调用链码S1以查询或更新账本L1。P1调用S1来生成一个包含了查询结果或提议
账本更新的提案响应。应用程序A接收该响应，此时查询过程便完成了。为了执行更新，程序A从所有它发送到排序节点O1以进行排序的响应中建立交易。排序节点O1收集整个网络中的交易并写入区块，并将
它们分发到包括P1在内的所有peer节点上。P1在将其应用到L1之前先进行验证。一旦L1更新完成，P1就
生成一个事件，由A来接收以表示完成。*

A peer can return the results of a query to an application immediately because
all the information required to satisfy the query is in the peer's local copy of
the ledger. Peers do not consult with other peers in order to return a query to
an application. Applications can, however, connect to one or more peers to issue
a query -- for example to corroborate a result between multiple peers, or
retrieve a more up-to-date result from a different peer if there's a suspicion
that information might be out of date. In the diagram, you can see that ledger
query is a simple three step process.
一个peer节点可以将查询的结果立即反馈给应用程序，因为完成查询所需要的全部信息都在peer节点
本地的账本副本中。peer节点之间不需要相互协商来将查询反馈给应用程序。但是，应用程序可以连接
一个或多个peer节点来提出查询——比如要确认多个peer节点间的查询结果，或者若怀疑信息可能过期可以
从另一个peer节点恢复最新的结果。在该图中，你能发现账本查询是简单的三个步骤。

An update transaction starts in the same way as a query transaction, but has two
extra steps. Although ledger-updating applications also connect to peers to
invoke a chaincode, unlike with ledger-querying applications, an individual peer
cannot perform a ledger update at this time, because other peers must first
agree to the change -- a process called **consensus**. Therefore, peers return
to the application a **proposed** update -- one that this peer would apply
subject to other peers' prior agreement. The first extra step -- four --
requires that applications send an appropriate set of matching proposed updates
to the entire network of peers as a transaction for commitment to their
respective ledgers. This is achieved by the application using an **orderer** to
package transactions into blocks, and distribute them to the entire network of
peers, where they can be verified before being applied to each peer's local copy
of the ledger. As this whole ordering processing takes some time to complete
(seconds), the application is notified asynchronously, as shown in step five.
一个更新事件的开始方式和查询事件一样，但还有额外的两步。尽管账本更新程序也连接peer节点以调用
链码，但与账本查询程序不同，单个peer节点此时不能执行账本更新，因为其他节点必须首先认同
账本的改变——即一个称为**共识**的过程。所以，peer节点为应用程序反馈
**提议**的更新——该更新将
被该节点加入其他节点先前的认同中。第一个额外步骤——four——要求应用程序向整个节点网络发送
一组合适的匹配提议更新，作为提交到各个节点各自账本的交易。该过程通过应用程序使用**排序节点**将交易打包成区块，
并将它们分发到整个节点网络上来完成，网络中的这些交易在被加入到每个节点的账本副本前会
被验证。由于整个排序进程需要耗费一些时间来完成（几秒），故该程序是异步通知的，如第五步所示。

Later in this topic, you'll learn more about the detailed nature of of this
ordering process -- and for a really detailed look at this process see the
[Transaction Flow](../txflow.html) topic.
在本专题后面的部分中，你会学习更多关于排序过程的详细原理——若要了解该进程更加详细的内容，
参见专题交易流程。
## Peers and Channels

Although this topic is about peers rather than channels, it's worth spending a
little time understanding how peers interact with each other, and applications,
via *channels* -- a mechanism by which a set of components within a blockchain
network can communicate and transact *privately*.
尽管本专题更多涉及的是peer节点而非通道，我们也值得花一点时间来了解各peer节点之间、peer节点与应
用程序之间是如何通过*通道*实现交互的——这是一种区块链网络中一组组成元素之间可以*私下*
交流与交易的机制。

These components are typically peer nodes, orderer nodes, and applications, and
by joining a channel they agree to come together to collectively share and
manage identical copies of the ledger for that channel. Conceptually you can
think of channels as being similar to groups of friends (though the members of a
channel certainly don't need to be friends!). A person might have several groups
of friends, with each group having activities they do together. These groups
might be totally separate (a group of work friends as compared to a group of
hobby friends), or there can be crossover between them. Nevertheless each group
is its own entity, with "rules" of a kind.
这些组成元素主要有peer节点，排序节点和应用程序，通过加入同一通道，上述组成元素可以在通道
内共享和管理相同的账本副本。从概念上你可以把通道认为是一群朋友的集合（尽管通道内的成员不
需要是朋友）。一个人可能有好几群朋友，每一群的朋友都有他们的集体活动。这些不同的朋友圈子可能完
全不相干（如一群工作伙伴相比于一群有共同爱好的朋友），或者他们之间也可以有交集。然而每一群
都各自为一个实体，有自己的一套“规则”。

![Peer5](./peers.diagram.5.png)

*Channels allow a specific set of peers and applications to communicate with
each other within a blockchain network. In this example, application A can
communicate directly with peers P1 and P2 using channel C. You can think of the
channel as a pathway for communications between particular applications and
peers. (For simplicity, orderers are not shown in this diagram, but must be
present in a functioning network.)*
*通道允许一组特定的peer节点和应用程序在区块链网络中互相交流。在本例中，程序A可以通过通道C
直接和节点P1和P2交流。你可以把通道认为是特定的节点和程序之间交流的途径（简单起见，
本图并未画出排序节点，但其在一个功能网络中必须出现。）*

We see that channels don't exist in the same way that peers do -- it's more
appropriate to think of a channel as a logical structure that is formed by a
collection of physical peers. *It is vital to understand this point -- peers
provide the control point for access to, and management of, channels*.
我们看到通道的存在方式与peer节点不同——将通道认为是由一组物理节点组成的逻辑结构更恰当。
*明白这一点至关重要——peer节点提供了访问和管理通道的控制点。*

## Peers and Organizations
##peer节点与组织

Now that you understand peers and their relationship to ledgers, chaincodes
and channels, you'll be able to see how multiple organizations come together to
form a blockchain network.
现在你明白了peer节点及其与账本、链码和通道的关系，你能看到多个组织怎样共同组成一个区块链网络。

Blockchain networks are administered by a collection of organizations rather
than a single organization. Peers are central to how this kind of distributed
network is built because they are owned by -- and are the connection points to
the network for -- these organizations.
区块链网络由多个而非单个组织管理。peer节点对于这种分布式网络的建立起着核心作用，因为它们是
由这些组织拥有的，并且是这些组织的网络连接点。

![Peer8](./peers.diagram.8.png)

*Peers in a blockchain network with multiple organizations. The blockchain
network is built up from the peers owned and contributed by the different
organizations. In this example, we see four organizations contributing eight
peers to form a network. The channel C connects five of these peers in the
network N -- P1, P3, P5, P7 and P8. The other peers owned by these
organizations have not been joined to this channel, but are typically joined to
at least one other channel. Applications that have been developed by a
particular organization will connect to their own organization's peers as well
as those of different organizations. Again,for simplicity, an orderer node is not 
shown in this diagram.*
*区块链网络中与多个组织相连的peer节点。不同组织拥有并贡献的peer节点建立了区块链网络。在本例中，
我们看到四个组织贡献了八个peer节点组成一个网络。在网络N中，通道C连接了这八个节点中的五个——P1、
P3、P5、P7和P8。这些组织拥有的其他节点并未接入该信道，但通常都接入至少一个其他通道。某个组织
开发的应用程序会与本组织和其他组织的节点相连。重申一遍，简单起见，本图未展示排序节点。*


It's really important that you can see what's happening in the formation of a
blockchain network. *The network is both formed and managed by the multiple
organizations who contribute resources to it.* Peers are the resources that
we're discussing in this topic, but the resources an organization provides are
more than just peers. There's a principle at work here -- the network literally
does not exist without organizations contributing their individual resources to
the collective network. Moreover, the network grows and shrinks with the
resources that are provided by these collaborating organizations.
重要的是你能看到在该区块链网络结构中发生了什么。*网络是由贡献资源的多个组织组成并管理的。*
本专题中我们讨论的资源即是peer节点，但组织提供的资源远非于此。可运行的区块链网络有一个原
则——如果各成员组织没有向这个集体网络贡献他们的个体资源，此网络原则上就不存在了。此外，网络也随着这些协作组织
提供的资源扩大和缩小。

You can see that (other than the ordering service) there are no centralized
resources -- in the [example above](#Peer8), the network, **N**, would not exist
if the organizations did not contribute their peers. This reflects the fact that
the network does not exist in any meaningful sense unless and until
organizations contribute the resources that form it. Moreover, the network does
not depend on any individual organization -- it will continue to exist as long
as one organization remains, no matter which other organizations may come and
go. This is at the heart of what it means for a network to be decentralized.
你能看到（除了排序服务外）并没有中心化的资源——在上例中，如果组织不贡献自己的peer节点，
网络**N**不会存在。这反映了这样一个事实，即除非组织贡献出组成
它的资源，否则网络从任何意义上都不存在，。此外，网络不依赖于任何一个组织——只要有一个组织留下，网络就会继续存在，无论其他
组织可能加入或离开。这正是网络去中心化的核心意义所在。

Applications in different organizations, as in the [example above](#Peer8), may
or may not be the same. That's because it's entirely up to an organization how
its applications process their peers' copies of the ledger. This means that both
application and presentation logic may vary from organization to organization
even though their respective peers host exactly the same ledger data.
正如上例所示，不同组织的应用程序，或许一样或许不同。这是因为程序如何处理节点的账本副本完
全取决于组织。这意味着即使不同组织的节点记录了完全相同的账本数据，它们的应用程序和呈现逻
辑也可能不同。

Applications either connect to peers in their organization, or peers in another
organization, depending on the nature of the ledger interaction that's required.
For ledger-query interactions, applications typically connect to their own
organization's peers. For ledger-update interactions, we'll see later why
applications need to connect to peers in every organization that is required to
endorse the ledger update.
应用程序要么连接到自身组织的peer节点，要么连接到其他组织的peer节点，这取决于所需的账本交
互的特性。对于账本查询交互，程序通常会连接到自身组织的节点上。至于账本更新交互，稍后我们
会看到为什么应用程序必须连接到每个需要用来对账本更新背书的组织的节点上。

## Peers and Identity
## peer节点与身份

Now that you've seen how peers from different organizations come together to
form a blockchain network, it's worth spending a few moments understanding how
peers get assigned to organizations by their administrators.
现在你已经看到来自不同组织的peer节点是如何一起组成一个区块链网络的了，我们值得花几分钟
来理解这些节点怎样被管理员分配到各个组织。

Peers have an identity assigned to them via a digital certificate from a
particular certificate authority. You can read lots more about how X.509
digital certificates work elsewhere in this guide, but for now think of a
digital certificate as being like an ID card that provides lots of verifiable
information about a peer. *Each and every peer in the network is assigned a
digital certificate by an administrator from its owning organization*.
peer节点具有通过来自特定证书机构的数字证书分配给它们的身份，在本指南的其他部分你可以
阅读更多关于X.509数字证书如何工作的信息，但现在请将数字证书看作一张提供大量关于节点的
可证实信息的身份证。*网络中的每个peer节点都由管理员从其拥有的组织中分配数字证书 。*
![Peer9](./peers.diagram.9.png)

*When a peer connects to a channel, its digital certificate identifies its
owning organization via a channel MSP. In this example, P1 and P2 have
identities issued by CA1. Channel C determines from a policy in its channel
configuration that identities from CA1 should be associated with Org1 using
ORG1.MSP. Similarly, P3 and P4 are identified by ORG2.MSP as being part of
Org2.*
*当一个节点接入通道时，它的数字证书就通过通道MSP标识了它所属的组织。在本例中，节点P1
和P2具有CA1颁发的身份。通道C根据其通道配置的策略确定来自CA1的身份标识应该使用ORG1.MSP
与Org1关联。相似地，P3和P4由ORG2.MSP认定为Org2的一部分。*

Whenever a peer connects using a channel to a blockchain network, *a policy in
the channel configuration uses the peer's identity to determine its
rights.* The mapping of identity to organization is provided by a component
called a *Membership Service Provider* (MSP) -- it determines how a peer gets
assigned to a specific role in a particular organization and accordingly gains
appropriate access to blockchain resources. Moreover, a peer can only be owned
by a single organization, and is therefore associated with a single MSP. We'll
learn more about peer access control later in this topic, and there's an entire
topic on MSPs and access control policies elsewhere in this guide. But for now,
think of an MSP as providing linkage between an individual identity and a
particular organizational role in a blockchain network.
每当一个节点使用通道接入区块链网络，*通道配置的策略就通过节点身份验证它的权限*。身份到组织
的映射是由叫做*成员资格服务提供者*（MSP）的成分提供的——它决定了peer节点在特定组织中如何被分配
特定角色，并相应地获得区块链资源适当的访问权限，此外，一个节点只能被单个组织拥有，因此与单个MSP关联。本专题的稍后部分我们
将学习更多关于节点访问控制的内容，且本指南的其他部分有一整个关于MSP和接入控制策略的专题。
但现在，请将MSP看作在区块链网络中，为节点身份和特殊组织角色提供连接的工具。

And to digress for a moment, peers as well as *everything that interacts with a
blockchain network acquire their organizational identity from their digital
certificate and an MSP*. Peers, applications, end users, administrators,
orderers must have an identity and an associated MSP if they want to interact
with a blockchain network. *We give a name to every entity that interacts with
a blockchain network using an identity -- a principal.* You can learn lots
more about principals and organizations elsewhere in this guide, but for now
you know more than enough to continue your understanding of peers!
说句题外话，*peer节点和所有其他与区块链网络交互的元素一样，从他们的数字证书和MSP中获取组
织身份。*peer节点、应用程序、终端用户、管理员、排序节点如果要和区块链网络交互，都必须要有一
个身份以及一个关联的MSP。*我们给所有通过身份和区块链网络交互的实体一个名字——主体。*在本指南
的其他部分，你能学到更多关于主体和组织的内容，但现在你所知道的已经超过你继续理解peer节点所需！

Finally, note that it's not really important where the peer is physically
located -- it could reside in the cloud, or in a data centre owned by one
of the organizations, or on a local machine -- it's the identity associated
with it that identifies it as owned by a particular organization. In our
example above, P3 could be hosted in Org1's data center, but as long as the
digital certificate associated with it is issued by CA2, then it's owned by
Org2.
最后，请记住peer节点的物理位置并不重要——它可以存在于云端，或者在某个组织拥有的数据中心中，
亦或是在一个本地机器上——与它绑定的身份证实了它被某一特定的组织拥有。在上面的例子中，P3可
以存储在Org1的数据中心中，但只要与它绑定的数字证书是由CA2颁发的，它就属于Org2。

## Peers and Orderers
## peer节点与排序节点

We've seen that peers form a blockchain network, hosting ledgers and chaincodes
contracts which can be queried and updated by peer-connected applications.
However, the mechanism by which applications and peers interact with each other
to ensure that every peer's ledger is kept consistent is mediated by special
nodes called *orderers*, and it's these nodes to which we now turn our
attention.
我们已经看到peer节点组成区块链网络，保存账本和能被与节点相连的应用程序查询和更新的链码合同。
然而，应用程序和peer节点相互作用以确保每个peer节点的账本保持一致的机制是由一种称为*排序节点*的特
殊节点所调节的，排序节点也正是我们正要关注的。

An update transaction is quite different to a query transaction because a single
peer cannot, on its own, update the ledger -- it requires the consent of other
peers in the network. A peer requires other peers in the network to approve a
ledger update before it can be applied to a peer's local ledger. This process is
called *consensus* -- and takes much longer to complete than a query. But when
all the peers required to approve the transaction do so, and the transaction is
committed to the ledger, peers will notify their connected applications that the
ledger has been updated. You're about to be shown a lot more detail about how
peers and orderers manage the consensus process in this section.
更新交易和查询交易颇有不同，因为单个peer节点无法独立更新账本——这要求网络中其他节点的同意。
在节点为本地账本执行更新之前，它需要网络中其他节点的支持。这个过程称为*共识*——它比查询要多花
很长时间。但是当所有需要的节点支持了该交易的更新，且交易被提交到账本上时，peer节点将通知与之相连的应用
程序账本已更新。本节接下来的部分，你将看到更多关于peer节点和排序节点如何管理共识进程的细节。

Specifically, applications that want to update the ledger are involved in a
3-phase process, which ensures that all the peers in a blockchain network keep
their ledgers consistent with each other. In the first phase, applications work
with a subset of *endorsing peers*, each of which provide an endorsement of the
proposed ledger update to the application, but do not apply the proposed update
to their copy of the ledger. In the second phase, these separate endorsements
are collected together as transactions and packaged into blocks. In the final
phase, these blocks are distributed back to every peer where each transaction is
validated before being applied to that peer's copy of the ledger.
具体而言，要更新账本的应用程序都涉及到一个三阶段的过程，这确保了区块链网络中各peer节点的账本
保持一致。在第一阶段，应用程序与一部分*背书节点*一起工作，且每一个背书节点都为程序提供所提议的账本
更新的背书。但并不将提议的更新应用到账本的副本上。在第二阶段，这些分散的背书将被作为交易
收集起来并打包成区块。在最后一个阶段，这些区块被分发回每个peer节点，每个交易被在被加入
这些节点的账本副本之前都将被验证。

As you will see, orderer nodes are central to this process -- so let's
investigate in a little more detail how applications and peers use orderers to
generate ledger updates that can be consistently applied to a distributed,
replicated ledger.
正如你将见到的，排序节点是这个过程的核心——所以，让我们更详细地研究应用程序和peer节点是
如何使用排序节点来生成账本更新，且这些更新可以一致应用到分布式的复制账本上的。

### Phase 1: Proposal
### 阶段1：提案
Phase 1 of the transaction workflow involves an interaction between an
application and a set of peers -- it does not involve orderers. Phase 1 is only
concerned with an application asking different organizations' endorsing peers to
agree to the results of the proposed chaincode invocation.
交易流程的第一阶段涉及应用程序和一组peer节点之间的交互——并不涉及排序节点。阶段1仅涉及一个
应用程序，该程序请求不同组织的背书节点认同所提议的链码调用的结果。

To start phase 1, applications generate a transaction proposal which they send
to each of the required set of peers for endorsement. Each peer then
independently executes a chaincode using the transaction proposal to
generate a transaction proposal response. It does not apply this update to the
ledger, but rather the peer signs it and returns to the application. Once the
application has received a sufficient number of signed proposal responses,
the first phase of the transaction flow is complete. Let's examine this phase in
a little more detail.
阶段1 开始时，应用程序生成一个交易提案，并将其发送到每组必要的peer节点上请求背书。
每个peer节点独立地使用该提案执行链码以生成提案响应。Peer节点不会将更新应用到账本上，而是对其签名并反馈给应用程序。一旦程序接收到足够数量的签名响应，交易的第一阶段就
完成了。让我们更详细地检查这个阶段。

![Peer10](./peers.diagram.10.png)

*Transaction proposals are independently executed by peers who return endorsed
proposal responses. In this example, application A1 generates transaction T1
proposal P which it sends to both peer P1 and peer P2 on channel C. P1 executes
S1 using transaction T1 proposal P generating transaction T1 response R1 which
it endorses with E1. Independently, P2 executes S1 using transaction T1
proposal P generating transaction T1 response R2 which it endorses with E2.
Application A1 receives two endorsed responses for transaction T1, namely E1
and E2.*
*peer节点独立处理交易提案并反馈背书响应。在本例中，应用程序A1生成交易T1的提案P并在通道
C中将其发送给peer节点P1和P2。P1使用交易T1的提案P执行S1来生成以E1背书的响应R1。相独立地，
P2使用交易T1的提案P执行S1来生成以E2背书的响应R2。应用程序A1接收到两个对交易T1的背书响应，
即E1和E2。*

Initially, a set of peers are chosen by the application to generate a set of
proposed ledger updates. Which peers are chosen by the application? Well, that
depends on the *endorsement policy* (defined for a chaincode), which defines
the set of organizations that need to endorse a proposed ledger change before it
can be accepted by the network. This is literally what it means to achieve
consensus -- every organization who matters must have endorsed the proposed
ledger change *before* it will be accepted onto any peer's ledger.
在一开始，应用程序选择一组peer节点来生成一组提议的账本更新。程序选择哪些节点取决于
*背书策略*（由一段链码定义），它定义了一组组织，这些组织在被网络接受前需要背书一个提议的账本改变。
这就是字面上达成共识的含义——每个重要的组织在被接受到任何peer节点的账本上*之前*，都需要对提议的
账本更新背书。

A peer endorses a proposal response by adding its digital signature, and signing
the entire payload using its private key. This endorsement can be subsequently
used to prove that this organization's peer generated a particular response. In
our example, if peer P1 is owned by organization Org1, endorsement E1
corresponds to a digital proof that "Transaction T1 response R1 on ledger L1 has
been provided by Org1's peer P1!".
一个节点通过添加其数字签名来对提案响应背书，并使用其私钥为整个提案的内容提供签名。该背书可以随后用来
证明这个组织的peer节点产生了特定的响应。在我们的例子中，如果peer节点P1由组织Org1拥有，则
背书节点E1对应一个数字证明，即“账本L1上对交易T1的响应R1已经由Org1的节点P1提供！”。

Phase 1 ends when the application receives signed proposal responses from
sufficient peers. We note that different peers can return different and
therefore inconsistent transaction responses to the application *for the same
transaction proposal*. It might simply be that the result was generated a
different time on different peers with ledgers at different states -- in which
case an application can simply request a more up-to-date proposal response. Less
likely, but much more seriously, results might be different because the chaincode is 
*non-deterministic*. Non-determinism is the enemy of chaincodes and ledgers and if 
it occurs it indicates a serious problem with the proposed transaction, as inconsistent 
results cannot, obviously, be applied to ledgers.An individual peer cannot know that 
their transaction result is non-deterministic -- transaction responses must be gathered
together for comparison before non-determinism can be detected. (Strictly speaking, 
even this is not enough, but we defer this discussion to the transaction topic, where
non-determinism is discussed in detail.)
当应用程序从足够数量的peer节点接收已签名的提案响应时，阶段1 结束。我们注意到，不同的节点可以有
不同的反馈，所以*对于同一交易提案*，会有不一致的交易响应反馈给程序。简单地说是因为该结果是账本处于不同状态下，不同节点在不同时间产生的
——这种情况下，应用程序可以简单地请求一个最新的提议响应。
结果不同的原因也可能是因为链码是*非确定性*的，尽管这不太可能，但却更加严重。非确定性是链码和账本的威胁，如果发
生则意味着所提出的交易存在严重的问题，因为不一致的结果显然不能应用到账本上。单个peer节点不能知
道他们的交易结果是非确定性的——在非确定性能被检测到之前，交易响应必须被收集起来作比较。（严格说，
这还不够，不过我们将这个讨论推迟到交易专题上，届时我们将对非确定性做更详细的讨论）。

At the end of phase 1, the application is free to discard inconsistent
transaction responses if it wishes to do so, effectively terminating the
transaction workflow early. We'll see later that if an application tries to use
an inconsistent set of transaction responses to update the ledger, it will be
rejected.
在阶段1末尾，应用程序可以自由丢弃不一致的交易响应，尽早有效地终止交易流程。稍后我们
会看到，如果应用程序试图用一组不一致的交易响应来更新账本，它将被拒绝。

### Phase 2: Packaging
### 阶段2：打包

The second phase of the transaction workflow is the packaging phase. The orderer
is pivotal to this process -- it receives transactions containing endorsed
transaction proposal responses from many applications. It orders each
transaction relative to other transactions, and packages batches of transactions
into blocks ready for distribution back to all peers connected to the orderer,
including the original endorsing peers.
交易流程的第二阶段是打包。排序节点对该过程至关重要——它负责接收交易，这些交易包含着来自
许多应用程序的已背书的提案响应。它给每一个交易排序，并将这些交易打包
成区块，为分发回与排序节点相连的所有节点做准备，包括初始的背书节点。

![Peer11](./peers.diagram.11.png)

*The first role of an orderer node is to package proposed ledger updates. In
this example, application A1 sends a transaction T1 endorsed by E1 and E2 to
the orderer O1. In parallel, Application A2 sends transaction T2 endorsed by E1
to the orderer O1. O1 packages transaction T1 from application A1 and
transaction T2 from application A2 together with other transactions from other
applications in the network into block B2. We can see that in B2, the
transaction order is T1,T2,T3,T4,T6,T5 -- which may not be the order in which
these transactions arrived at the orderer node! (This example shows a very
simplified orderer configuration.)*
*排序节点扮演的第一个角色就是打包提议的账本更新。在本例中，应用程序A1向排序节点01发送由
E1和E2背书的交易T1，相应地，程序A2向排序节点01发送由E1背书的交易T2，O1将来自程序A1的交易T1和
来自程序A2的交易T2与来自网络中的其他交易一起打包成区块B2，交易的顺序是T1,T2,T3,T4,T6,
T5——这可能与这些交易到达排序节点的顺序不同！（本例展示了一个非常简单的排序节点配置。）*

An orderer receives proposed ledger updates concurrently from many different
applications in the network on a particular channel. Its job is to arrange
these proposed updates into a well-defined sequence, and package them into
*blocks* for subsequent distribution. These blocks will become the *blocks* of
the blockchain! Once an orderer has generated a block of the desired size, or
after a maximum elapsed time, it will be sent to all peers connected to it on a
particular channel. We'll see how this block is processed in phase 3.
在一个具体通道中，一个排序节点同时接收来自许多不同程序提议的账本更新。它的任务是为这些更
新安排明确的顺序，并将它们打包成*区块*以备随后的分发。这些打包后的账本更新就会成为区块链中
的*区块*！一旦一个排序节点生成了大小符合要求的区块，或者经过了最长时间之后，它会被送到特定通道中
所有与之相连的peer节点上。我们在阶段3会看到这些区块怎样被处理。

It's worth noting that the sequencing of transactions in a block is not
necessarily the same as the order of arrival of transactions at the orderer!
Transactions can be packaged in any order into a block, and it's this sequence
that becomes the order of execution. What's important is that there **is** a
strict order, rather than **what** that order is.
值得注意的是区块中交易的顺序不必与其到达排序节点时一致！交易在区块中可以被打包成任意顺序，
这个顺序将是排序节点执行的顺序。重要的是**有**一个严格的顺序，而顺序是**怎样**并不重要。

This strict ordering of transactions within blocks makes Hyperledger Fabric a
little different to some other blockchains where the same transaction can be
packaged into multiple different blocks. In Hyperledger Fabric, this cannot
happen -- the blocks generated by a collection of orderers are said to be
**final** because once a transaction has been written to a block, its position
in the ledger is immutably assured. Hyperledger Fabric's finality means that a
disastrous occurrence known as a **ledger fork** cannot occur. Once transactions
are captured in a block, history cannot be be rewritten for that transaction at
a future point in time.
区块中对交易的严格排序使Hyperledger Fabric与其他能将同一交易打包成多种区块的区块链略有
不同。在Hyperledger Fabric中，这不会发生——由一组排序节点生成的区块被称为是**最终**区块，因为
一旦交易被写入区块，其在账本中的位置将被确定，且不可更改。Hyperledger Fabric的最终确定性
意味着灾难性的**账本分叉**不会发生。一旦交易被写入区块，就不能在将来重写历史记录。

We can see also see that whereas peers host the ledger and chaincodes,
orderers most definitely do not. Every transaction that arrives at an orderer is
mechanically packaged in a block -- the orderer makes no judgement as to the
value of a transaction, it simply packages it. That's an important behavior of
Hyperledger Fabric -- all transactions are marshalled into in a strict order --
transactions are never dropped or de-prioritized.
我们也能看到尽管peer节点保存了账本和链码，绝大多数排序节点却没有。每一个到达排序节点的交易都在区块
中被机械化地打包——排序节点并不对交易的值进行判断，它只是简单地打包。这是Hyperledger Fabric
中很重要的操作——所有交易都被排成严格的顺序——交易永远不会被丢弃或取消优先级。

At the end of phase 2, we see that orderers have been responsible for the simple
but vital processes of collecting proposed transaction updates, ordering them,
packaging them into blocks, ready for distribution.
在阶段2末尾，我们看到了由排序节点负责的简单但却关键的任务，即收集提议的交易更新，对其排序，
打包成区块，并为分发做准备。

### Phase 3: Validation
### 阶段3 ：确认

The final phase of the transaction workflow involves the distribution and
subsequent validation of blocks from the orderer to the peers, where they can be
applied to the ledger. Specifically, at each peer, every transaction within a
block is validated to ensure that it has been consistently endorsed by all
relevant organizations before it is applied to the ledger. Failed transactions
are retained for audit, but are not applied to the ledger.
交易流程的最终阶段涉及对交易的分发以及后续从排序节点到peer节点的区块确认，在这里他们可以
被应用到账本上。具体说，在区块中每一个peer节点上的交易都被确认以确保它在被应用到账本之前
已经所有相关的组织一致地背书过。失败的交易将被保存用于审计，不应用到账本上。

![Peer12](./peers.diagram.12.png)

*The second role of an orderer node is to distribute blocks to peers. In this
example, orderer O1 distributes block B2 to peer P1 and peer P2. Peer P1
processes block B2, resulting in a new block being added to ledger L1 on P1.
In parallel, peer P2 processes block B2, resulting in a new block being added
to ledger L1 on P2. Once this process is complete, the ledger L1 has been
consistently updated on peers P1 and P2, and each may inform connected
applications that the transaction has been processed.*
*排序节点扮演的第二个角色是将区块分发到peer节点上。在本例中，排序节点O1将区块B2分发到
peer节点P1和P2上。P1对区块B2进行处理，生成一个全新的区块并加入P1自身的账本L1上。同样
地，peer节点P2处理区块B2，生成新区块并应用到自身的账本L1上。一旦该进程完成，账本L1就
被一致地更新到节点P1和P2上，且每个节点都可能通知应用程序交易已被处理完成。*

Phase 3 begins with the orderer distributing blocks to all peers connected to
it. Peers are connected to orderers on channels such that when a new block is
generated, all of the peers connected to the orderer will be sent a copy of the
new block. Each peer will process this block independently, but in exactly the
same way as every other peer on the channel. In this way, we'll see that the
ledger can be kept consistent. It's also worth noting that not every peer needs
to be connected to an orderer -- peers can cascade blocks to other peers using
the **gossip** protocol, who also can process them independently. But let's
leave that discussion to another time!
阶段3以排序节点将区块分发到所有与之相连的peer节点上开始。通道中的peer节点与排序节点相连，
所以每当生成一个新区块，新区块的副本会被发送到所有与排序节点相连的peer节点上。每个peer节点
会独立地处理这个区块，但其方式与通道中其他每个节点都相同。我们将看到使用这种方式可以使账本
保持一致。还值得注意的是并非所有peer节点都需要与一个排序节点相连——peer节点可使用**gossip**
数据传输协议将区块串联到其他区块上，且各节点能对其进行独立处理。这些我们下次再讨论！

Upon receipt of a block, a peer will process each transaction in the sequence in
which it appears in the block. For every transaction, each peer will verify that
the transaction has been endorsed by the required organizations according to the
*endorsement policy* of the chaincode which generated the transaction. For
example, some transactions may only need to be endorsed by a single
organization, whereas others may require multiple endorsements before they are
considered valid. This process of validation verifies that all relevant
organizations have generated the same outcome or result.
接收到一个区块后，peer节点会按照交易出现在区块中的顺序对其进行处理。对于每一个交易，
每个peer节点都会根据生成交易的链码的*背书策略*，验证其已被所需组织背书。比如，一些交易可能
只需要被单个组织背书，而其他交易在被认定为有效之前可能需要多个组织的背书。该确认过程就验
证了所有相关的组织已生成同样的结果。

If a transaction has been endorsed correctly, the peer will attempt to apply it
to the ledger. To do this, a peer must perform a ledger consistency check to
verify that the current state of the ledger is compatible with the state of the
ledger when the proposed update was generated. This may not always be possible,
even when the transaction has been fully endorsed. For example, another
transaction may have updated the same asset in the ledger such that the
transaction update is no longer valid and therefore can no longer be applied. In
this way each peer's copy of the ledger is kept consistent across the network
because they each follow the same rules for validation.
如果交易被正确背书，peer节点将试图将其应用到账本上。为此，节点必须执行账本一致性检查以确认
当前账本状态与生成提议更新时的状态兼容。即使当交易已被完整背书，这一点也并非总能达到。比如，
另一个交易可能在账本上更新了同样的资金导致该交易更新不再有效，因此不能再应用。这种方式下整个
网络中每个peer节点的账本副本都保持一致，因为它们遵循同样的验证规则。

After a peer has successfully validated each individual transaction, it updates
the ledger. Failed transactions are not applied to the ledger, but they are
retained for audit purposes, as are successful transactions. This means that
peer blocks are almost exactly the same as the blocks received from the orderer,
except for a valid or invalid indicator on each transaction in the block.
一个节点成功验证每个交易后，它就更新了账本。失败的交易不会被应用到账本，但会和成功的
交易一样被保存以作审计之用。这意味着，除了区块中每个交易的有效或无效指示符外，peer节点的区块
与从排序节点接收到的几乎完全相同。

We also note that phase 3 does not require the running of chaincodes -- this is
only done in phase 1, and that's important. It means that chaincodes only have
to be available on endorsing nodes, rather than throughout the blockchain
network. This is often helpful as it keeps the logic of the chaincode
confidential to endorsing organizations. This is in contrast to the output of
the chaincodes (the transaction proposal responses) which are shared to every
peer in the channel, whether or not they endorsed the transaction. This
specialization of endorsing peers is designed to help scalability.
我们也注意到阶段3并未要求执行链码——这只在阶段1 完成，且十分重要。它意味着链码只需在背书
节点上有效，而不必贯穿整个网络都有效。这对链码保持其逻辑对于背书组织机密十分有益。这与链码的
输出（交易提议响应）相反，它被共享到通道内的每个peer节点，不管它们是否已对交易背书。这种
背书节点的特殊化的设计旨在实现可扩展性。

Finally, every time a block is committed to to a peer's ledger, that peer
generates an appropriate *event*. *Block events* include the full block content,
while *block transaction events* include summary information only, such as
whether each transaction in the block has been validated or invalidated.
*Chaincode* events that the chaincode execution has produced can also be
published at this time. Applications can register for these event types so
that they can be notified when they occur. These notifications conclude the
third and final phase of the transaction workflow.
最后，每当一个区块被提交到peer节点的账本上，该peer节点就生成一个相适应的*事件*。*区块事件*包括
了整个区块内容，而*区块交易事件*只包括信息的摘要，比如是否每个区块中的交易都被确认与否。链码执
行所产生的*链码*事件也可以在此时发布，应用程序可以注册这些事件类型以便在事件发生时收到通知。
这些通知将结束交易流程的第三即最后阶段。

In summary, phase 3 sees the blocks which are generated by the orderer
consistently applied to the ledger. The strict ordering of transactions into
blocks allows each peer to validate that transaction updates are consistently
applied across the blockchain network.
总而言之，在阶段3我们看到了排序节点生成的区块被一致地应用到账本上。交易严格的排序规则允许每
个peer节点验证交易更新是否被一致地应用到整个区块链网络上。

### Orderers and Consensus
### 排序节点与共识
This entire transaction workflow process is called *consensus* because all peers
have reached agreement on the order and content of transactions, in a process
that is mediated by orderers. Consensus is a multi-step process and applications
are only notified of ledger updates when the process is complete -- which may
happen at slightly different times on different peers.
这整个交易流进程被称为共识，因为所有的peer节点对交易的顺序和内容都达成了*共识*。在一个由排序
节点介导的过程中，共识是一个多步骤的过程且应用程序只在该过程完成时才收到账本更新的通知——在
不同的节点上，可能收到更新的时间有细微差别。

We will discuss orderers in a lot more detail in a future orderer topic, but for
now, think of orderers as nodes which collect and distribute proposed ledger
updates from applications for peers to validate and include on the ledger.
在将来排序节点的专题中我们会更多地讨论其细节，但现在，请将排序节点视作这样一种节点，它收集并
分发来自应用程序提议的账本更新以便peer节点对其验证并将其应用在账本上。

That's it! We've now finished our tour of peers and the other components that
they relate to in Hyperledger Fabric. We've seen that peers are in many ways the
most fundamental element -- they form the network, host chaincodes and the
ledger, handle transaction proposals and responses, and keep the ledger
up-to-date by consistently applying transaction updates to it.
就是这些了！现在我们已经完成了对于peer节点和其它在Hyperledger Fabric中与之相关成分的认识，
我们发现，peer节点从许多方面看都是最基本的元素——它们组成了网络，保存链码和账本，处理交易的
提案和相应，并将交易一致地应用到账本上以保持账本最新。

