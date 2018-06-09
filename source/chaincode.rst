Chaincode Tutorials 链码教程
===================

What is Chaincode? 什么是链码？
------------------

Chaincode is a program, written in `Go <https://golang.org>`_, `node.js <https://nodejs.org>`_,
and eventually in other programming languages such as Java, that implements a
prescribed interface. Chaincode runs in a secured Docker container isolated from
the endorsing peer process. Chaincode initializes and manages ledger state
through transactions submitted by applications.

链码是一个程序,使用 `Go <https://golang.org>`_, `node.js <https://nodejs.org>`_ 来编写,
后续将可使用其他的编程语言例如Java,来实现规定的接口。
链码运行在一个安全的Docker容器中独立于背书节点。
通过app提交交易链码可以初始化和管理ledger state(账本状态)。

A chaincode typically handles business logic agreed to by members of the
network, so it may be considered as a "smart contract". State created by a
chaincode is scoped exclusively to that chaincode and can't be accessed
directly by another chaincode. However, within the same network, given
the appropriate permission a chaincode may invoke another chaincode to
access its state.

链码通常处理业务逻辑以便于同意区块链网络中的成员操作,所以它可以被视为一种"智能合约"。
只能通过唯一的域内链码进行账本状态的添加, 不能直接通过其他链码访问。
然而,在同一个网络里,可以给予适当的权限允许链码调用另一个链码访问它的账本状态。

Two Personas 两种观点
------------

We offer two different perspectives on chaincode. One, from the perspective of
an application developer developing a blockchain application/solution
entitled :doc:`chaincode4ade`, and the other, :doc:`chaincode4noah` oriented
to the blockchain network operator who is responsible for managing a blockchain
network, and who would leverage the Hyperledger Fabric API to install,
instantiate, and upgrade chaincode, but would likely not be involved in the
development of a chaincode application.

对于链码我们提供两种不同的观点。第一, 从应用程序开发人员的角度来看去开发区块链应用程序/
解决方案标题为 :doc:`chaincode4ade` 。第二， :doc:`chaincode4noah` 面向区块链网络
运营商 - 负责管理区块链网络，利用 Hyperledger Fabric API 进行安装、实例化、链码升级，
但可能不参与链码应用程序的开发。
