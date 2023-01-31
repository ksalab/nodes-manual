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


### 1) add env
