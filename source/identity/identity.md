# Identity

## What is an Identity? 什么是身份？

The different actors in a blockchain network include peers, orderers, client applications,
administrators and more. Each of these actors has an identity that is encapsulated in an
X.509 digital certificate. These identities really matter because they **determine the exact
permissions over resources that actors have in a blockchain network.** Hyperledger Fabric uses
certain properties in an actor's identity to determine permissions, and it gives them a
special name -- a **principal**. Principals are just like userIDs or groupIDs, but a little
more flexible because they can include a wide range of an actor's identity properties.
When we talk about principals, we're thinking about the actors in the system --
specifically the actor's identity properties which determine their permissions. These
properties are typically the actor's organization, organizational unit, role or even the
actor's specific identity. 在区块链网络中有不同的参与者，包括节点，排序者，客户端，管理员等。每个参与者都有一个封装成X.509的数字证书的身份。这些身份非常重要因为它们决定了这些参与者所拥有的在区块链网络中的精确的资源权限。 Hyperledger Fabric 使用参与者的一些身份属性来决定其权限，并赋予其一个特定的名称：principal，它非常像用户ID或组ID，但更灵活因为它们可以包含广泛的参与者的身份属性。当我们说起principal，我们就会想到系统里的参与者，特别是决定其权限的身份属性。这些属性通常指参与者的组织，组织单位，角色甚至参与者的特殊身份。

Most importantly, **an identity** must be **verifiable** (a real
identity, in other words), and for this reason it must come from an
authority **trusted** by the system. A [membership service provider](../membership/membership.html)
(MSP) is the means to achieve this in Hyperledger Fabric. More specifically,
an MSP is a component that represents the membership rules of an organization,
and as such, it that defines the rules that govern a valid identity
of a member of this organization. The default MSP implementation in Fabric
uses X.509 certificates as identities, adopting a traditional Public
Key Infrastructure (PKI) hierarchical model. 最重要的是身份必须是可验证的（即真实的身份）。由于这个原因，它必须被体系中的机构信任。在Hyperledger Fabric中会员服务提供者（MSP）就是干这个的。更特殊的是，MSP是一个组件，它代表了一个组织的会员规则，它定义了管理这个组织会员合法身份的规则。MSP的实施默认用X.509证书作为身份识别，采用了传统的公钥基础设施（PKI）体系模型。


## A Simple Scenario to Explain The Use of an Identity

Imagine that you visit a supermarket to buy some groceries. At the checkout you see
a sign that says that only Visa, Mastercard and AMEX cards are accepted. If you try to
 pay with a different card -- let's call it an "ImagineCard" -- it doesn't matter whether
 the card is authentic and you have sufficient funds in your account. It will be not be
 accepted.

![Scenario](./identity.diagram.6.png)

*Having a valid credit card is not enough -- it must also be accepted by the store! PKIs
and MSPs work together in the same way -- PKI provides a list of identities,
and an MSP says which of these are members of a given organization that participates in
the network.*

PKI certificate authorities and MSPs provide a similar combination of functionalities.
A PKI is like a card provider -- it dispenses many different types of verifiable
identities. An MSP, on the other hand, is like the list of card providers accepted
by the store -- determining which identities are the trusted members (actors)
of the store payment network. **MSPs turn verifiable identities into the members
of a blockchain network**.

Let's drill into these concepts in a little more detail.

## What are PKIs?

**A public key infrastructure (PKI) is a collection of internet technologies that provides
secure communications in a network.** It's PKI that puts the **S** in **HTTPS** -- and if
you're reading this documentation on a web browser, you're probably using a PKI to make
sure it comes from a verified source.

![PKI](./identity.diagram.7.png)

*The elements of Public Key Infrastructure (PKI). A PKI is comprised of Certificate
Authorities who issue digital certificates to parties (e.g., users of a service, service
provider), who then use them to authenticate themselves in the messages they exchange
with their environment. A CA's Certificate Revocation List (CRL) constitutes a reference
for the certificates that are no longer valid. Revocation of a certificate can happen fora number of reasons. For example, a certificate may be revoked because the cryptographic
private material associated to the certificate has been exposed.

Although a blockchain network is more than a communications network, it relies on the
PKI standard to ensure secure communication between various network participants, and to
ensure that messages posted on the blockchain are properly authenticated.
It's therefore really important to understand the basics of PKI and then why MSPs are
so important.

