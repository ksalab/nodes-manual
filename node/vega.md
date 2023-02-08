# ![logo](https://raw.githubusercontent.com/ksalab/nodes/main/logo/vega-logo.png "VEGA") ![logo text](https://raw.githubusercontent.com/ksalab/nodes/main/logo/vega-logo-text.png "VEGA")

## Create wallet

### Install and run Vega Wallet

#### Download file

Download and save the zip file from Vega Wallet software [releases ↗](https://github.com/vegaprotocol/vegawallet/releases/). Keep track of where you've saved the file, because that's where the command line interface will look for it.

Download `vegawallet-linux-amd64.zip` and unzip it

```bash
wget https://github.com/vegaprotocol/vegawallet/releases/download/v0.16.1/vegawallet-linux-amd64.zip
unzip vegawallet-linux-amd64.zip
```

YOU MAY NEED TO CHANGE YOUR SYSTEM PREFERENCES TO RUN THE FILE

```bash
chmod +x vegawallet
```

### Generate new wallet

#### Initialise the software

The `init` command will initialise the software the first time you use it. This creates the folders and the configuration files needed by the software to operate.

```bash
./vegawallet init
```

#### Create your wallet

Next, create a wallet by giving it **a name and passphrase**.

This step will:

- create your first public and private key
- show your wallet's recovery phrase (save this immediately)

Replace `MY_WALLET_NAME` (below) with your chosen wallet name:

```bash
./vegawallet create --wallet "MY_WALLET_NAME"
```

It will then prompt you to **input a passphrase**, and then **confirm that passphrase**. You'll use this wallet name and passphrase to login to the token site and Vega Console.

> Warning
>
> Keep your recovery phrase safe and secret. You will need it to import your keys.
>
> **Your recovery phrase is only shown once ever and cannot be recovered. DO NOT SHARE YOUR RECOVERY PHRASE**.

