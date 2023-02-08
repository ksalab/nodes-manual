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

First, initialize a node configuration directory under `~/.babylond`. The `$NODENAME` variable specifies the name you aim to give your node.

```bash
babylond init $NODENAME --chain-id bbn-test-1
```

Then, retrieve the genesis file and place it in the node directory:

```bash
wget https://github.com/babylonchain/networks/raw/main/bbn-test1/genesis.tar.bz2
tar -xjf genesis.tar.bz2 && rm genesis.tar.bz2
mv genesis.json ~/.babylond/config/genesis.json
```

### Add seed nodes and persistent peers

Edit the configuration file at `~/.babylond/config/config.toml` and modify the `seeds` and `persistent_peers` attributes to contain appropriate seeds and peers of your choice. The full list of Babylon approved seeds and peers can be found under the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/new/main/node#network-information) page.

Edit the configuration file at `~/.babylond/config/app.toml` and modify the `btc-network` and `btc-tag` attributes to contain the BTC network parameters specified in the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/new/main/node#network-information) page.

> Note
>
> Addictional [peers](https://polkachu.com/testnets/babylon/peers)

### Setup cosmovisor

To install the latest version of Cosmovisor

```bash
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

Create the necessary directories

```bash
mkdir -p ~/.babylond
mkdir -p ~/.babylond/cosmovisor
mkdir -p ~/.babylond/cosmovisor/genesis
mkdir -p ~/.babylond/cosmovisor/genesis/bin
mkdir -p ~/.babylond/cosmovisor/upgrades
```

Copy the babylond binary into the `cosmovisor/genesis` folder

```bash
cp $GOPATH/bin/babylond ~/.babylond/cosmovisor/genesis/bin/babylond
```

Setup a cosmovisor service

```bash
sudo tee /etc/systemd/system/babylond.service > /dev/null <<EOF
[Unit]
Description=Babylon daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=babylond"
Environment="DAEMON_HOME=${HOME}/.babylond"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```

###Start the node

```bash
sudo -S systemctl daemon-reload
sudo -S systemctl enable babylond
sudo -S systemctl start babylond
```

You can check the status of the node by running

```bash
systemctl status babylond
```

## Getting Testnet Tokens

In this guide we will go through how you can create a keyring and request for tokens through the Babylon testnet faucet.

### Create a keyring

> NOTE
> 
> Currently, validators can only use the `test` keyring backend because they need to automatically submit transactions containing BLS signatures. In the future, Babylon will support other types of encrypted backends provided by the Cosmos SDK for validators.

One can create a keyring through the `babylond keys add` command. Full specification for this command can be found under the [CLI docs](https://docs.babylonchain.io/docs/cli/babylond/keys/babylondkeysdd).

```bash
# Replace the --keyring-backend argument with a backend of your choice
babylond --keyring-backend test keys add my-key
```

This will output an address and a memo. Record the memo as it is the only way to recover your key if it gets lost.

### Request funds from the Babylon testnet faucet

This can be accomplished by going to the Babylon [testnet faucet](https://faucet.testnet.babylonchain.io/) page to request funds by providing the address you created before.

## Becoming a Validator

In this guide we are going to go through the steps for someone to become a validator.

Prerequisites: Having a full node setup and synced by following this [guide](https://github.com/ksalab/nodes-manual/new/main/node#build-and-install-babylon)

### Create a Keyring and Get Funds

Validators are required to have funds for two reasons:

- They need to provide a self delegation
- They need to pay for transaction fees for submitting BLS signature transactions

> NOTE
> 
> Currently, validators can only use the `test` keyring backend. In the future, Babylon will support other types of encrypted backends provided by the Cosmos SDK for validators.

The [Getting Testnet Tokens]() page contains detailed instructions on how to create a keyring and get funds for it through a faucet.

