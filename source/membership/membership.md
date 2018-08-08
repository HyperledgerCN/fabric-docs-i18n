# Membership

# ��Ա���

If you've read through the documentation on [Identity](../identity/identity.html)
you've seen how a PKI can provide verifiable identities through a chain
of trust. Now let's see how these identities can be used to represent the
trusted members of a blockchain network.

������Ѿ��Ķ����й�[���](../identity/identity.html)���ĵ�����ô���Ѿ��˽���PKI���ͨ���������ṩ����֤����ݡ����������ǿ�����Щ���������ڱ�ʾ����������Ŀ��ų�Ա��

This is where a **Membership Service** Provider (MSP) comes into play --
**it identifies which Root CAs and Intermediate CAs are trusted to define
the members of a trust domain, e.g., an organization**, either by
listing the identities of their members, or by identifying which CAs are authorized to
issue valid identities for their members, or -- as will usually be the case --
through a combination of both.

���� **��Ա����** �ṩ�̣�MSP���������õĵط� - **��ͨ���г���Ա����ݻ���ͨ��ʶ����ЩCA����ȨΪ���Ա������Ч���**������ - ͨ������� - ͨ�����ߵ���ϣ���������Щ��CA���м�CA�Ǳ����ε��ǿ���������������������֯���ĳ�Ա�ġ�

The power of an MSP goes beyond simply listing who is a network participant or
member of a channel. An MSP can identify specific roles an actor might play either within the range
or the organization (trust domain) the MSP represents (e.g., MSP admins, members of an
organization subdivision), and sets the basis for defining access privileges in the
the context of a network and channel (e.g., channel admins, readers, writers).
The configuration of an MSP is advertised to all the channels, where members of the
corresponding organization participate (in the form of a **channel MSP**).
Peers, orderers and clients also maintain a local MSP instance (also known as **lLocal
MSP**) to authenticate messages of members of their organization outside the context
of a channel.
In addition, an MSP can allow for the identification of a list of
identities that have been revoked (we discussed this in the [Identity](../identity/identity.html)
documentation but will talk about how that process also extends to an MSP).

MSP��ǿ���ܲ��������г�˭����������߻����ŵ��ģ�channel����Ա��MSP���Զ�������߿�����MSP������ķ�Χ����֯���������ڰ��ݵ��ض���ɫ�����磬MSP����Ա����֯��֧�ĳ�Ա������Ϊ��������ŵ��ı����£����磬�ŵ�����Ա���Ķ��ߣ�д���ߣ��������Ȩ�޵춨�˻�����MSP�����ñ�ͨ��������ŵ���������Ӧ��֯�ĳ�Ա����һ�� **�ŵ�MSP** ����ʽ�����롣�ڵ㣬����ڵ�Ϳͻ��˻�ά��һ������MSPʵ����Ҳ��Ϊ **lLocal MSP** ���������ŵ�����֮����֤����֯��Ա����Ϣ�����⣬MSP����ʶ���ѱ�����������б�����������ĵ��жԴ˽��������ۣ��������۸������������չ��MSP�ģ���

We'll talk more about local and channel MSPs in a moment. For now let's
talk more about what MSPs do in general.

�����Ժ����ϸ���۱���MSP���ŵ�MSP�� ������������̸̸ͨ������£�MSP����ô���ġ�

### Mapping MSPs to Organizations

###��MSPӳ�䵽��֯

An organization is a managed group of members and can be something as big
 as a multinational corporation or as small as a flower shop. What's most
 important about organizations (or **orgs**) is that they manage their
members under a single MSP. Note that this is different from the organization
concept defined in an X.509 certificate, which we'll talk about later.

��֯��һ���ܹ���ĳ�Ա��֯������������˾������Ҳ�����񻨵�һ��С�� **��֯��orgs��** ����Ҫ����������һ��MSP�¹������ǵĳ�Ա����ע�⣬����X.509֤���ж������֯���ͬ�����ǽ����Ժ����ۡ�

The exclusive relationship between an organization and its MSP makes it sensible to
name the MSP after the organization, a convention you'll find adopted in most policy
configurations. For example, organization `ORG1` would have an MSP called `ORG1-MSP`.
In some cases an organization may require multiple membership groups -- for example,
where channels are used to perform very different business functions between
organisations. In these cases it makes sense to have multiple MSPs and name them
accordingly, e.g., `ORG2-MSP-NATIONAL` and `ORG2-MSP-GOVERNMENT`, reflecting the
different membership roots of trust within `ORG2` in the NATIONAL sales channel
compared to the GOVERNMENT regulatory channel.

