# 自述文件

## 介绍
本文档包含了 Fabric 文档如何建立、发布以及在修改文档前应注意的约定事项等信息。

本文档核心部分采用 [reStructuredText](http://docutils.sourceforge.net/rst.html) 
编写，并可使用 [Sphinx](http://www.sphinx-doc.org/en/stable/) 转换至HTML格式。
HTML文件发布在 http://hyperledger-fabric.readthedocs.io上。该站点使用了钩子程序，
主仓库 `docs/source` 目录下的任何新内容都会触发一次对该文档新的构建与发布。

## 约定事项

* 源文件为 RST 格式，保存在 `docs/source` 目录下。
* 主入口点为 index.rst。要在目录中增加内容时只需用和其他主题一样的方式修改即可。
  当你阅读后就会发现，该文件的自解释性非常好。
* 尽可能多的使用相对链接。它的推荐语法为：  :doc:\`锚文本 &lt;相对路径&gt;\`
<br/>不要把后缀 .rst 加到文件路径的最后
* 对于非 RST 文件，例如纯文本、MD 或 YAML 文件等，将其链接到 github 上的文件，例如： https://github.com/hyperledger/fabric/blob/master/docs/README.md

注意：以上约定信息意味着我们需要依赖于 github 镜像仓库。
当通过 RST 文件浏览时，相对链接无法在 github 上正常使用。

## 建立环境

和修改生产版本一样，对文档的任何修改都需要通过构建文档方式来测试。
你可以用两种方式来做：
建立你自己的暂存库与发布站点或在你本机上构建文档。下文会对这两种方式进行介绍：

### 建立你自己的暂存库与发布站点

根据以下步骤，你可以很容易的建立起自己的暂存库：

1. 创建 [github 上的 fabirc](https://github.com/hyperledger/fabric) 分支
1. 在你自己分支上，在屏幕右上角进入 `settings`
1. 点击 `Integration & services`
1. 点击 `Add service` 下拉菜单,
1. 向下滚动至 ReadTheDocs
1. 然后，前往 http://readthedocs.org 注册一个账号。首批提示中有一个会提供与 github 的链接。选择这一项
1. 点击导入一个项目
1. 在选项中导航至你的分支（例如 yourgithubid/fabric）
1. 系统会询问你这个项目的名字。可凭自己的直觉想一个。这个名字会出现在 URL 的开头，所以你可能会需要在该名字后增加 `-fabric` 以便于和你给其他项目创建的文档进行区分，例如： `yourgithubid-fabric.readthedocs.io/en/latest` 

现在，只要当你在自己的分支上修改或增加文档内容，这个 URL 就会将这些内容自动更新！

### 在本机构建文档

以下是从 fabric 主目录开始、不依赖于 ReadTheDocs、在本机上构建文档的简单步骤。
注意：你可能需要根据你的操作系统进行一些调整。

```
sudo pip install Sphinx
sudo pip install sphinx_rtd_theme
cd fabric/docs # Be in this directory. Makefile sits there.
make html
```

以上步骤会在 `docs/build/html` 目录下生成所有的html文件。你可以用浏览器在本地浏览。
当然，每次修改文档后你都需要重新执行 `make
html` 。


另外，如果你愿意，你也可以通过以下命令（或你操作系统的类似命令）在本地运行一个 web 服务器：


```
sudo apt-get install apache2
cd build/html
sudo cp -r * /var/www/html/
```

然后你就可以通过 `http://localhost/index.html` 来访问这些 html 文件。

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />本文授权基于 <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">知识共享 署名 4.0 国际协议</a>。