There are four key elements to PKI:

 * **Digital Certificates**
 * **Public and Private Keys**
 * **Certificate Authorities**
 * **Certificate Revocation Lists**

Let's quickly describe these PKI basics, and if you want to know more details,
[Wikipedia](https://en.wikipedia.org/wiki/Public_key_infrastructure) is a good place to start.

## Digital Certificates

A digital certificate is a document which holds a set of attributes relating to a
party. The most common type of certificate is the one compliant with the [X.509 standard](https://en.wikipedia.org/wiki/X.509),
which allows the encoding of a party's identifying details in its structure.
For example, John Doe of Accounting division in
FOO Corporation in Detroit, Michigan might have a digital certificate with a
`SUBJECT` attribute of `C=US, ST=Michigan, L=Detroit, O=FOO Corporation, OU=Accounting,
CN=John Doe /UID=123456`. John's certificate is similar to his government identity
card -- it provides information about John which he can use to prove key facts about him.
There are many other attributes in an X.509 certificate, but let's concentrate
on just these for now.

![DigitalCertificate](./identity.diagram.8.png)

*A digital certificate describing a party called John Doe. John is the `SUBJECT` of the
certificate, and the highlighted `SUBJECT` text shows key facts about John. The
certificate also holds many more pieces of information, as you can see. Most importantly,
John's public key is distributed within his certificate, whereas his private signing key
is not. This signing key must be kept private.*

What is important is that all of John's attributes can be recorded using a mathematical
technique called cryptography (literally, "*secret writing*") so that tampering will
invalidate the certificate. Cryptography allows John to present his certificate to others
to prove his identity so long as the other party trusts the certificate issuer, known
as a **Certificate Authority** (CA). As long as the CA keeps certain cryptographic
information securely (meaning, its own **private signing key**), anyone reading the
certificate can be sure that the information about John has not been tampered with --
it will always have those particular attributes for John Doe. Think of Mary's X.509
certificate as a digital identity card that is impossible to change.

## Authentication \& Public keys and Private Keys

Authentication and message integrity are important concepts of secure
communication. Authentication requires that parties who exchange messages
can be assured of the identity that created a specific message. Integrity
requires that the message was not modified during its transmission.
For example, you might want to be sure you're communicating with the real John
Doe than an impersonator. Or if John has sent you a message, you might want to be sure
that it hasn't been tampered with by anyone else during transmission.

Traditional authentication mechanisms rely on **digital signature mechanisms**, that
as the name suggests, allow a party to digitally **sign** its messages. Digital
signatures also provide guarantees on the integrity of the signed message.

Technically speaking, digital signature mechanisms require require for each party to
hold two cryptographically connected keys: a public key that is made widely available,
and acts as authentication anchor, and a private key that is used to produce
**digital signatures** on messages. Recipients of digitally signed messages can verify
the origin and integrity of a received message by checking that the
attached signature is valid under the public key of the expected sender.

**The unique relationship between a private key and the respective public key is the
cryptographic magic that makes secure communications possible**. The unique
mathematical relationship between the keys is such that the private key can be used to
produce a signature on a message that only the corresponding public key can match, and
only on the same message.

![AuthenticationKeys](./identity.diagram.9.png)

In the example above, to authenticate his message Joe uses his private key to produce a
signature on the message, which he then attaches to the message. The signature
can be verified by anyone who sees the signed message, using John's public key.



## Certificate Authorities

As you've seen, an actor or a node is able to participate in the blockchain network,
via the means of a **digital identity** issued for it by an authority trusted by the
system. In the most common case, digital identities (or simply **identities**) have
the form of cryptographically validated digital certificates that comply with X.509
standard, and are issued by a Certificate Authority (CA).

CAs are a common part of internet security protocols, and you've probably heard of
some of the more popular ones: Symantec (originally Verisign), GeoTrust, DigiCert,
GoDaddy, and Comodo, among others.

![CertificateAuthorities](./identity.diagram.11.png)

*A Certificate Authority dispenses certificates to different actors. These certificates
are digitally signed by the CA (i.e, using the CA's private key), and
bind together the actual actor with the actor's  public key, and optionally with a
comprehensive list of properties. Clearly, if one trust the CA (and
knows its public key), it can (by validating the CA's signature on the actor's
certificate) trust that the specific actor is bound to the public key included in the
certificate, and owns the included attributes.

Crucially certificates can be widely disseminated, as they do not include neither the
actors' nor the actual CA's private keys. As such they can be used as anchor of
trusts for authenticating messages coming from different actors.

In reality, CAs themselves also have a certificate, which they make widely
available. This allows the consumers of identities issued by a given CA to
verify them by checking that the certificate could only have been generated
by the holder of the corresponding private key (the CA).

In the Blockchain setting, every actor who wishes to interact with the network
needs an identity. In this setting,  you might say that **one or more CAs** can be used
to **define the members of an organization's from a
digital perspective**. It's the CA that provides the basis for an
organization's actors to have a verifiable digital identity.

### Root CAs, Intermediate CAs and Chains of Trust

CAs come in two flavors: **Root CAs** and **Intermediate CAs**. Because Root CAs
(Symantec, Geotrust, etc) have to **securely distribute** hundreds of millions
of certificates to internet users, it makes sense to spread this process out
across what are called *Intermediate CAs*. These Intermediate CAs
have their certificates issued by the root CA or another intermediate authority,
allowing the establishment of a "chain of trust" for any certificate
that is issued by any CA in the chain. This ability to track back to the Root
CA not only allows the function of CAs to scale while still providing security
-- allowing organizations that consume certificates to use Intermediate CAs with
confidence -- it limits the exposure of the Root CA, which, if compromised, would
endanger the entire chain of trust. If an Intermediate CA is compromised, on the
other hand, there is a much smaller exposure.

![ChainOfTrust](./identity.diagram.1.png)

*A chain of trust is established between a Root CA and a set of Intermediate CAs
as long as the issuing CA for the certificate of each of these Intermediate CAs is
either the Root CA itself or has a chain of trust to the Root CA.*

Intermediate CAs provide a huge amount of flexibility when it comes to the issuance
of certificates across multiple organizations, and that's very helpful in a
permissioned blockchain system. For example, you'll see that different organizations
may use different Root CAs, or the same Root CA with different Intermediate CAs --
it really does depend on the needs of the network.

### Fabric CA

It's because CAs are so important that Fabric provides a built-in CA component to
allow you to create CAs in the blockchain networks you form. This component -- known
as **fabric-ca** is a private root CA provider capable of managing digital identities of
Fabric participants that have the form of X.509 certificates.
Because Fabric-CA is a custom CA targetting the Root CA needs of Fabric,
it is inherently not capable of providing SSL certificates for general/automatic use
in browsers. However, because **some** CA must be used to manage identity
(even in a test environment), fabric-ca can be used to provide and manage
certificates. It is also possible -- and fully appropriate -- to use a
public/commerical root or intermediate CA to provide identification.

If you're interested, you can read a lot more about fabric-ca
[in the CA documentation section](http://hyperledger-fabric-ca.readthedocs.io/).

## Certificate Revocation Lists

A Certificate Revocation List (CRL) is easy to understand -- it's just a list of
references to certificates that a CA knows to be revoked for one reason or another. If you recall
the store scenario, a CRL would be like a list of stolen credit cards.

When a third party wants to verify another party's identity, it first checks the
issuing CA's CRL to make sure that the certificate has not been revoked.
A verifier doesn't have to check the CRL, but if they don't they run the risk of
accepting a compromised identity.

![CRL](./identity.diagram.12.png)

*Using a CRL to check that a certificate is still valid. If an impersonator tries to
pass a compromised digital certificate to a validating party, it can be first
checked against the issuing CA's CRL to make sure it's not listed as no longer valid.*

Note that a certificate being revoked is very different from a certificate expiring.
Revoked certificates have not expired -- they are, by every other measure, a fully
valid certificate. This is similar to the difference between an expired driver's license
 and a revoked driver's license. For more in depth information into CRLs, click [here](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#generating-a-crl-certificate-revocation-list).

Now that you've seen how a PKI can provide verifiable identities through a chain of
trust, the next step is to see how these identities can be used to represent the
trusted members of a blockchain network. That's where a Membership Service Provider
(MSP) comes into play -- **it identifies the parties who are the members of a given organization in the blockchain network**.

To learn more about membership, check out the conceptual documentation on [MSPs](../membership/membership.html).

<!---
Licensed under Creative Commons Attribution 4.0 International License https://creativecommons.org/licenses/by/4.0/
-->
