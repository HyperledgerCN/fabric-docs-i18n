入门指南
===============

.. toctree::
   :maxdepth: 1

   prereqs
   samples

安装前提
^^^^^^^^^^^^^^^^^^^^^

在我们开始前，请您按照:doc:`prereqs`文档里的要求，准备您即将用于开发区块链应用以及（或者）
运行Hyperledger Fabric的平台。


安装二进制文件和Docker镜像
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

在我们为Hyperledger Fabric二进制文件（Bineries）开发真实的安装包时，
我们为您的系统提供了一个可以 :ref:`binaries` 的脚本。
这个脚本也会把Docker镜像（Images）下载到您的本地注册表（Local Registry）。


Hyperledger Fabric 示例
^^^^^^^^^^^^^^^^^^^^^^^^^^

我们在教程中提供了一组示例应用程序，您可以在开始教程前，安装这些:doc:`samples` 。


API 文档
^^^^^^^^^^^^^^^^^

Hyperledger Fabric 的 Golang API 文档存放在 godoc 网站中的
 `Fabric <http://godoc.org/github.com/hyperledger/fabric>`_.
如果您计划使用这些API做开发，可以即刻收藏这些链接。


Hyperledger Fabric SDKs
^^^^^^^^^^^^^^^^^^^^^^^

Hyperledger Fabric致力于为各种编程语言提供SDK，首先发布的两款SDK是面向 Node.js 和 Java，
我们希望在1.0.0版本发布之后，尽快提供面向Python和Go的SDK。

  * `Hyperledger Fabric Node SDK 文档 <https://fabric-sdk-node.github.io/>`__.
  * `Hyperledger Fabric Java SDK 文档 <https://github.com/hyperledger/fabric-sdk-java>`__.


Hyperledger Fabric CA
^^^^^^^^^^^^^^^^^^^^^

Hyperledger Fabric 提供一个可选的`证书授权服务 <http://hyperledger-fabric-ca.readthedocs.io/en/latest>`_
用于生成证书和密钥，以帮助您配置和管理区块链网络中的身份信息。但是，您也可以使用任何一个可以生成ECSDA证书的证书授权（CA）。


.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
