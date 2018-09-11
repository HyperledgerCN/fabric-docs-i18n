System Chaincode Plugins
========================
系统链码插件
========================

System chaincodes are specialized chaincodes that run as part of the peer process
as opposed to user chaincodes that run in separate docker containers. As
such they have more access to resources in the peer and can be used for
implementing features that are difficult or impossible to be implemented through
user chaincodes. Examples of System Chaincodes are ESCC (Endorser System Chaincode)
for endorsing proposals, QSCC (Query System Chaincode) for ledger and other
fabric related queries and VSCC (Validation System Chaincode) for validating
a transaction at commit time respectively.

系统链码是作为节点进程的一部分运行的专用链码，而不是在单独的在docker容器中运行的用户链码。因此，它们可以更多地访问节点程序中的资源，并可用于实现难以或不可能通过用户链码实现的功能。系统链码的例子有：ESCC(Proendorser System Chaincode)用于支持提议，QSCC(QuerySystem Chaincode)用于分类账和其他与fabric相关的查询，VSCC(ValidationSystem Chaincode)用于提交时间验证事务。

Unlike a user chaincode, a system chaincode is not installed and instantiated
using proposals from SDKs or CLI. It is registered and deployed by the peer at start-up.

与用户链码不同，系统链码不会使用SDK或CLI的提议来安装和实例化。它在启动时由节点注册和部署。

System chaincodes can be linked to a peer in two ways: statically and dynamically
using Go plugins. This tutorial will outline how to develop and load system chaincodes
as plugins.

系统链码可以通过两种方式链接到节点：静态地和动态地使用GO插件。本教程将概述如何开发和加载系统链码作为插件。

Developing Plugins
------------------
开发插件
------------------ 

A system chaincode is a program written in `Go <https://golang.org>`_ and loaded
using the Go `plugin <https://golang.org/pkg/plugin>`_ package.

系统链码是用 `Go <https://golang.org>`_ 编写的程序，并且使用Go  `plugin <https://golang.org/pkg/plugin>`_ 插件包加载。

A plugin includes a main package with exported symbols and is built with the command
``go build -buildmode=plugin``.

插件包含一个带有导出符号的主包，并使用命令 ``go build -buildmode = plugin`` 构建。

Every system chaincode must implement the `Chaincode Interface <http://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#Chaincode>`_
and export a constructor method that matches the signature ``func New() shim.Chaincode`` in the main package.
An example can be found in the repository at ``examples/plugin/scc``.

每个系统链码都必须实现 `Chaincode Interface <http://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#Chaincode>`_ 并导出一个与主包中的签名 ``func New() shim.Chaincode`` 匹配的构造函数方法。 可以在 ``examples/plugin/scc`` 的存储库中找到一个示例。

Existing chaincodes such as the QSCC can also serve as templates for certain
features - such as access control - that are typically implemented through system chaincodes.
The existing system chaincodes also serve as a reference for best-practices on
things like logging and testing.

诸如QSCC之类的现有链码也可以用作某些特征的模板 - 例如访问控制 - 通常通过系统链码实现。 现有的系统链码也可作为日志记录和测试等最佳实践的参考。

.. note:: On imported packages: the Go standard library requires that a plugin must
          include the same version of imported packages as the host application (fabric, in this case)

.. note:: 在导入的包上：Go标准库要求插件必须包含与主机应用程序相同的导入包版本（在本例中为fabric）

Configuring Plugins
-------------------
配置插件
-------------------

Plugins are configured in the ``chaincode.systemPlugin`` section in *core.yaml*:

插件在 *core.yaml* 的 ``chaincode.systemPlugin`` 部分中配置：

.. code-block:: bash

  chaincode:
    systemPlugins:
      - enabled: true
        name: mysyscc
        path: /opt/lib/syscc.so
        invokableExternal: true
        invokableCC2CC: true


A system chaincode must also be whitelisted in the ``chaincode.system`` section in *core.yaml*:

系统链码也必须在 *core.yaml* 的 ``chaincode.system`` 部分列入白名单：

.. code-block:: bash

  chaincode:
    system:
      mysyscc: enable


.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
