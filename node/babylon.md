# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/babylon.webp "BABYLON")

## Installation Guide

### Install Golang

> NOTE
> 
> Babylon requires Golang [version 1.19](https://go.dev/doc/install) for Babylon to be installed on your system. Install it using the instructions on the provided link.

After you install it, you can verify that you have the proper version by running:

```bash
$ go version
go version go1.19 linux/amd64
```

### Build and install Babylon

You need to clone Babylon’s GitHub repository to install the `babylond` executable.

Install build requirements

```bash
sudo apt install git build-essential ufw curl jq snapd --yes
```

Retrieve the Babylon source code either through the [releases page](https://github.com/babylonchain/babylon/releases) or by cloning the [source code](https://github.com/babylonchain/babylon). In case you want to create a full node for the testnet, it is highly recommended that you choose the release corresponding to the current testnet. See the [testnet network](https://docs.babylonchain.io/docs/testnet/network-information) information page for more details.
Navigate to the directory that contains the Babylon source code. From there build and install the babylond executable

```bash
git checkout <version_to_install>
make install
```

> NOTE
>
> The last command first executes `git checkout` in the specific version that you want to install. Ensure that you install the same version of the Babylon executable as the one that is running on the network you aim to join.

### Network Information

#### bbn-test-1 network

Babylon Version: [v0.5.0](https://github.com/babylonchain/babylon/tree/v0.5.0)

Seed Nodes: The following are maintained by the Babylon foundation

- `03ce5e1b5be3c9a81517d415f65378943996c864@18.207.168.204:26656`
- `a5fabac19c732bf7d814cf22e7ffc23113dc9606@34.238.169.221:26656`

RPC Nodes: rpc.testnet.babylonchain.io:26657

BTC Network Params

Tag: `bbn0`

Network: `mainnet`

## Setting Up a Full Node

This is a guide for setting up a full node for the Babylon system.

> NOTE
> 
> This guide requires having Babylon [installed](https://github.com/ksalab/nodes-manual/new/main/node#build-and-install-babylon) on a Linux System. The instructions can be found on the Installation page The version to install is specified at the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/new/main/node#network-information) page.

### Initialize the node directory

