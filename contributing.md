# 贡献参考流程

翻译流程如下，大家可以参考一下：

1. 首先fork本项目
2. 把fork的项目也就是你的项目clone到你的本地
3. 在命令行运行 `git branch develop` 来创建一个新分支
4. 运行 `git checkout develop` 来切换到新分支
5. 运行 `git remote add upstream https://github.com/HyperledgerCN/fabric-docs-i18n.git` 把我的库添加为远端库
6. 运行 `git remote update`更新
7. 运行 `git fetch upstream gh-pages` 拉取我的库的更新到本地
8. 运行 `git rebase upstream/gh-pages` 将我的更新合并到你的分支

这是一个初始化流程，只需要做一遍就行，之后请一直在develop分支进行修改。

如果修改过程中我的库有了更新，请重复6、7、8步。

修改之后，首先push到你的库，然后登录GitHub，在你的库的首页可以看到一个 `pull request` 按钮，点击它，填写一些说明信息，然后提交即可。

## 验证

在提交前需要进行简单的测试。有两种方式可以来进行测试

### 建立自己的staging repo并将翻译发布到公开的网站

1. fork 原英文文档 fabric on github
2. 从fork的项目中，到“setting”
3. 点击"Integrating & services"
4. 点击“Add service"下拉菜单
5. 选择 ReadTheDocs
6. 到 http://readthedocs.org 注册一个账户。选择连接到github
7. 点击"import a project"
8. 选择fork的项目
9. 填写项目名字，会生成readthedocs项目的URL
现在你可以在自己fork的项目里修改或者添加翻译文档，readthedocs生成的URL可以自动更新翻译的文档

### 在本地编译文档
下面的命令可以快速的在fork的项目下docs/build/html生成html文件，可以用浏览器直接打开验证翻译的文档

```
sudo pip install Sphinx
sudo pip install sphinx_rtd_theme
cd fabric/docs # Be in this directory. Makefile sits there.
make html
```
你也可以用下面的命令启动一个本地的web服务器，并通过`http://localhost/index.html`来浏览翻译的文档：

```
sudo apt-get install apache2
cd build/html
sudo cp -r * /var/www/html/
```

