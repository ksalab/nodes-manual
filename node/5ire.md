# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/5ire.png "5irechain")

## Overview

The Validator Node Setup is an instructional guide for installing a node on the 5irechain network. This guide provides comprehensive instructions, including visual aids, for setting up the node on a Linux operating system.

## What is a node?

A node in a blockchain network is a device or program that participates in the network by maintaining a copy of the blockchain and contributing to the consensus process. The node serves as an entry gate to the blockchain network and the developer application. Blockchain is a network of nodes interconnected with one another. The node provides API for validating, managing accounts, sending transactions, interacting with smart contracts, and more.

## System Requirements

### Minimum Requirements:

- CPU:
  - Core: 4
  - Threads: 8
- RAM: 8GB
- Storage: 100GB SSD
- Software:
  - Linux Version : 18.04 LTS or above
  - Yarn: Version 1.22.19 or above
  - Docker: Version 20.10.10 or above

### Recommended Requirements:

- CPU:
  - Core: 8
  - Threads: 16
- RAM: 32GB
- Storage: 100GB SSD
- Software:
  - Linux Version : 18.04 LTS or above
  - Yarn: Version 1.22.19 or above
  - Docker: Version 20.10.10 or above

**Protocols** If the system firewall is enabled, the Node host must make the following ports (TCP) accessible:

9944 : For web sockets 9933 : For RPC call

Best Practices:

- Allow only minimum ports
  - 30333: For Blockchain discovery
  - 9944: For web sockets
  - 9933: For RPC call
  - 22: For SSH
- Mount extra volume for blockchain storage
- Assign Static Public IP to instance/VM
- Monitoring Alarm on blockchain process and extra volume

**Important Note**: It is recommended to set up the validator node on a cloud server instead of your local system. Nodes can only work on Intel processors. Run a validator node if you want to onboard as a valdiator.


## Installation Process

### Guidelines & Precautions:

For a smooth experience, it is recommended to run the validator node in the background. You can use the "Screen Application" for this purpose.

The "Screen Application" is a program that allows running multiple shell sessions in the command line, similar to having multiple tabs open in a browser.

Using screens has unique benefits such as the ability to 'detach' from a session without terminating any ongoing process, switch between sessions and resume where you left off. If a session is terminated, all work is kept intact. By using the screen environment, work can be accessed from multiple devices.

To learn about guidelines for using the Screen Application, please follow the links below

For [Linux](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-screen-on-an-ubuntu-cloud-server) | For [Mac](https://formulae.brew.sh/formula/screen)

### Running a Node

The following steps below are for installing full nodes or validators via Docker:

**Step 1**: Download the Docker image, using the following command:

```bash
docker pull 5irechain/5ire-thunder-node:0.12
```

**Step 2**: Run the Docker image

For a full node, run the following command:

```bash
docker run -p 30333:30333 -p 9933:9933 -p 9944:9944 5irechain/5ire-thunder-node:0.10 --port 30333  --ws-external --ws-port 9944 --rpc-external --rpc-port 9933 --rpc-cors all --no-telemetry --name my-5ire-full-node --bootnodes /ip4/3.19.122.7/tcp/30333/p2p/12D3KooWNLQPtTkKwapACfMas7vyM4gujwnevxfgXUyTGno3bDpY --pruning archive
```

For a validator node, run the following command:

```bash
docker run -p 30333:30333 5irechain/5ire-thunder-node:0.10 --port 30333 --no-telemetry --name my-5ire-validator --bootnodes /ip4/3.19.122.7/tcp/30333/p2p/12D3KooWNLQPtTkKwapACfMas7vyM4gujwnevxfgXUyTGno3bDpY --pruning archive --validator
```

*Note: the command line above runs the Docker image on several ports (-p = ports)
Port 30333:30333 - Relay Chain
Port 9933:9933 - RPC Node
Port 9944:9944 - Web Sockets*

## How to onboard a validator

### Overview

Validators are crucial to the security & upkeep of the network, as they validate transactions, facilitate the creation of new blocks and participate in 5ire consensus with other validators. By following this guide, you can use the 5irechain validator application to onboard the network as a validator and contribute to the overall stability of the blockchain.

### Prerequisites

Before proceeding, ensure that you have successfully set up your 5ire Validator Node. The link for instructions on how to do this is given below:

[How to setup a 5ireChain Node](https://github.com/ksalab/nodes-manual/blob/main/node/5ire.md#installation-process)

### Who are validators
A validator is a participant in a 5irechain consensus (POF) that is responsible for maintaining the security of the distributed ledger by validating transactions and adding them to the 5ireChain. They are in charge of creating new blocks, as well as ensuring the network's finality and, ultimately, its security.

Validators are incentivized to participate in the network through the use of a native cryptocurrency (5ire in our chain) and are required to "stake" a certain amount (Min. 200 5IRE in our case) of coins to become a validator.

The stake acts as a guarantee of good conduct, and whenever they make a mistake, this stake is slashed. In contrast, when they follow the rules, they are rewarded according to their contribution.

It is important to note that being a validator on a live network comes with a significant level of responsibility. Not only is the validator responsible for their own stake, but they are also responsible for the stake of their nominators. Any mistakes made by the validator can result in a slashing of stake amount and reputation.

## What are the minimum requirements to become a validator?

### Stake

The minimum requirement to become a validator on the 5ireChain is 200 5ire coins. This means that as a minimum, you will need enough 5ire coins to set up as a validator plus a little extra for transaction fees. The rest can come from nominators.

Note: The minimum stake of 200 5ire is required for 5irechain testnet network - Thunder

### Validator Node

It is mandatory to set up your 5irechain Validator Node to be a validator. Please follow this link to set up your node.

In case you already have a 5irechain valdiator node setup, please proceed this document for further instructions.

### Validator Application

You must access the Validator Application to become a Validator.

[5irechain valdiator application link](https://validator.5ire.network)

You will be redirected to the application by clicking the provided link where you must enter their “Node Information” into the node information input field in order to establish a connection between their node and the application.

Format for entering the Node Information:

protocol://IP:port

Example: ws://127.0.0.1:9944

Example: wss://www.example.com

Note: The "wss" protocol uses WebSocket over a secure TLS/SSL connection, while the "ws" protocol uses an unencrypted connection.

## Accessing the application

### Blocks Page

The blocks page displays the most recent block details, as well as the name of the validator validating a particular block, block height, block hash, and the block's timestamp.

![image]()
