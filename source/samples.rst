Hyperledger Fabric 示例
==========================

.. note:: 如果您运行在 **Windows** 操作系统上，接下来的终端命令行，可以使用 Docker Quickstart Terminal，
          如果您之前没有安装，请访问 :doc:`prereqs` 页面。

          如果您在 Windows 7 或者 macOS 操作系统上使用 Docker Toolbox，
          需要在 ``C:\Users`` (Windows 7) 或者 ``/Users`` (macOS) 目录下安装和运行示例。

          如果您使用 Mac 版 Docker，示例需要位于 ``/Users``, ``/Volumes``, ``/private``, 或者 ``/tmp`` 目录下。
					如果需要使用不同的位置，请查询Docker文档中的 `文件共享 <https://docs.docker.com/docker-for-mac/#file-sharing>`__。

          如果您使用 Windows 版 Docker，请查询Docker文档中的 `共享驱动 <https://docs.docker.com/docker-for-windows/#shared-drives>`__，
          并使用使用其中一个共享驱动的位置。

在您的机器上选择一个存放 Hyperledger Fabric 示例应用程序仓库的位置，并在终端窗口中打开。然后，执行以下命令：

.. code:: bash

  git clone -b master https://github.com/hyperledger/fabric-samples.git
  cd fabric-samples
  git checkout {TAG}　

.. note:: 为了确保示例和您接下来将要下载的 Fabric 二进制文件版本兼容，请检查与您 Fabric 版本匹配的示例 ``{TAG}``，比如, v1.1.0。
          使用命令 "git tag" 可以查看所有 Fabric 示例标签的列表。

.. _binaries:

下载特定平台的二进制文件
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

接下来，我们将要安装 Hyperledger Fabric 特定平台的二进制文件。这个过程主要用于补充上述 Hyperledger Fabric 示例，但也可以被独立使用。
如果您不安装以上的示例，可以直接创建和输入目录，用于提取存放特定平台的二进制文件内容。

在您即将用于提取存放特定平台的二进制文件的目录下，请执行以下命令：

.. code:: bash

  curl -sSL https://goo.gl/6wtTN5 | bash -s 1.1.0

.. note:: 如果您在运行上述 curl 命令时遇到错误，您的 curl 版本可能太旧，不能处理重定向或者不能支持该环境。

	  请访问 :doc:`prereqs` 页面获取关于最新 curl 版本和正确环境的更多信息。或者，您可以用以下长地址了解其他信息:
	  https://github.com/hyperledger/fabric/blob/master/scripts/bootstrap.sh

.. note:: 对于任何一个已发布版本的 Hyperledger Fabric，您都可以使用以上命令，只需把 '1.1.0' 替换成您需要安装版本的标识符。

通过上述命令，下载和执行一个bash脚本，该脚本可以下载和提取，所有您需要用于设置网络的特定平台的二进制文件，并把他们放入您之前创建的克隆仓库中，
其中，包括四个特定平台的二进制文件：

  * ``cryptogen``,
  * ``configtxgen``,
  * ``configtxlator``,
  * ``peer``
  * ``orderer`` 和
  * ``fabric-ca-client``

他们会被放入您当前工作目录的 ``bin`` 子目录下。

您也许想要把这个目录加入到 PATH 环境变量中，这样可以在不完全符合每个二进制文件路径的情况下就找到他们，比如：

.. code:: bash

  export PATH=<path to download location>/bin:$PATH

最后，这个脚本将会从 `Docker Hub <https://hub.docker.com/u/hyperledger/>`__ 下载 Hyperledger Fabric docker 镜像，
加入您的本地 Docker 注册表，并标记他们为 'latest'。

结束之后，这个脚本会罗列出所有已经安装的 Docker 镜像。

看看每一个镜像的名字，这些都将是组成我们的 Hyperledger Fabric 网络的组件。同时，您会发现您有两个同一镜像ID的实例 -
一个标记为 "x86_64-1.x.x"，另一个标记为 "latest".

.. note:: 在不同的架构上，x86_64 会被用于标识您所用架构的字符串替换。

.. note:: 如果您有其他该文档未谈及的疑问，或者在任何一个教程中遇到问题，请您访问 :doc:`questions` 页面了解关于额外帮助的温馨提示。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
