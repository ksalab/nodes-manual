# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/andromeda.png "ANDROMEDA")

## Installation Guide

Here you have to put name of your moniker (validator) that will be visible in explorer

```bash
NODENAME="YOUR_NODE_MONIKER"
echo "export NODENAME=$NODENAME" >> ~/.bash_profile
source ~/.bash_profile
```

### Install dependencies, if needed

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl build-essential git wget jq make gcc tmux chrony lz4 unzip
```

### Install Golang

```bash
ver="1.20"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

### Download and build binaries

```bash
cd $HOME && rm -rf andromedad
git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad
git checkout galileo-3-v1.1.0-beta1 
make install
```

### Create service

```bash
sudo tee /etc/systemd/system/andromedad.service > /dev/null <<EOF
[Unit]
Description= Andromeda Network Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which andromedad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable andromedad
```

### Config app

```bash
ANDROMEDA_PORT=14
echo "export ANDROMEDA_CHAIN_ID=galileo-3" >> $HOME/.bash_profile
echo "export ANDROMEDA_PORT=${ANDROMEDA_PORT}" >> $HOME/.bash_profile
source $HOME/.bash_profile

andromedad config chain-id $ANDROMEDA_CHAIN_ID
andromedad config keyring-backend test
andromedad config node tcp://localhost:${ANDROMEDA_PORT}657
andromedad init $NODENAME --chain-id $ANDROMEDA_CHAIN_ID
```

### Download Genesis and Addrbook (updates every: 1h)

```bash
wget https://snapshot.yeksin.net/andromeda/genesis.json -O $HOME/.andromedad/config/genesis.json
wget https://snapshot.yeksin.net/andromeda/addrbook.json -O $HOME/.andromedad/config/addrbook.json
```

### Set seeds and peers

```bash
SEEDS=""
PEERS="06d4ab2369406136c00a839efc30ea5df9acaf11@10.128.0.44:26656,43d667323445c8f4d450d5d5352f499fa04839a8@192.168.0.237:26656,29a9c5bfb54343d25c89d7119fade8b18201c503@192.168.101.79:26656,6006190d5a3a9686bbcce26abc79c7f3f868f43a@37.252.184.230:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.andromedad/config/config.toml
```

### Config pruning, set minimum gas price, enable prometheus and reset chain data

```bash
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.andromedad/config/app.toml

sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.andromedad/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uandr\"/" $HOME/.andromedad/config/app.toml
```

### Set custom ports

```bash
coming soon...
```

### Download Snapshot

```bash
curl -L https://snapshot.yeksin.net/andromeda/data.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.andromedad
```

### Start service

```bash
sudo systemctl start andromedad
```

### Check logs

```bash
sudo journalctl -u andromedad -f -o cat
```
