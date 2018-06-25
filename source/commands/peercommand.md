# peer 命令

## Description 描述

 The `peer` command has five different subcommands, each of which allows
 administrators to perform a specific set of tasks related to a peer.  For
 example, you can use the `peer channel` subcommand to join a peer to a channel,
 or the `peer  chaincode` command to deploy a smart contract chaincode to a
 peer.

 `peer` 命令有五个子命令，每一个都可以让管理员执行与peer节点相关的特定任务集。例如，
 你可以使用 `peer channel` 子命令来把peer加入channel，或者使用`peer  chaincode`
 命令在peer节点上部署智能合约chaincode。

## Syntax 语法

The `peer` command has five different subcommands within it:

`peer` 命令内部包含如下五个不同的子命令:
```
peer chaincode [option] [flags]
peer channel   [option] [flags]
peer logging   [option] [flags]
peer node      [option] [flags]
peer version   [option] [flags]
```

Each subcommand has different options available, and these are described in
their own dedicated topic. For brevity, we often refer to a command (`peer`), a
subcommand (`channel`), or subcommand option (`fetch`) simply as a **command**.

每一个子命令各自有不同的可用参数选项，这些选项由它们专用的主题描述。为简便起见，我们经常提到
(`peer`)来代指一个命令，或者(`channel`)就指一个子命令，或者只提到子命令的选项如(`fetch`)就
 是指一个**命令**.

If a subcommand is specified without an option, then it will return some high
level help text as described in the `--help` flag below.

如果一个子命令没有指定选项，它会返回一些上层help文本信息，就像加上`--help` 标签一样的效果。

## Flags

Each `peer` subcommand has a specific set of flags associated with it, many of
which are designated *global* because they can be used in all subcommand
options. These flags are described with the relevant `peer` subcommand.

The top level `peer` command has the following flags:

* `--help`

  Use `--help` to get brief help text for any `peer` command. The `--help` flag
  is very useful -- it can be used to get command help, subcommand help, and
  even option help.

  For example
  ```
  peer --help
  peer channel --help
  peer channel list --help

  ```
  See individual `peer` subcommands for more detail.

* `--logging-level <string>`

  This flag sets the logging level for a peer when it is started.

  There are six possible values for  `<string>` : `debug`, `info`, `notice`,
  `warning`, `error`, and `critical`.

  If `logging-level` is not explicitly specified, then it is taken from the
  `CORE_LOGGING_LEVEL` environment variable if it is set. If
  `CORE_LOGGING_LEVEL` is not set then the file `sampleconfig/core.yaml` is used
  to determined the logging level for the peer.

  You can find the current logging level for a specific component on the peer by
  running `peer logging getlevel <component-name>`.

* `--version`

  Use this flag to show detailed information about how the peer was built. This
  flag cannot be applied to `peer` subcommands or their options.

## Usage

Here's some examples using the different available flags on the `peer` command.

* Using the `--help` flag on the `peer channel join` command.

  ```
  peer channel join --help

  Joins the peer to a channel.

  Usage:
    peer channel join [flags]

  Flags:
    -b, --blockpath string   Path to file containing genesis block

  Global Flags:
        --cafile string                       Path to file containing PEM-encoded trusted certificate(s) for the ordering endpoint
        --certfile string                     Path to file containing PEM-encoded X509 public key to use for mutual TLS communication with the orderer endpoint
        --clientauth                          Use mutual TLS when communicating with the orderer endpoint
        --keyfile string                      Path to file containing PEM-encoded private key to use for mutual TLS communication with the orderer endpoint
        --logging-level string                Default logging level and overrides, see core.yaml for full syntax
    -o, --orderer string                      Ordering service endpoint
        --ordererTLSHostnameOverride string   The hostname override to use when validating the TLS connection to the orderer.
        --tls                                 Use TLS when communicating with the orderer endpoint
    -v, --version                             Display current version of fabric peer server

  ```
  This shows brief help syntax for the `peer channel join` command.

* Using the `--version` flag on the `peer` command.

  ```
  peer --version

  peer:
   Version: 1.1.0-alpha
   Go version: go1.9.2
   OS/Arch: linux/amd64
   Experimental features: false
   Chaincode:
    Base Image Version: 0.4.5
    Base Docker Namespace: hyperledger
    Base Docker Label: org.hyperledger.fabric
    Docker Namespace: hyperledger

  ```

  This shows that this peer was built using an alpha of Hyperledger Fabric
  version 1.1.0, compiled with GOLANG 1.9.2. It can be used on Linux operating
  systems with AMD64 compatible instruction sets.
