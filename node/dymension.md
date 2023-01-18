# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/dymension.png "DYMENSION")

### 1) add env

```bash
echo '# DYMENSION section start' >> $HOME/.bash_profile
DYMENSION_NODENAME=node_name
echo 'export DYMENSION_NODENAME='$DYMENSION_NODENAME >> $HOME/.bash_profile
DYMENSION_NUM=2
echo "export DYMENSION_WALLET=wallet" >> $HOME/.bash_profile
echo "export DYMENSION_CHAIN_ID=local-testnet" >> $HOME/.bash_profile
echo "export DYMENSION_PORT=${DYMENSION_NUM}" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### 2) install go

```bash
ver="1.19.5"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

#### check version go
```bash
go version
```

### 3) install binaries

#### clone dymension
```bash
git clone https://github.com/dymensionxyz/dymension.git --branch v0.1.0-alpha
cd dymension
make install
```

#### check that the dymd binaries have been successfully installed
````bash
dymd version
````

should return "latest". If the dymd command is not found an error message is returned, confirm that your [GOPATH](https://go.dev/doc/gopath_code#GOPATH) is correctly configured by running the following command

````bash
export PATH=$PATH:$(go env GOPATH)/bin
````

### 4) initializing dymd
Set the following variables:
````bash
export CHAIN_ID="local-testnet"
export KEY_NAME="local-user"
export MONIKER_NAME="local"
````