��֯����MSP֮��Ķ�ռ��ϵʹ������֮֯������MSP�����ǵģ����Ǵ�������������ж�����õ�Լ�������磬��֯ORG1����һ����ΪORG1-MSP��MSP����ĳЩ����£���֯������Ҫ�����Ա�� - ���磬ʹ���ŵ�����֮֯��ִ�зǳ���ͬ��ҵ���ܡ�����Щ����£��ж��MSP����Ӧ������������������ģ����磬ORG2-MSP-NATIONAL��ORG2-MSP-GOVERNMENT����ӳ����GOVERNMENT����ŵ���ȣ���NATIONAL�����ŵ��е�ORG2���β�ͬ��Ա����

![MSP1](./membership.diagram.3.png)

*Two different MSP configurations for an organization. The first configuration shows
the typical relationship between an MSP and an organization -- a single MSP defines
the list of members of an organization. In the second configuration, different MSPs
are used to represent different organizational groups with national, international,
and governmental affiliation.*

*һ����֯�����ֲ�ͬMSP���õ�һ��������ʾ��MSP����֮֯��ĵ��͹�ϵ - ����MSP��������֯��Ա�б� �ڵڶ��������У���ͬ��MSP���ڱ�ʾ���й��ң����ʺ����������Ĳ�ͬ��֯�顣*

#### Organizational Units and MSPs

####��֯��λ��MSP

An organization is often divided up into multiple **organizational units** (OUs), each
of which has a certain set of responsibilities. For example, the `ORG1`
organization might have both `ORG1-MANUFACTURING` and `ORG1-DISTRIBUTION` OUs
to reflect these separate lines of business. When a CA issues X.509 certificates,
the `OU` field in the certificate specifies the line of business to which the
identity belongs.

һ����֯ͨ��������Ϊ��� **��֯��λ** ��OU����ÿ����֯��λ����һ�������Ρ����磬ORG1��֯����ͬʱ����ORG1-MANUFACTURING��ORG1-DISTRIBUTION OU���Է�ӳ��Щ������ҵ���ߡ���CA�䷢X.509֤��ʱ��֤���е�OU�ֶ�ָ����ʶ������ҵ���ߡ�

We'll see later how OUs can be helpful to control the parts of an organization who
are considered to be the members of a blockchain network. For example, only
identities from the `ORG1-MANUFACTURING` OU might be able to access a channel,
whereas `ORG1-DISTRIBUTION` cannot.

�Ժ����ǽ�����OU��������ڿ��Ʊ���Ϊ�����������Ա��һ����֯�����磬ֻ��ORG1-MANUFACTURING OU�еı�ʶ�����ܹ������ŵ�����ORG1-DISTRIBUTION�е����ܡ�


Finally, though this is a slight misuse of OUs, they can sometimes be used by
different* organizations in a consortium to distinguish each other. In such cases, the
different organizations use the same Root CAs and Intermediate CAs for their chain
of trust, but assign the `OU` field appropriately to identify members of each
organization. We'll also see how to configure MSPs to achieve this later.

��󣬾������Ƕ�OU����΢���ã���������ʱ���Ա������еĲ�ͬ* ��֯�������ֱ˴ˡ�����������£���ͬ����֯����������ʹ����ͬ�ĸ�CA���м�CA�����ʵ��ط���OU�ֶ��Ա�ʶÿ����֯�ĳ�Ա�� ���ǻ��������������MSP�Ա��Ժ�ʵ�ִ�Ŀ�ġ�


### Local and Channel MSPs

###����MSP���ŵ�MSP

