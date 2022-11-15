# ![alt text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/gitopia-logo-white.svg "GITOPIA")

### add env

```
echo '# GITOPIA section start' >> $HOME/.bash_profile
GITOPIA_NODENAME=ksalab
echo 'export GITOPIA_NODENAME='$GITOPIA_NODENAME >> $HOME/.bash_profile
GITOPIA_NUM=2
echo "export GITOPIA_WALLET=wallet" >> $HOME/.bash_profile
echo "export GITOPIA_CHAIN_ID=gitopia-janus-testnet-2" >> $HOME/.bash_profile
echo "export GITOPIA_PORT=${GITOPIA_PORT}" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### install git remote helper
```
curl https://get.gitopia.com | bash
```

### install go

```
ver="1.19.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

### download and build binaries

```
cd $HOME && rm -rf gitopia
git clone -b v1.2.0 gitopia://gitopia/gitopia && cd gitopia
make install
```

### config
```
gitopiad config chain-id $GITOPIA_CHAIN_ID
gitopiad config keyring-backend test
gitopiad config node tcp://localhost:${GITOPIA_NUM}6657
```

### init
```
gitopiad init $GITOPIA_NODENAME --chain-id $GITOPIA_CHAIN_ID
```

### download genesis and addrbook
```
wget -O $HOME/.gitopia/config/addrbook.json "http://65.108.6.45:8000/gitopia/addrbook.json"
wget https://server.gitopia.com/raw/gitopia/testnets/master/gitopia-janus-testnet-2/genesis.json.gz
gunzip genesis.json.gz
mv genesis.json $HOME/.gitopia/config/genesis.json
```

### set peers and seeds
```
SEEDS="399d4e19186577b04c23296c4f7ecc53e61080cb@seed.gitopia.com:26656"
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gitopia/config/config.toml
```

### set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${GITOPIA_NUM}6658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${GITOPIA_NUM}6657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${GITOPIA_NUM}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${GITOPIA_NUM}6656\"%;s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${GITOPIA_NUM}6660\"%" $HOME/.gitopia/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${GITOPIA_NUM}317\"%; s%^address = \":8080\"%address = \":${GITOPIA_NUM}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${GITOPIA_NUM}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${GITOPIA_NUM}091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:${GITOPIA_NUM}545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:${GITOPIA_NUM}546\"%" $HOME/.gitopia/config/app.toml
```

### config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gitopia/config/app.toml
```

### set minimum gas price and timeout commit
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0utlore\"/" $HOME/.gitopia/config/app.toml
```

### enable prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.gitopia/config/config.toml
```

### reset
```
gitopiad tendermint unsafe-reset-all --home $HOME/.gitopia
```

### create service
```
sudo tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=gitopia
After=network-online.target
[Service]
User=$USER
ExecStart=$(which gitopiad) start --home $HOME/.gitopia
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

### start service
```
sudo systemctl daemon-reload
sudo systemctl enable gitopiad
sudo systemctl restart gitopiad
```

# Post installation
```
source $HOME/.bash_profile
```

### +v(Check the status of your node and ctrl + c)
```
gitopiad status 2>&1 | jq .SyncInfo
```

## Create wallet (or use existing wallet - Keplr)

### (Please save all keys on your notepad)
```
gitopiad keys add $GITOPIA_WALLET
```

### To recover your old (existing) wallet use this command
```
gitopiad keys add $GITOPIA_WALLET --recover
```

### show keys
```
gitopiad keys list
```

### Add wallet and valoper address and load variables into the system
```
GITOPIA_WALLET_ADDRESS=$(gitopiad keys show $GITOPIA_WALLET -a)
GITOPIA_VALOPER_ADDRESS=$(gitopiad keys show $GITOPIA_WALLET --bech val -a)
echo 'export GITOPIA_WALLET_ADDRESS='${GITOPIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export GITOPIA_VALOPER_ADDRESS='${GITOPIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Fund your wallet (to create validator) TB you can also use web faucet link

### Create validator (after recieving of tokens and must important sync is false)
### replace with your wallet name and with your validator name
```
gitopiad tx staking create-validator \
 --amount 1000000utlore \
 --from $GITOPIA_WALLET \
 --commission-max-change-rate "0.01" \
 --commission-max-rate "0.2" \
 --commission-rate "0.07" \
 --min-self-delegation "1" \
 --pubkey  $(gitopiad tendermint show-validator) \
 --moniker $GITOPIA_NODENAME \
 --chain-id $GITOPIA_CHAIN_ID
 ```
 
### check your validator key
```
[[ $(gitopiad q staking validator $GITOPIA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(gitopiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

## Get list of validators
```
gitopiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Get currently connected peer list with ids
```
curl -sS http://localhost:${GITOPIA_NUM}6657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

## Delegate stake
```
gitopiad tx staking delegate $GITOPIA_VALOPER_ADDRESS 10000000utlore --from=$GITOPIA_WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

## Check logs
```
journalctl -fu gitopiad -o cat
```

## Start service
```
sudo systemctl start gitopiad
```

## Stop service
```
sudo systemctl stop gitopiad
```

## Restart service
```
sudo systemctl restart gitopiad
```

## Synchronization info
```
gitopiad status 2>&1 | jq .SyncInfo
```

## Validator info
```
gitopiad status 2>&1 | jq .ValidatorInfo
```

## Node info
```
gitopiad status 2>&1 | jq .NodeInfo
```

## Show node id
```
gitopiad tendermint show-node-id
```

## Edit validator
```
gitopiad tx staking edit-validator \
    --moniker=$GITOPIA_NODENAME \
    --identity=<your_keybase_id> \
    --website="<your_website>" \
    --details="<your_validator_description>" \
    --chain-id=$GITOPIA_CHAIN_ID \
    --from=$WALLET
```

## Unjail validator
```
gitopiad tx slashing unjail \
    --broadcast-mode=block \
    --from=$GITOPIA_WALLET \
    --chain-id=$GITOPIA_CHAIN_ID \
    --gas=auto
```

## Delete node
```
sudo systemctl stop gitopiad
sudo systemctl disable gitopiad
sudo rm /etc/systemd/system/gitopia* -rf
sudo rm $(which gitopiad) -rf
sudo rm $HOME/.gitopia* -rf
sudo rm $HOME/gitopia -rf
sed -i '/GITOPIA_/d' ~/.bash_profile
```

