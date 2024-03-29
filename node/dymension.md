# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/dymension.png "DYMENSION")

### 1) add env

```bash
echo '# DYMENSION section start' >> $HOME/.bash_profile
DYMENSION_MONIKER=node_name
echo 'export DYMENSION_MONIKER='$DYMENSION_MONIKER >> $HOME/.bash_profile
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

check version go
```bash
go version
```

### 3) install binaries

clone dymension
```bash
git clone https://github.com/dymensionxyz/dymension.git --branch v0.1.0-alpha
cd dymension
make install
```

check that the dymd binaries have been successfully installed
````bash
dymd version
````

should return "latest". If the dymd command is not found an error message is returned, confirm that your [GOPATH](https://go.dev/doc/gopath_code#GOPATH) is correctly configured by running the following command

````bash
export PATH=$PATH:$(go env GOPATH)/bin
````

### 4) initializing dymd
When starting a node you need to initialize a chain with a user:
````bash
dymd init "$DYMENSION_MONIKER" --chain-id "$DYMENSION_CHAIN_ID"
dymd keys add "$DYMENSION_WALLET" --keyring-backend test
dymd add-genesis-account "$(dymd keys show "$DYMENSION_WALLET" -a --keyring-backend test)" 100000000000stake
dymd gentx "$DYMENSION_WALLET" 100000000stake --chain-id "$DYMENSION_CHAIN_ID" --keyring-backend test
dymd collect-gentxs
````

Now start the chain!
```bash
dymd start
````

You should have a running local node! Let's run a sample transaction.
Keep the node running and open a new tab in the terminal. Let's get your validator consensus address.

### 5) interacting with the node
````bash
dymd tendermint show-address
````

This returns an address with the prefix "dymvalcons" or the dymension validator consensus address.
To further learn about the dymension protocol please visit our brief overview on [how dymension works](https://docs.dymension.xyz/learn/modular-intro), our more in-depth [litepaper](https://docs.dymension.xyz/dymension-litepaper/dymension-litepaper-index) and our tutorial on how to [create a RollApp on top of dymension's settlement layer](https://docs.dymension.xyz/developers/checkers-rollapp/).
