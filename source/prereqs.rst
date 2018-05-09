预备知识
=============

安装 cURL
------------

如果您还没有安装cURL工具，或者当您从文件中运行curl命令遇到错误提示时，请下载最新版本的`cURL
<https://curl.haxx.se/download.html>`__ 工具。

.. note:: 如果您使用的是Windows操作系统，请参照下文中关于 `Windows 附加条件`_ 的特定注解.

Docker 和 Docker Compose
-------------------------

请在您用于运行、开发、或其他方式使用Hyperledger Fabric的平台上安装以下内容：

  - MacOSX, \*nix, 或者 Windows 10： `Docker <https://www.docker.com/products/overview>`__
    Docker 17.06.2-ce 或者更高版本。
  - 旧版 Windows： `Docker
    Toolbox <https://docs.docker.com/toolbox/toolbox_install_windows/>`__ -
    同样的, Docker 版本需要 Docker 17.06.2-ce 或者更高版本。

您可以在终端提示符中通过以下命令，检查您所安装的Docker版本：

.. code:: bash

  docker --version

.. note:: 在为Mac或者Windows安装Docker或者Docker Toolbox时，也会安装Docker Compose。
         如果您已经安装了Docker，请务必检查是否同时安装了Docker Compose 1.14.0 或者更高版本。
         如果没有，我们建议您安装更新版本的Docker。

您可以在终端提示符中通过以下命令，检查您所安装的Docker Compose版本：

.. code:: bash

  docker-compose --version

.. _Golang:

Go 编程语言
-----------------------

Hyperledger Fabric 在很多组件中使用Go 1.9.x 版本的编程语言。

.. note:: 不支持使用Go 1.8.x 版本。

  - `Go <https://golang.org/>`__ - 1.9.x 版本

既然我们写的是Go链码（Chaincode）程序，我们需要确保源代码位于``$GOPATH``树目录中的某处。首先，您需要检查是否已经设置``$GOPATH``环境变量。

.. code:: bash

  echo $GOPATH
  /Users/xxx/go

当您echo ``$GOPATH``时，如果什么都没有显示，您需要设置这个环境变量。
通常来说，如果您的开发工作空间（development workspace）中有一个子树目录（directory tree child），
这个变量值就是这个目录，或者也可以是您$HOME目录下的子目录。由于我们将要用Go语言进行一系列编码，
您也许需要将如下内容添加至配置文件``~/.bashrc``：

.. code:: bash

  export GOPATH=$HOME/go
  export PATH=$PATH:$GOPATH/bin

Node.js 运行环境和 NPM
-----------------------

如果您打算使用Hyperledger Fabric的Node.js SDK为Hyperledger Fabric开发应用程序，您需要安装Node.js 8.9.x版本。

.. note:: 暂时不支持Node.js 9.x 版本。

  - `Node.js <https://nodejs.org/en/download/>`__ - 8.9.x 或更高版本

.. note:: 安装 Node.js 时同时需要安装 NPM, 但是，建议您先确认已经安装的NPM版本。您可以通过以下命令，升级 ``npm`` 工具：

.. code:: bash

  npm install npm@5.6.0 -g

Python
^^^^^^

.. note:: 以下内容仅面向 Ubuntu 16.04 的用户。

默认情况下，Ubuntu 16.04 已经安装 Python 3.5.1 作为其 ``python3`` 的二进制版本。
但是，Fabric Node.js SDK 需要迭代 Python 2.7 版本，用于成功运行 ``npm install``命令，
建议通过以下命令，获取2.7版本：

.. code:: bash

  sudo apt-get install python

请检查您的版本号：

.. code:: bash

  python --version


Windows 附加条件
--------------

如果在Windows 7操作系统上做开发，您可以在 Docker Quickstart Terminal 中工作，它使用 `Git Bash
<https://git-scm.com/downloads>`__ ，提供了一个除了内置Windows shell的更好替代。

然而，经验显示，这个开发环境功能比较局限，它适用于运行基于Docker的场景，比如:doc:`getting_started`，
但是当运行包含``make`` 和 ``docker``的命令时，您可能会遇到困难。

在Windows 10 操作系统上，您应该使用原生的Docker分发版本，您也可能需要使用 Windows PowerShell。
但是，为了让 :ref:`binaries` 命令成功运行，您还是需要有可用的 ``uname`` 命令。
您可以通过其作为Git的一部分而得到它，但是需要注意的是，它只支持64-bit的版本。

在运行任何``git clone``命令之前，请运行以下命令：

::

    git config --global core.autocrlf false
    git config --global core.longpaths true

您可以通过以下命令，检查这些变量的设置：

::

    git config --get core.autocrlf
    git config --get core.longpaths

这些设置需要分别为 ``false`` 和 ``true``。

依附Git和Docker Toolbox的``curl`` 命令是旧版的，不能够正常处理 :doc:`getting_started` 中使用的重定向。
请确保您安装和使用`cURL 下载页面 <https://curl.haxx.se/download.html>`__中的较新版本。

对于 Node.js，您也需要必要的 Visual Studio C++ Build Tools，这是免费的工具，可以通过以下命令安装：

.. code:: bash

	  npm install --global windows-build-tools

请参照 `NPM windows-build-tools 页面
<https://www.npmjs.com/package/windows-build-tools>`__ 获取更多细节。

当完成以上任务之后，您还需要通过以下命令，安装 NPM GRPC 模块：

.. code:: bash

	  npm install --global grpc

到此为止，您的环境应该已经准备好运行 :doc:`getting_started` 中的示例和教程。

.. note:: 如果您有其他该文档未谈及的疑问，或者在任何一个教程中遇到问题，请您访问 :doc:`questions` 页面了解关于额外帮助的温馨提示。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
