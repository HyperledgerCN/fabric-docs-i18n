Chaincode 链码
=========

[WIP]

The widely-used term, smart contract, is referred to as "chaincode" in
Hyperledger Fabric.

人们常说的"智能合约"，在Hyperledger Fabric中被称为"chaincode"。

Self-executing logic that encodes the rules for specific types of
network transactions. Chaincode (currently written in Go) is
installed and instantiated onto a channel's peers by an appropriately
authorized member. End users then invoke chaincode through a client-side
application that interfaces with a network peer. Chaincode runs network
transactions, which if validated, are appended to the shared ledger and
modify world state.

智能合约把一些规则编码进了特定类型的网络交易中，并且能够自动执行这些逻辑。Chaincode(目前使用Go来编写)是
通过一个被相应地授权的成员执行installed(安装)和instantiated(实例化)在相应channel（通道）的peer节点上。
终端用户可以通过连接着peer节点的client-side application(客户端应用程序)调用链码。
Chaincode(链码)运行网络交易,如果验证通过,可以增加共享账本信息和修改world state(状态账本)。

.. Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/

