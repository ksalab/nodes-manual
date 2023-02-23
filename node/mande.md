# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/mande-text.webp "Mande")

## Overview

Please go through the [whitepaper](https://drive.google.com/file/d/17EScDNUlaYT1Xiera20x8rYsmI3ejggj/view) to understand the mechanics

## What is a node?

# Joining Testnet

## System Requirements
- Two or more CPU cores
- At least 100 GB of disk storage
- At least 4 GB of memory
** HDD not recommended **

## Binaries

Download our binaries from [here](https://github.com/mande-labs/testnet-1/blob/main/mande-chaind) and place it in your bin path. Ex: /usr/local/bin/

```bash
wget -O mande-chaind https://github.com/mande-labs/testnet-1/blob/main/mande-chaind
chmod +x mande-chaind
```

## Generate keys

```bash
mande-chaind keys add [key_name]
```

or

```bash
mande-chaind keys add [key_name] --recover
```

to regenerate keys with your BIP39 mnemonic

## Claim testnet coins

```bash
curl -d '{"address":"mande...<mande wallet address>"}' -H 'Content-Type: application/json' http://35.224.207.121:8080/request
```

# Setting up a Node

Following steps are rudimentary way of setting up a validator, For production we advise your sentry architecture to create well defined process

- Initialize node

```bash
mande-chaind init {{NODE_NAME}} --chain-id mande-testnet-1
```

- Replace the contents of your `${HOME}/.mande-chaind/config/genesis.json` with that of genesis file on [this](https://github.com/mande-labs/testnet-1/blob/main/genesis.json) repo
- Verify checksum `jq -S -c -M "" genesis.json | sha256sum` matches `def6850afe2cb311b7909cdc9bfb6dd436b36a6fc015c3d524270a5cff050dfe`
- Inside file `${HOME}/.mande-chaind/config/config.toml`,
  - set `seeds` to `"cd3e4f5b7f5680bbd86a96b38bc122aa46668399@34.171.132.212:26656"`.
  - set `persistent_peers` to `"ee8a1b98e931e81d32c52f0b489fa22b52778d7c@34.171.132.212:26656,6780b2648bd2eb6adca2ca92a03a25b216d4f36b@34.170.16.69:26656"`
  - If your node has a public ip, set it in `external_address = "tcp://<public-ip>:26656"`, else leave the filed empty.
- Set `minimum-gas-prices` in `${HOME}/.mande-chaind/config/app.toml` with the minimum price you want (example `0.005mand`) for the security of the network.
- Start node

```bash
mande-chaind start
```

