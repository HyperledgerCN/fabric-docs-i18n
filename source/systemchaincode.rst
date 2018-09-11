System Chaincode Plugins
========================
ϵͳ������
========================

System chaincodes are specialized chaincodes that run as part of the peer process
as opposed to user chaincodes that run in separate docker containers. As
such they have more access to resources in the peer and can be used for
implementing features that are difficult or impossible to be implemented through
user chaincodes. Examples of System Chaincodes are ESCC (Endorser System Chaincode)
for endorsing proposals, QSCC (Query System Chaincode) for ledger and other
fabric related queries and VSCC (Validation System Chaincode) for validating
a transaction at commit time respectively.

ϵͳ��������Ϊ�ڵ���̵�һ�������е�ר�����룬�������ڵ�������docker���������е��û����롣��ˣ����ǿ��Ը���ط��ʽڵ�����е���Դ����������ʵ�����Ի򲻿���ͨ���û�����ʵ�ֵĹ��ܡ�ϵͳ����������У�ESCC(Proendorser System Chaincode)����֧�����飬QSCC(QuerySystem Chaincode)���ڷ����˺�������fabric��صĲ�ѯ��VSCC(ValidationSystem Chaincode)�����ύʱ����֤����

Unlike a user chaincode, a system chaincode is not installed and instantiated
using proposals from SDKs or CLI. It is registered and deployed by the peer at start-up.

���û����벻ͬ��ϵͳ���벻��ʹ��SDK��CLI����������װ��ʵ��������������ʱ�ɽڵ�ע��Ͳ���

System chaincodes can be linked to a peer in two ways: statically and dynamically
using Go plugins. This tutorial will outline how to develop and load system chaincodes
as plugins.

ϵͳ�������ͨ�����ַ�ʽ���ӵ��ڵ㣺��̬�غͶ�̬��ʹ��GO��������̳̽�������ο����ͼ���ϵͳ������Ϊ�����

Developing Plugins
------------------
�������
------------------ 

A system chaincode is a program written in `Go <https://golang.org>`_ and loaded
using the Go `plugin <https://golang.org/pkg/plugin>`_ package.

ϵͳ�������� `Go <https://golang.org>`_ ��д�ĳ��򣬲���ʹ��Go  `plugin <https://golang.org/pkg/plugin>`_ ��������ء�

A plugin includes a main package with exported symbols and is built with the command
``go build -buildmode=plugin``.

�������һ�����е������ŵ���������ʹ������ ``go build -buildmode = plugin`` ������

Every system chaincode must implement the `Chaincode Interface <http://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#Chaincode>`_
and export a constructor method that matches the signature ``func New() shim.Chaincode`` in the main package.
An example can be found in the repository at ``examples/plugin/scc``.

ÿ��ϵͳ���붼����ʵ�� `Chaincode Interface <http://godoc.org/github.com/hyperledger/fabric/core/chaincode/shim#Chaincode>`_ ������һ���������е�ǩ�� ``func New() shim.Chaincode`` ƥ��Ĺ��캯�������� ������ ``examples/plugin/scc`` �Ĵ洢�����ҵ�һ��ʾ����

Existing chaincodes such as the QSCC can also serve as templates for certain
features - such as access control - that are typically implemented through system chaincodes.
The existing system chaincodes also serve as a reference for best-practices on
things like logging and testing.

����QSCC֮�����������Ҳ��������ĳЩ������ģ�� - ������ʿ��� - ͨ��ͨ��ϵͳ����ʵ�֡� ���е�ϵͳ����Ҳ����Ϊ��־��¼�Ͳ��Ե����ʵ���Ĳο���

.. note:: On imported packages: the Go standard library requires that a plugin must
          include the same version of imported packages as the host application (fabric, in this case)

.. note:: �ڵ���İ��ϣ�Go��׼��Ҫ�����������������Ӧ�ó�����ͬ�ĵ�����汾���ڱ�����Ϊfabric��

Configuring Plugins
-------------------
���ò��
-------------------

Plugins are configured in the ``chaincode.systemPlugin`` section in *core.yaml*:

����� *core.yaml* �� ``chaincode.systemPlugin`` ���������ã�

.. code-block:: bash

  chaincode:
    systemPlugins:
      - enabled: true
        name: mysyscc
        path: /opt/lib/syscc.so
        invokableExternal: true
        invokableCC2CC: true


A system chaincode must also be whitelisted in the ``chaincode.system`` section in *core.yaml*:

ϵͳ����Ҳ������ *core.yaml* �� ``chaincode.system`` ���������������

.. code-block:: bash

  chaincode:
    system:
      mysyscc: enable


.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/
