---
title: "How to Bake on Ghostnet"
weight: 1
type: docs
summary: Using TezBake to bake on the Ghostnet Tezos perpetual testnet
---

## Ghostnet

Ghostnet is Tezos' perpetual testnet. In the context of TezBake, Ghostnet is
intended to provide a no-consequences testing environment where you can 
learn how to properly bake and pay using TezBake and TezPay. It's highly
recommended for all serious bakers to have a Ghostnet setup running, to
help test protocol migrations.

## Preparation

Installing BakeBuddy CLI (bb-cli) and using it to setup your Tezos baker is very simple. You will need the following tools:


1. Spare computer or existing computer with Linux installed. We recommend Ubuntu Linux.
   (note: you must have an SSD drive or better & at least 8GB RAM)
2. Ledger Nano S hardware wallet with Tezos Wallet & Baker apps installed.
   (note: it's necessary to use Ledger Live to install the Tezos Wallet & Baking applications; to install the latter you must enable developer mode in Ledger Live settings)

---

## Installation

### Download and copy bb-cli
To begin, download the latest bb-cli version for your Linux desktop and copy it to a universally available location so you can execute BB-CLI command from any location. Using the commands below, typed or pasted one-by-one is the intended order of operations.
If you have a regular Intel or AMD computer that's not "ARM" based.

   ```
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/bb-cli-linux-amd64 && chmod +x bb-cli-linux-amd64
   sudo mv bb-cli-linux-amd64 /usr/sbin/bb-cli
   # you may be prompted for sudo password; execute 1 line at a time
   ```
   
If you have a Raspberry Pi or you're running Linux on macOS M1 architecture:

   ```
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/bb-cli-linux-arm64 && chmod +x bb-cli-linux-arm64
   sudo mv bb-cli-linux-arm64 /usr/sbin/bb-cli
   # you may be prompted for sudo password; execute 1 line at a time
   ```

### Setup Tezos node, signer and install bb-cli dependencies

   ```
   bb-cli setup -a --node-configuration=https://gitlab.com/groktech/xtz.configs/-/raw/main/ghostnet.json
   # you may be prompted for sudo password
   ```

### Bootstrap Tezos node
At this stage, it's necessary to bootstrap your node, meaning to download a copy of the blockchain so you don't have to synchronize block-by-block, which takes hours at best. With the import method shown below, bootstrapping could take as little as 5 minutes.
  
   ```
   bb-cli bootstrap-node --tarball
   ```

### Start Tezos node
After importing the snapshot, you need to start your node and wait until it's fully synchronized before importing your Ledger key.

   ```
   bb-cli start
   ```

After starting the node, run the following command over and over every few minutes and monitor the "level" displayed.
   
   ```
   bb-cli info
   ```

Level refers to the latest block number on mainnet. Navigate to https://tzkt.io or https://tzstats.com and observe the latest block. Once the level in your command matches the latest block on your blockchain explorer, your node is in full sync and you can keep following the steps below.

### Import Ledger key
Now that your node is in full sync, you can import your Ledger key. You will need to connect your Ledger and enter your Ledger PIN. Then open the Tezos Baking app.

   ```
   bb-cli import-key --derivation-path="ed25519/0h/0h"
   # If you have a custom derivation path, you can change ed to bip as shown (--derivation-path="bip25519/3h/6h/9h")
   ```

The ledger will ask you twice to confirm this operation. Make sure the baker you see on the ledger screen matches the one you want to use. If you don't have this information yet, don't worry. To get the address of the ledger that's used by default simply go to https://kukai.app and login with ledger, accepting the default derivation path.


bip is supposed to be more strong in the long term than ed25519, which is the default one. Including it in the command above as shown does not change the default behavior but it shows which path is default so it can be customized for preference and security. 

Putting the baker on a non-default derivation path provides an additional layer of security for your baker at the cost of extra complexity for you. Make sure your setup is clearly documented for your own records.

### Authorize Ledger to bake for key
Having imported your Ledger key to your signer, it's time to authorize your Ledger to bake for this key.

   ```
   bb-cli setup-ledger --main-hwm 1
   # Pay careful attention to the value of --main-hwm

   ```

If your device was used to bake before it might have a "high watermark" aka HWM. If you try to use this device on a testnet, it will not work because the block height on test networks usually starts with 1 while mainnet is up to over a couple of million blocks at the time of writing.
If you used to bake on mainnet with the same ledger as you're trying to use now but it's been a while, it's highly recommended to change the 1 above to the current Tezos block, which can be found at.

The watermark is simply a record of the lack block number your ledger helped to bake or endorse. If you're setting up a brand new device that's not been used for baking before, there is no need to alter the default command above.

Always make sure you're not accidentally going to double bake by using your production ledger and/or setup to bake on a testnet. It's really easy to make this mistake and the only thing preventing it are your personal standard operating procedures.

### Register Ledger key as baker on the blockchain
For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker. For all other operations, you must use the Tezos Baking app.

   ```
   bb-cli register-key
   ```

   Registering is not necessary if this is already an active baker ledger which is being setup on some kind of failover machine or in a situation where it has not been over 2 weeks of actively baking.
