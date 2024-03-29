# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/quasar.png "QUASAR")

### Hardware Requirements
These are the suggested specs. Given we are still in the testnet phase, you may decide to cut down a little.

- 4 or more physical CPU cores
- At least 500GB of SSD disk storage
- At least 16GB of memory
- At least 100mbps network bandwidth

### How to Install Quasar

Binary installation

> (Currently tested in Ubuntu 22)

1) Download the binary corresponding to the testnet version from here

```bash
mkdir quasar
cd quasar
wget https://github.com/quasar-finance/binary-release/raw/main/v0.0.2-alpha-11/quasarnoded-linux-amd64 -O quasarnoded
```

check version

```bash
./quasar version
```

> 0.0.2-alpha-11

2) Put it in your desired folder (it is useful to have it in your PATH)

```bash
echo "export PATH=$PATH:$HOME/quasar" >> ~/.bash_profile
source ~/.bash_profile
cd $HOME
```

### How to join the chain as a full node

1) Initialize the node files with

```bash
quasarnoded init < node_moniker > --chain-id < qsr-internal-questnet-01/02/03/etc >
```

> Current chain-id: `qsr-questnet-04`

2) Download the genesis file from the corresponding version, e.g:

```bash
wget https://github.com/quasar-finance/questnet/blob/main/v03/definitive-genesis.json -O $HOME/.quasarnoded/config/genesis.json
```

3) Set the persistent_peers parameter in the `~/.quasarnoded/config/config.toml` with the ones listed in the `persistent_peers.txt` file in the repo (remove the node itself from the list if needed) example file for questnet v02

#### Config pruning, set minimum gas price, enable prometheus and reset chain data

```bash
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.quasarnoded/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.quasarnoded/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.quasarnoded/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.quasarnoded/config/app.toml

sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.quasarnoded/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uqsr\"/" $HOME/.quasarnoded/config/app.toml
```

#### Create wallet

```bash
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Add New Key

```bash
quasarnoded keys add $WALLET
```

Recover Existing Key

```bash
quasarnoded keys add $WALLET  --recover
```

Create service

```bash
sudo tee /etc/systemd/system/quasard.service > /dev/null <<EOF
[Unit]
Description=quasar
After=network-online.target
[Service]
User=$USER
ExecStart=$(which quasarnoded) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

4) Start the quasar service. If you installed the systemd unit, it should be something like

```bash
sudo systemctl daemon-reload
sudo systemctl enable quasard
sudo systemctl restart quasard
```

Log

```bash
sudo journalctl -f -u quasard -o cat --no-hostname
```

#### This will make the node sync its state with the chain.

> **This will take several hours**

More info [here](https://hub.cosmos.network/main/hub-tutorials/join-testnet.html)

> Extra: You can promote your node to validator following this guide

### Create validator (after recieving of tokens and must important sync is false)

replace with your wallet name and with your validator name

```bash
quasarnoded tx staking create-validator \
 --amount 1000000uqsr \
 --from wallet \
 --commission-max-change-rate "0.1" \
 --commission-max-rate "0.2" \
 --commission-rate "0.1" \
 --min-self-delegation "1" \
 --pubkey  $(quasarnoded tendermint show-validator) \
 --moniker ksalab \
 --identity "3BDA27DB2D54D59B" \
 --chain-id qsr-questnet-04 \
 -y
```

