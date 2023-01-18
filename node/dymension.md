# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/dymension.png "DYMENSION")

### add env

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

### install go

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

### install git remote helper
```bash
curl https://get.gitopia.com | bash
```

### download and build binaries