MSPs appear in two places in a Blockchain network: channel configuration
(**channel MSPs**), and locally on an actor's premise (**local MSP**).
**Local MSPs defined for nodes** (peer or orderer) and **users** (administrators
that use the CLI or client applications that use the SDK). **Every node and user
must have a local MSP defined**, as it defines who has administrative or
participatory rights at that level and outside the context of a channel
(who the administrators of a peer's organization, for example).

MSP�����������������е�����λ�ã��ŵ����ã� **�ŵ�MSP** ���Լ��ڲ����ߵı��أ� **����MSP** ���� **����MSP��Ϊ�ڵ㣨�ڵ����ڵ㣩���û���ʹ��CLI�Ĺ���Ա��ʹ��SDK�Ŀͻ���Ӧ�ó��򣩶����**��ÿ���ڵ���û������붨��һ������MSP����Ϊ���������ڸü�������ŵ�����֮��˭���й��������Ȩ�ޣ����磬һ���ڵ���֯�Ĺ���Ա����

In contrast, **channel MSPs define administrative and participatory rights at the
channel level**. Every organization participating in a channel must have an MSP
defined for it. Peers and orderers on a channel will all share the same view on channel
MSPs, and will henceforth be able to authenticate correctly the channel participants.
This means that if an organization wishes to join the channel, an MSP incorporating
the chain of trust for the organization's members would need to be included in the
channel configuration. Otherwise transactions originating from this organization's
identities will be rejected.

�෴��**�ŵ�MSP���ŵ��������˹���Ͳ����Ȩ��**�� **�����ŵ���ÿ����֯��������һ��Ϊ�䶨���MSP**��һ���ŵ��ϵĽڵ�ͱ���ڵ㽫���ŵ�MSP�Ϲ�����ͬ����ͼ�����Ҵ˺��ܹ���ȷ����֤�ŵ������ߡ�����ζ�ţ����һ����֯ϣ��������ŵ�������Ҫ����������֯��Ա��������MSP�����ŵ������С� �������Ը���֯����ݵĽ��׽����ܾ���

The key difference here between local and channel MSPs is not how they function,
but their **scope**.

����MSP���ŵ�MSP֮��Ĺؼ������������ǵ�������ʽ�����������ǵ� **��Χ** ��

![MSP2](./membership.diagram.4.png)

*Local and channel MSPs. The trust domain (e.g., organization) of each
peer is defined by the peer's local MSP, e.g., ORG1 or ORG2. Representation
of an organization on a channel is achieved by the inclusion of the
organization's MSP in the channel. For example, the channel of this figure
is managed by both ORG1 and ORG2. Similar principles apply for
the network, orderers, and users, but these are not shown here for
simplicity.*

*����MSP���ŵ�MSP�� ÿ���ڵ�����������磬��֯���ɽڵ�ı���MSP���壬����ORG1��ORG2��ͨ�����ŵ��а�����֯��MSP��ʵ��һ����֯���ŵ��ϵı�ʾ�����磬��ͼ���ŵ�ͬʱ��ORG1��ORG2�������Ƶ�ԭ�����������磬�ڵ���û�����Ϊ��������˴�δ��ʾ��Щԭ��*��

**Local MSPs are only defined on the file system of the node
or user** to which they apply. Therefore, physically and logically there is
only one local MSP per node or user. However, as **channel MSPs are available
to all nodes in the channel**, they are logically defined once in
the channel in its configuration. However, **a channel MSP is instantiated
on the file system of every node in the channel and kept synchronized via
consensus**. So while there is a copy of each channel MSP on the local file
system of every node, logically a channel MSP resides on and is maintained by
the channel or the network.

**����MSP��������Ӧ�õĽڵ���û����ļ�ϵͳ�ϱ�����**����ˣ��������Ϻ��߼��ϣ�ÿ���ڵ���û�ֻ��һ������MSP�����ǣ�**�����ŵ�MSP�������ŵ��е����нڵ�**��������������ϣ��������ŵ��н���һ���߼��ϵĶ��塣Ȼ����**�ŵ�MSP���ŵ���ÿ���ڵ���ļ�ϵͳ�Ͻ���ʵ��������ͨ����ʶ����ͬ��**����ˣ���Ȼÿ���ڵ�ı����ļ�ϵͳ�ϴ���ÿ���ŵ�MSP�ĸ��������߼���һ���ŵ�MSPפ���ڸ��ŵ��ϲ����ŵ�������ά����

You may find it helpful to see how local and channel MSPs are used by seeing
what happens when a blockchain administrator installs and instantiates a smart
contract, as shown in the [diagram above](MSP2).

ͨ���鿴����������Ա��װ��ʵ�������ܺ�Լʱ�ᷢ��ʲô�����ܻ�����ʹ�ñ���MSP���ŵ�MSP���а���������ͼ��ʾ��

An administrator `B` connects to the peer with an identity issued by `RCA1`
and stored in their local MSP. When `B` tries to install a smart contract on
the peer, the peer checks its local MSP, `ORG1-MSP`, to verify that the identity
of `B` is indeed a member of `ORG1`. A successful verification will allow the
install command to complete successfully. Subsequently, `B` wishes
to instantiate the smart contract on the channel. Because this is a channel
operation, all organizations in the channel must agree to it. Therefore, the
peer must check the MSPs of the channel before it can successfully commits this
command. (Other things must happen too, but concentrate on the above for now.)

����ԱBʹ��RCA1��������ݸ��ڵ����Ӳ��洢���䱾��MSP�С���B�����ڽڵ��ϰ�װ���ܺ�Լʱ���ڵ����䱾��MSP ��ORG1-MSP������֤B�����ȷʵ��ORG1�ĳ�Ա���ɹ���֤������װ����ɹ���ɡ� ���Bϣ�����ŵ���ʵ�������ܺ�Լ����������һ���ŵ��������ŵ��е�������֯������ͬ��ò�������ˣ��ڵ�����ȼ���ŵ���MSP��Ȼ����ܳɹ��ύ����� ��������������Ҳ���뷢���������ڹ�ע���������ݡ���

One can observe that channel MSPs, just like the channel itself is a
**logical construct**. They only become physical once they are instantiated
on the local filesystem of the peers of the channel orgs, and managed by it.

����Թ۲쵽�ŵ�MSP�������ŵ�������һ�� **�߼��ṹ**������ֻ�����ŵ���֯�Ľڵ�ı����ļ�ϵͳ�Ͻ���ʵ������Ż��Ϊ�����ϵģ����������й���


### MSP Levels

###MSP��

The split between channel and local MSPs reflects the needs of organizations
to administer their local resources, such as a peer or orderer nodes, and their
channel resources, such as ledgers, smart contracts, and consortia, which
operate at the channel or network level. It's helpful to think of these MSPs
as being at different **levels**, with **MSPs at a higher level relating to
network administration concerns** while **MSPs at a lower level handle
identity for the administration of private resources**. MSPs are mandatory
at every level of administration -- they must be defined for the network,
channel, peer, orderer and users.


�ŵ�MSP�ͱ���MSP֮��ķ��뷴ӳ����֯�����䱾����Դ������ڵ����ڵ㣩�����ŵ���Դ���������ŵ������缶����Ӫ�ķ����ʣ����ܺ�Լ�����ˣ������󡣽���ЩMSP��Ϊ���ڲ�ͬ **���** ���а����ģ�**MSP�������������������صĸ��߼��𣬶��ϵͼ����MSP����˽����Դ��������**��MSP��ÿ�������ζ��Ǳ���� -����Ϊ���磬�ŵ����ڵ㣬����ڵ���û�����MSP��

![MSP3](./membership.diagram.2.png)

*MSP Levels. The MSPs for the peer and orderer are local, whereas the MSPs for a
channel (including the network configuration channel) are shared across all
participants of that channel. In this figure, the network configuration channel
is administered by ORG1, but another application channel can be managed by ORG1
and ORG2. The peer is a member of and managed by ORG2, whereas ORG1 manages the
orderer of the figure. ORG1 trusts identities from RCA1, whereas ORG2 trusts
identities from RCA2. Note that these are administration identities, reflecting
who can administer these components. So while ORG1 administers the network,
ORG2.MSP does exist in the network definition.*

*MSP�㡣 �ڵ�ͱ���ڵ��MSP�Ǳ��صģ����ŵ���MSP���������������ŵ����ڸ��ŵ������в�����֮�乲�� �ڴ�ͼ�У����������ŵ���ORG1��������һ��Ӧ�ó����ŵ�����ORG1��ORG2�����ڵ���ORG2�ĳ�Ա����ORG2������ORG1����ͼ�еı���ڵ㡣 ORG1��������RCA1����ݣ���ORG2��������RCA2����ݡ���ע�⣬��Щ�ǹ�����ݣ���ӳ��˭���Թ�����Щ����� ��ˣ�����ORG1�������磬��ORG2.MSPȷʵ���������綨����*��

 * **Network MSP:** The configuration of a network defines who are the
 members in the network --- by defining the participant organizations MSPs
 --- as well as which of these members are authorized to perform
 administrative tasks (e.g., creating a channel).

 * **����MSP��** ���������ͨ�������������֯��MSP�Լ���Щ��Ա����Ȩִ�й����������磬�����ŵ��������� ˭�������еĳ�Ա��

 * **Channel MSP:** It is important for a channel to maintain the MSPs of its members
 separately. A channel provides private communications between a particular set of
 organizations which in turn have administrative control over it. Channel
 policies interpreted in the context of that channel's MSPs
 define who has ability to participate in certain action on the channel, e.g., adding
 organizations, or instantiating chaincodes. Note that there
 is no necessary relationship between the permission to administrate a channel and
 the ability to administrate the network configuration channel (or any other channel).
 Administrative rights exist within the scope of what is being administrated
 (unless the rules have been written otherwise -- see the discussion of the `ROLE` attribute below).

 * **�ŵ�MSP��** �ŵ������ܹ�����ά�����Ա��MSP���ŵ����ض���һ����֮֯���ṩ˽��ͨ�ţ�����Щ��֯�ַ��� ��������й�����ơ��ڸ��ŵ���MSP�Ļ����н��͵��ŵ����Զ���˭�����������ŵ��ϵ�ĳЩ������磬�����֯ ��ʵ���������롣��ע�⣬����һ���ŵ���Ȩ�����������������ŵ������κ������ŵ���������֮��û�б�Ȼ�Ĺ�ϵ �������Ȩ�޴����ڹ���Χ�ڣ����ǹ����Ѿ����б�д - ����������ROLE���Ե����ۣ���

 * **Peer MSP:** This local MSP is defined on the file system of each peer and there is a
 single MSP instance for each peer. Conceptually, it performs exactly the same function
 as channel MSPs with the restriction that it only applies to the peer where it is defined.
 An example of an action whose authorization is evaluated using the peer's local MSP is
 the installation of a chaincode on the peer premise.

 * **�ڵ�MSP��** ��ÿ���ڵ���ļ�ϵͳ�϶��屾��MSP������ÿ���ڵ㶼��һ��MSPʵ���� �Ӹ����Ͻ�����ִ������ ��MSP��ȫ��ͬ�Ĺ��ܣ�Լ�������������ڶ������Ľڵ㡣 ʹ�ýڵ�ı���MSP��������Ȩ�Ĳ�����һ��ʾ�����ڽڵ� ǰ���°�װ�����롣

 * **Orderer MSP:** Like a peer MSP, an orderer local MSP is also defined on the file system
 of the node and only applies to that node. Like peer nodes, orderers are also owned by a single
 organization and therefore have a single MSP to list the actors or nodes it trusts.

 * **����ڵ�MSP��** ��ڵ�MSPһ��������ڵ㱾��MSPҲ�ڽڵ���ļ�ϵͳ�϶��岢�ҽ������ڸýڵ㡣 ��ڵ�һ ��������ڵ�Ҳ�ɵ�����֯ӵ�У����ֻ��һ��MSP���г������εĲ����߻�ڵ㡣


### MSP Structure

###MSP�ṹ

So far, you've seen that the two most important elements of an MSP are the specification
of the (root or intermediate) CAs that are used to used to establish an actor's or node's
membership in the respective organization. There are, however, more elements that are
used in conjunction with these two to assist with membership functions.

��ĿǰΪֹ�����Ѿ�����һ��MSP����������Ҫ��Ԫ���ǣ������м䣩CA�ĸ�ʽ����������Ӧ����֯�н��������߻�ڵ�ĳ�Ա�ʸ񡣵��ǣ��и����Ԫ����������Ԫ�ؽ��ʹ����Э����Ա��ݵĹ��ܡ�

![MSP4](./membership.diagram.5.png)

*The figure above shows how a local MSP is stored on a local filesystem. Even though
channel MSPs are not physically structured in exactly this way, it's still a helpful
way to think about them.*

*��ͼ��ʾ�˱���MSP��δ洢�ڱ����ļ�ϵͳ�С������ŵ�MSP������ṹ������ˣ�������Ȼ��һ�����õķ�ʽ��˼������*��

As you can see, there are nine elements to an MSP. It's easiest to think of these elements
in a directory structure, where the MSP name is the root folder name with each
subfolder representing different elements of an MSP configuration.

����������һ��MSP�оŸ�Ԫ�ء���򵥵ķ�������Ŀ¼�ṹ�п�����ЩԪ�أ�����MSP�����Ǹ��ļ������ƣ�ÿ�����ļ��д���MSP���õĲ�ͬԪ�ء�

Let's describe these folders in a little more detail and see why they are important.

�����Ǹ���ϸ��������Щ�ļ��У�������Ϊʲô���Ǻ���Ҫ��


* **Root CAs:** This folder contains a list of self-signed X.509 certificates of
  the Root CAs trusted by the organization represented by this MSP.
  There must be at least one Root CA X.509 certificate in this MSP folder.

* **��CA��** ���ļ��а�����MSP����ʾ����֯�����εĸ�CA����ǩ��X.509֤���б� ��MSP�ļ����б���������һ  ��Root CA X.509֤�顣

  This is the most important folder because it identifies the CAs from
  which all other certificates must be derived to be considered members of the
  corresponding organization.
  ��������Ҫ���ļ��У���Ϊ����ʶCA���Ӹ�CA�����뱻��Ϊ��Ӧ��֯�ĳ�Ա��������������������֤��.


* **Intermediate CAs:** This folder contains a list of X.509 certificates of the
  Intermediate CAs trusted by this organization. Each certificate must be signed by
  one of the Root CAs in the MSP or by an Intermediate CA whose issuing CA chain ultimately
  leads back to a trusted Root CA.

* **�м�CA��** ���ļ��а�������֯���ε��м�CA��X.509֤���б� ÿ��֤�������MSP�е�һ����CA���м�CAǩ��  ���м�CA�䷢��CA�����ջ᷵�ص������εĸ�CA.

  Intuitively to see the use of an intermediate CA with respect to the corresponding
  organization's structure, an intermediate CA may represent a different subdivision
  of the organization, or the organization itself (e.g., if a commercial CA is leveraged
  for the organization's identity management). In the latter case other intermediate CAs,
  lower in the CA hierarchy can be used to represent organization subdivisions.
  [Here](../msp.html) you may find more information on best practices for MSP configuration.
  Notice, that it is possible to have a functioning network that does not have any
  Intermediate CA, in which case this folder would be empty.
 
  ֱ�۵ؿ����������Ӧ��֯�Ľṹʹ���м�CA���м�CA���Ա�ʾ��֯����֯����Ĳ�ͬϸ�֣����磬���һ����ҵCA    ������֯����ݹ������ں�һ������£�CA��νṹ�нϵ͵������м�CA�����ڱ�ʾ��֯��ϸ�֡������������    �ҵ��й�MSP���õ����ʵ���ĸ�����Ϣ����ע�⣬����ʹһ����������������û���κ��м�CA������������£�����   ���н�Ϊ�ա�

  Like the Root CA folder, this folder defines the CAs from which certificates must be
  issued to be considered members of the organization.

  ���CA�ļ���һ�������ļ��ж����˱���䷢֤����ܱ���Ϊ��֯��Ա��CA.

* **Organizational Units (OUs):** These are listed in the `$FABRIC_CFG_PATH/msp/config.yaml`
  file and contain a list of organizational units, whose
  members are considered to be part of the organization represented by this MSP.
  This is particularly useful when you want to restrict the members of an organization
  to the ones holding an identity (signed by one of MSP designated CAs) with a specific
  OU in it.

* **��֯��λ��OU����** ��Щ��λ����$ FABRIC_CFG_PATH / msp / config.yaml�ļ��У�������һ����֯��λ����  �����Ա����Ϊ��MSP���������֯��һ���֡� ����ϣ������֯��Ա����Ϊӵ�����а����ض�OU����ݣ���MSPָ��  ��CA֮һǩ�����ĳ�Աʱ���˹����������á�

  Specifying OUs is optional. If no OUs are listed, all the identities that are part of
  an MSP -- as identified by the Root CA and Intermediate CA folders -- will be considered
  members of the organization.
 
  �ض�OU�ǿ�ѡ�ġ� ���δ�г��κ�OU������ΪMSPһ���ֵ�������ݣ��ɸ�CA���м�CA�ļ��б�ʶ��������Ϊ��֯��  ��Ա��

* **Administrators:** This folder contains a list of identities that define the
  actors who have the role of administrators for this organization. For the standard MSP type,
  there should be one or more X.509 certificates in this list.

* **����Ա��** ���ļ��а���һ����ʶ�б����ڶ�����д���֯����Ա��ɫ�Ĳ����ߡ� ���ڱ�׼MSP���ͣ����б���  Ӧ����һ������X.509֤�顣

  It's worth noting that just because a actor has the role of an administrator it doesn't
  mean that they can administer particular resources! The actual power a given identity has
  with respect to administering the system, is determined by the policies that manage system
  resources. For example, a channel policy might specify that `ORG1-MANUFACTURING`
  administrators have the rights to add new organizations to the channel, whereas the
  `ORG1-DISTRIBUTION` administrators have no such rights.
  
  ֵ��ע����ǣ�������Ϊһ�������߾��й���Ա�Ľ�ɫ��������ζ�����ǿ��Թ����ض�����Դ����������ڹ���ϵͳ��  ���ʵ�ʹ�Ч�ɹ���ϵͳ��Դ�Ĳ��Ծ��������磬�ŵ����Կ���ָ��ORG1-MANUFACTURING����Ա��Ȩ������֯��ӵ�  �ŵ�����ORG1-DISTRIBUTION����Ա��û�д���Ȩ�ޡ�

  Even though an X.509 certificate has a `ROLE` attribute (specifying, for example, that a
  actor is an `admin`), this refers to a actor's role within its organization
  rather than on the blockchain network. This is similar to the purpose of
  the `OU` attribute, which -- if it has been defined -- refers to a actor's place in
  the organization.

  ��ʹX.509֤�����ROLE���ԣ����磬ָ��ĳ���������ǹ���Ա������Ҳ��ָ������֯�ڶ������������������еĽ�ɫ   ����������OU���Ե�Ŀ�ģ�����Ѿ����壬��ָ������֯�в����ߵ�λ�á�

  The `ROLE` attribute **can** be used to confer administrative rights at the channel level
  if the policy for that channel has been written to allow any administrator from an organization
  (or certain organizations) permission to perform certain channel functions (such as
  instantiating chaincode). In this way, an organization role can confer a network role.
  This is conceptually similar to how having a driver's license issued by the US state of
  Florida entitles someone to drive in every state in the US.

  ����ѱ�д���ŵ��Ĳ�����������֯����ĳЩ��֯�����κι���Աִ��ĳЩ�ŵ����ܣ�����ʵ���������룩����ROLE  ���Կ��������ŵ����������Ȩ�ޡ� ͨ�����ַ�ʽ����֯��ɫ���Ը��������ɫ�� ���ڸ����������������������  �ݰ䷢�ļ�ʻִ�������Ȩĳ����������ÿ���ݿ�����

* **Revoked Certificates:** If the identity of a actor has been revoked,
  identifying information about the identity -- not the identity itself -- is held in this folder.
  For X.509-based identities, these identifiers are pairs of strings known as
  Subject Key Identifier (SKI) and Authority Access Identifier (AKI), and are checked
  whenever the X.509 certificate is being used to make sure the certificate has not
  been revoked.

* **����֤�飺**  ��������ߵ�����ѱ����������ڴ��ļ����б����й���ݵ���Ϣ - ��������ݱ��� ���ڻ���  X.509����ݣ���Щ����ǳ�Ϊ������Կ��ʶ����SKI������Ȩ���ʱ�ʶ����AKI�����ַ����ԣ�����ÿ��ʹ��X.509֤  ����ȷ��֤����δ������ʱ���ͻ������м�顣

  This list is conceptually the same as a CA's Certificate Revocation List (CRL), but it also
  relates to revocation of membership from the organization. As a
  result, the administrator of an MSP, local or channel, can quickly revoke a actor or node
  from an organization by advertising the updated CRL of the CA the revoked certificate
  as issued by.
  This "list of lists" is optional. It will only become populated as certificates are revoked.

  ���б��ڸ�������CA��֤������б�CRL����ͬ������Ҳ�����֯�г�����Ա����йء� ��ˣ�MSP�Ĺ���Ա������  ���ŵ�������ͨ����CA�������ѳ���֤��������CA���µ�CRL�����ٳ�����֯�еĲ����߻�ڵ㡣 ����ǿ�ѡ�ġ� ��  ֻ����֤�鱻����ʱ��䡣


* **Node Identity:** This folder contains the identity of the node, i.e.,
  cryptographic material that --- in combination to the content of KeyStore -- would
  allow the node to authenticate itself in the messages that is sends to other
  participants of its channels and network.
  For X.509 based identities, this folder contains an **X.509 certificate**.
  This is the certificate a peer places in a transaction proposal response, for example,
  to indicate that the peer has endorsed it -- which can subsequently be checked
  against the resulting transaction's endorsement policy at validation time.

* **�ڵ��ʶ��** ���ļ��а����ڵ�ı�ʶ��������KeyStore��ϵļ��ܲ��Ͻ�����ڵ��ڷ��͸����ŵ������������   �����ߵ���Ϣ�ж�������������֤�� ���ڻ���X.509�ı�ʶ�����ļ��а��� **X.509֤��**�� ���ǽڵ��ڽ�������   ��Ӧ�з��õ�֤�飬���磬����ָʾ�ڵ��Ѿ��Ͽ��� - �������ڿ���֤ʱ���ڶԽ�����׵��Ͽɲ��Խ��м�顣

  This folder is mandatory for local MSPs, and there must be exactly one X.509 certificate
  for the node. It is not used for channel MSPs.

  ���ļ��ж��ڱ���MSP�Ǳ���ģ����Ҹýڵ����ֻ��һ��X.509֤�顣 ���������ŵ�MSP��


* **KeyStore for Private Key:** This folder is defined for the local MSP of a peer or
  orderer node (or in an client's local MSP), and contains the node's **signing key**.
  This key matches cryptographically the node's identity included in **Node Identity**
  folder and is used to sign data -- for example to sign a transaction proposal response,
  as part of the endorsement phase.

* **˽Կ��KeyStore��**  ���ļ�����Ϊ�ڵ����ڵ�ı���MSP����ͻ��˵ı���MSP������ģ������ڵ�� **ǩ  ����Կ**�� ����Կ�Լ��ܷ�ʽƥ���ڽڵ��ʶ�ļ����а����Ľڵ��ʶ��������ǩ������ - ����ǩ����������Ӧ  ����Ϊ�Ͽɽ׶ε�һ���֡�

  This folder is mandatory for local MSPs, and must contain exactly one private key.
  Obviously, access to this folder must be limited only to the identities, users who have
  administrative responsibility on the peer.

  ���ļ��ж��ڱ���MSP�Ǳ���ģ����ұ���ֻ����һ��˽Կ�� ��Ȼ���Դ��ļ��еķ���Ȩ�ޱ�������ڽڵ��Ͼ��й�  ��ְ����û���ݡ�

  Configuration of a **channel MSPs** does not include this part, as channel MSPs aim
  to offer solely identity validation functionalities, and not signing abilities.

  **�ŵ�** MSP�����ò������˲��֣���Ϊ�ŵ�MSPּ�ڽ��ṩ�����֤���ܣ�������ǩ�����ܡ�


* **TLS Root CA:** This folder contains a list of self-signed X.509 certificates of the
  Root CAs trusted by this organization **for TLS communications**. An example of a TLS
  communication would be when a peer needs to connect to an orderer so that it can receive
  ledger updates.

* **TLS��CA��** ���ļ��а�������֯���ε����� **TLSͨ��** �ĸ�CA����ǩ��X.509֤���б� TLSͨ�ŵ�һ��ʾ��  �ǵ��ڵ���Ҫ���ӵ�����ڵ��Ա������Խ��շ����ʸ���ʱ��

  MSP TLS information relates to the nodes inside the network, i.e., the peers and the
  orderers, rather than those that consume the network -- applications and administrators.

  MSP TLS��Ϣ�漰�����ڵĽڵ㣬���ڵ�ͱ���ڵ㣬��������Щ��������Ľڵ� - Ӧ�ó���͹���Ա��

  There must be at least one TLS Root CA X.509 certificate in this folder.

  ���ļ����б���������һ��TLS��CA X.509֤�顣


* **TLS Intermediate CA:** This folder contains a list intermediate CA certificates
  CAs trusted by the organization represented by this MSP **for TLS communications**.
  This folder is specifically useful when commercial CAs are used for TLS certificates of an
  organization. Similar to membership intermediate CAs, specifying intermediate TLS CAs is
  optional.

* **TLS�м�CA��** ���ļ��а���һ���б��м�CA֤�鱻��MSP���������֯�����Σ�������TLSͨ�š�����ҵCA����  һ����֯��TLS֤��ʱ�����ļ����ر����á����Ա�м�CA���ƣ��м�TLS CA�ǿ�ѡ�ġ�

  For more information on TLS, click [here](../enable_tls.html).

  �й�TLS�ĸ�����Ϣ���뵥��[�˴�](../enable_tls.html)

If you've read this doc as well as our doc on [Identity](../identity/identity.html)), you
should have a pretty good grasp of how identities and membership work in Hyperledger Fabric.
You've seen how a PKI and MSPs are used to identify the actors collaborating in a blockchain
network. You've learned how certificates, public/private keys, and roots of trust work,
in addition to how MSPs are physically and logically structured.

������Ѿ��Ķ������ĵ��Լ����ǵ�����ĵ�����ô��Ӧ�÷ǳ��˽�Hyperledger Fabric�е���ݺͳ�Ա��ݡ� ���Ѿ��˽������ʹ��PKI��MSP��ʶ����������������Э���Ĳ����ߡ� ����MSP��������߼��ṹ֮�⣬�����˽���֤�飬��Կ/˽Կ�����θ��Ĺ���ԭ��

<!---
Licensed under Creative Commons Attribution 4.0 International License https://creativecommons.org/licenses/by/4.0/
-->
