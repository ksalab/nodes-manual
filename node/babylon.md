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

Retrieve the Babylon source code either through the [releases page](https://github.com/babylonchain/babylon/releases) or by cloning the [source code](https://github.com/babylonchain/babylon). In case you want to create a full node for the testnet, it is highly recommended that you choose the release corresponding to the current testnet. See the [testnet network](https://github.com/ksalab/nodes-manual/blob/main/node/babylon.md#network-information) information page for more details.
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
> This guide requires having Babylon [installed](https://github.com/ksalab/nodes-manual/new/main/node#build-and-install-babylon) on a Linux System. The instructions can be found on the Installation page The version to install is specified at the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/blob/main/node/babylon.md#network-information) page.

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

Edit the configuration file at `~/.babylond/config/config.toml` and modify the `seeds` and `persistent_peers` attributes to contain appropriate seeds and peers of your choice. The full list of Babylon approved seeds and peers can be found under the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/blob/main/node/babylon.md#network-information) page.

Edit the configuration file at `~/.babylond/config/app.toml` and modify the `btc-network` and `btc-tag` attributes to contain the BTC network parameters specified in the [bbn-test-1 network info](https://github.com/ksalab/nodes-manual/blob/main/node/babylon.md#network-information) page.

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

### Start the node

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

The [Getting Testnet Tokens](https://github.com/ksalab/nodes-manual/blob/main/node/babylon.md#getting-testnet-tokens) page contains detailed instructions on how to create a keyring and get funds for it through a faucet.

### Create a BLS key

Validators are expected to submit a BLS signature at the end of each epoch. To do that, a validator needs to have a BLS key pair to sign information with.

Using the address that you created on the previous step (`$ADDR` variable):

```bash
babylond create-bls-key $ADDR
```

This command will create a BLS key and add it to the `~/.babylond/config/priv_validator_key.json`. This is the same file that stores the private key that the validator uses to sign blocks. Please ensure that this file is secured properly.

After creating a BLS key, you need to restart your node to load this key into memory. If you followed the [setting up a node guide](https://github.com/ksalab/nodes-manual/new/main/node#build-and-install-babylon), you would have to

```bash
sudo systemctl stop babylond
sudo systemctl start babylond
```

### Modify the configuration

A Babylon validator needs to send BLS signature transactions at the end of each epoch. This process is done automatically through the Babylon codebase which identifies which key to use from the `~/.babylond/config/client.toml` file. Edit this file and set the keyring backend that you're using. In this guide's case:

```bash
keyring-backend = "test"
```

Furthermore, you need to specify the name of the key that the validator will be using to submit BLS signature transactions under the `~/.babylond/config/app.toml` file. Edit this file and set the key name to the one that holds funds on your keyring:

```bash
key-name = "val-key" # replace with your key name
```

Finally, it is strongly recommended to modify the timeout_commit value under `~/.babylond/config/config.toml`. This value specifies how long a validator will wait before commiting a block before starting on a new height. More information can be found here. Given that Babylon aims to have a 10 second time between blocks, set this value to:

```bash
timeout_commit = "10s"
```

### Create the validator

Contrary to a vanilla Cosmos SDK chain, a validator for Babylon is created through the `babylond tx checkpointing create-validator` command. This command expects that a BLS validator key exists under the `~/.babylond/config/priv_validator_key.json` and has the same parameters as the `babylond tx staking create-validator` command.

To create the validator (using sample parameters):

```bash
# Note the variables
# - $AMOUNT in ubbn, e.g. 10000000ubbn
# - $CHAIN_ID the chain ID
# - $VAL_KEY the name of the key (with a test keyring backend) used for the validator
babylond tx checkpointing create-validator \
    --amount="$AMOUNT" \
    --pubkey=$(babylond tendermint show-validator) \
    --moniker="My Validator" \
    --chain-id=$CHAIN_ID \
    --gas="auto" \
    --gas-adjustment=1.2 \
    --gas-prices="0.0025ubbn" \
    --keyring-backend=test \
    --from=$VAL_KEY \
    --commission-rate="0.10" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation="1"
```

> INFO
> 
> Note: In order to become an active validator, you need to have more `ubbn` tokens bonded than the last validator ordered by the tokens bonded (or the validator set to not be full) as well as have at least `10000000ubbn` bonded.

### Verify your validator

On the Babylon system, one can become a validator only after an epoch ends. For the testnet, an epoch lasts for around 30 minutes.

To verify that you have become a validator, first find your validator address:

```bash
babylond keys show $KEYNAME -a --bech val
```

where `$KEYNAME` is the name of the key that you used for the self-delegation (e.g. `val-key` on our example). This will return an address which you can use as the `$ADDR` variable to perform the following query:

```bash
babylond query staking validator $ADDR
```

If all goes well, you should see a response indicating the parameters that you specified on the `create-validator` transaction.

After the epoch ends and if you have enough stake to be an active validator, performing this query will return you a status `BOND_STATUS_BONDED`. Congrats! You are now a validator on the Babylon system.

## Becoming a Babylon Vigilante

Vigilante programs are standalone programs that are run along-side with Babylon nodes. People run a vigilante program in three modes, `reporter`, `submitter`, and `monitor`, each responsible to different aspects to the security of the Babylon network.

### Prerequisites

First we need to have the access to a synced Babylon full node and a synced BTC full node. To run a BTC full node, please follow the [instructions of running a `bitcoind` full node]() or the [instructions of running a `btcd` full node]() (an alternative full node bitcoin implementation written in Golang). We support the connections to both. To run a Babylon full node, please follow this [guide](https://github.com/ksalab/nodes-manual/new/main/node#build-and-install-babylon).

We specify the following paths:

```bash
BABYLON_PATH="path_where_babylon_is_built" # example: $HOME/Projects/Babylon/babylon
VIGILANTE_PATH="root_vigilante_dir" # example: $HOME/Projects/Babylon/vigilante
TESTNET_PATH="path_where_the_testnet_files_will_be_stored" # example: $HOME/Projects/Babylon/babylon/.testnet
```

### Configuration

Create a directory which will store the vigilante configuration, copy the sample vigilante configuration into a `vigilante.yml` file, and adapt it to the specific requirements.

Currently, the vigilante configuration should be edited manually. In the future, we will add functionality for generating this file through a script. For Docker deployments, we have created the `sample-vigilante-docker.yaml` file which contains a configuration that will work out of this box for this guide.

```bash
cp sample-vigilante.yml $VIGILANTE_PATH/vigilante.yml
nano $VIGILANTE_PATH/vigilante.yml # edit the config file to replace $TESTNET instances
```

## Deploy Vigilante Locally

### Reporter

Run the vigilante reporter

```bash
go run $VIGILANTE_PATH/cmd/main.go reporter \
         --config $VIGILANTE_PATH/vigilante.yml \
         --babylon-key $BABYLON_KEY_DIR
```

where `$BABYLON_KEY_DIR` stores the keyring that will be used to submit BTC headers and checkpoints to Babylon.

### Submitter

Similarly, run the vigilante submitter

```bash
go run $VIGILANTE_PATH/cmd/main.go submitter \
         --config $VIGILANTE_PATH/vigilante.yml
```

### Monitor

The monitor mode requires the genesis file of Babylon as an additional parameter, which is stored under $BABYLON_NODE_PATH/config/genesis.json. So, run the vigilante monitor

```bash
go run $VIGILANTE_PATH/cmd/main.go monitor \
         --genesis $BABYLON_NODE_PATH/config/genesis.json
         --config $VIGILANTE_PATH/vigilante.yml
```

## Deploy Vigilante Using Docker

### Reporter

Initially, build a Docker image named `babylonchain/vigilante-reporter`

```bash
p sample-vigilante-docker.yml $VIGILANTE_PATH/vigilante.yml
make reporter-build
```

Afterwards, run the above image and attach the directories that contain the configuration for Babylon, Bitcoin, and the vigilante.

```bash
docker run --rm \
         -v $TESTNET_PATH/bitcoin:/bitcoin \
         -v $BABYLON_NODE_PATH:/babylon \
         -v $VIGILANTE_PATH:/vigilante \
         babylonchain/vigilante-reporter
```

### Submitter

Follow the same steps as above, but with the `babylonchain/vigilante-submitter` Docker image.

```bash
docker run --rm \
         -v $TESTNET_PATH/bitcoin:/bitcoin \
         -v $BABYLON_NODE_PATH:/babylon \
         -v $VIGILANTE_PATH:/vigilante \
         babylonchain/vigilante-submitter
```

### Monitor

Follow the same steps as above, but with the `babylonchain/vigilante-monitor` Docker image.

```bash
docker run --rm \
         -v $TESTNET_PATH/bitcoin:/bitcoin \
         -v $BABYLON_NODE_PATH:/babylon \
         -v $VIGILANTE_PATH:/vigilante \
         babylonchain/vigilante-monitor
```

### buildx

The above Dockerfiles are also compatible with Docker's [buildx feature](https://docs.docker.com/desktop/multi-arch/) that allows multi-architectural builds. To have a multi-architectural build,

```bash
docker buildx create --use
make reporter-buildx  # for the reporter
make submitter-buildx # for the submitter
make monitor-buildx # for the monitor
```

