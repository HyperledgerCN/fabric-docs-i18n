测试(Testing)
=======

单元测试(Unit test)
~~~~~~~~~
查看 :doc:`building Hyperledger Fabric <dev-setup/build>` 获得单元测试命令。

查看 `单元测试覆盖报告 <https://jenkins.hyperledger.org/view/fabric/job/fabric-merge-x86_64/>`__

为了查看一个包或者所有子包的覆盖率，替换 ``-cover`` 执行单元测试

::

    go test ./... -cover

为了查看一个包中哪一行没有被覆盖，用源代码生成一个带有覆盖率注释的html报告

::

    go test -coverprofile=coverage.out
    go tool cover -html=coverage.out -o coverage.html


系统测试(System test)
~~~~~~~~~~~

[WIP] ...即将到来

这个主题旨在包含推荐的测试场景，以及当前针对各种配置的性能数据

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/

