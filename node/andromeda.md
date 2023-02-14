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

