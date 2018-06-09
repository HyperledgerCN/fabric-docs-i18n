Chaincode
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

安全执行的编码规则适用于特殊类型的网络交易。Chaincode(目前使用Go来编写)是通过
一个合适地授权成员installed(安装)和instantiated(实例化)在对等节点的通道上。
最终用户可以通过client-side app(客户端应用程序)调用链码与网络中的对等节点进行联通。
Chaincode(链码)运行网络交易,如果验证通过,可以增加共享账本信息和修改world state(状态账本)。

.. Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/


