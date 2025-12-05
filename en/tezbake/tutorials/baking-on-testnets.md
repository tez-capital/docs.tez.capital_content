---
title: "Baking on Testnets"
weight: 1
type: docs
summary: Using TezBake to bake on the Tezos testnets
---

## Tezos Testnets

* Seoulnet is a simple and fast testnet with 20 minute cycles. It's a great way to quickly test your baker setup and get familiar with the Tezos baking process. Seoulnet will be removed once the Tallinn upgrade is activated on mainnet. Each protocol upgrade on mainnet usually comes with a new testnet that has the same features and really fast cycles.
* Ghostnet is the original Tezos testnet. It is a public testnet that is used by developers and users to test new features and applications before they are deployed on the mainnet. Ghostnet is a great way to get familiar with the Tezos ecosystem and to test your applications in a safe environment where the chain conditions are similar to mainnet.
* Shadownet is similar to Ghostnet but it is a private testnet that is used by developers to test new features and applications before they are deployed on the mainnet. Shadownet is not open to the public and is only accessible to developers who are working on Tezos projects. Shadownet bakers are centrally selected by the core Tezos development team. Setting up a testnet bakery here is possible but not encouraged.

## Preparation

Installing TezBake and using it to setup your Tezos baker is very simple. You will need the following tools:

1. Spare computer or existing computer or VM with Linux installed. We recommend Ubuntu Linux.
   > You must have an SSD drive or better & at least 8GB RAM

All advanced TezBake configurations, including TezSign block signing apply here as well.

---

> 🚨 Please note that running a DAL node is now a mandatory requirement for baking on the Tezos network.It's not currently mandatory to run the DAL on the same node as your node or signer. Read more about advanced DAL configurations here: [Baking with DAL](/tezbake/tutorials/baking-with-dal)
>
> What is the DAL anyway? The DAL acts like an overflow area for data, where large amounts of information can be kept available to the network without overloading the core blockchain. This means Tezos can safely handle far more transactions and complex operations, because the rollups can rely on the DAL to make their data available for everyone to verify​.

## Installation (All-in-one)

### Download and install tezbake

To begin, run the script below, which will download the latest version of TezBake and copy it to your `/usr/sbin` directory. This script works with both x86_64 and arm64 architectures.

   ```bash
   wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
   # you may be prompted for sudo password
   ```

### Setup Tezos node, signer, DAL and install tezbake dependencies

   ```bash
   # Seoulnet setup:
   tezbake setup --with-dal --node-configuration=https://raw.githubusercontent.com/tez-capital/xtz.configs/main/seoulnet.json
   # Ghostnet setup:
   tezbake setup --with-dal --node-configuration=https://raw.githubusercontent.com/tez-capital/xtz.configs/main/ghostnet.json
   # you may be prompted for sudo password
   ```

### Bootstrap Tezos node

At this stage, it's necessary to bootstrap your node, meaning to download a copy of the blockchain so you don't have to synchronize block-by-block, which takes hours at best.

   ```bash
   tezbake bootstrap-node <url> <block_hash>
   # Seoulnet example:
   tezbake bootstrap-node https://snapshots.tzinit.org/seoulnet/rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
   # Ghostnet example:
   tezbake bootstrap-node https://snapshots.eu.tzinit.org/ghostnet/rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
   ```

Get the block hash and block level from the snapshot provider's website:
<https://snapshots.eu.tzinit.org/ghostnet/rolling.html>

> The `<block_hash>` argument is optional but encouraged. If you don't want to borther with this protection, use the second method below which will also be faster.

Verify the hash/checksum provided by the snapshot provider to ensure the snapshot is valid. You can find the correct hashes for all blocks on Tezos blockchain explorers such as:
<https://tzkt.io/blocks>
<https://tzstats.com/>

Simply search for the block level in the search field and verify the hash of the block matches the hash provided by the snapshot provider.

### Start Tezos node

After importing the snapshot, you need to start your node and wait until it's fully synchronized before importing your Ledger key.

   ```bash
   tezbake start
   ```

After starting the node, run the following command over and over every few minutes and monitor the "level" displayed.

   ```bash
   tezbake info
   ```

> Level refers to the latest block number on mainnet. Navigate to <https://tzkt.io> or <https://tzstats.com> and observe the latest block. Once the level in your command matches the latest block on your blockchain explorer, your node is in full sync and you can keep following the steps below.
> Both <https://tzkt.io> or <https://tzstats.com> provide Ghostnet and Testnet block explorers as well. Make sure you're looking at the right explorer.

### Import baking keys and register as baker

Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### Import Soft key to TezBake

First, generate the baker key for TezBake:

   ```bash
   tezbake setup-soft-wallet --generate bls --key-alias baker
   ```

> Make sure to backup your key in a secure location and never share it.

You can get the secret/private key by running the following command:

   ```bash
   tezbake signer client show address baker --show-secret
   ```

### Register as baker on the Tezos blockchain Testnet

For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (__note__: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

To secure your XTZ security deposit on Ghostnet you can use the faucet to get some free XTZ. You can find the faucet at <https://faucet.ghostnet.teztnets.com/>

Each Tezos testnet has a faucet over at <https://teztnets.com>

   ```bash
   tezbake register-key
   ```

> Registering applies to new bakers and to inactive bakers. If you're setting up a new baker, you must register it. If you're setting up a baker that's been inactive for over 2 weeks, you must register it. If you're setting up a baker that's been inactive for less than 2 weeks, you don't need to register it. The best way to find out if you need to register your baker again is to look into your baking rights schedule and see if they stopped coming in. If they did, you need to register your baker again.

### Stake your baking XTZ security deposit

To bake on the Tezos network, you need to stake your XTZ security deposit. This is a slashable security deposit that you will get back when you stop baking. The minimum bond to get baking rights is currently set at 6000 XTZ.

You can stake your security deposit by running the following command, after opening your Ledger Tezos Wallet app:

   ```bash
   tezbake signer client stake 6000 for baker
   ```

> Change 6000 to the amount you want to stake. The minimum is 6000 XTZ. You may start baking with as little as 1000 XTZ but you will need to set your baking over staking multiplier to 5X and secure 5000 XTZ that will stake to your baker to cover the security deposit requirement. You can also secure delegators' XTZ to your baker to cover the security deposit requirement. Each XTZ delegated to your baker will be counted 0.5 towards the security deposit requirement. For example you can start baking with 1000 XTZ + 5000 XTZ secured from stakers, or you can start baking with 1000 XTZ + 10000 XTZ secured from delegators. You can also start baking with 1000 XTZ + 2000 XTZ secured from stakers + 6000 XTZ secured from delegators.

### Import your DAL attester profile

   ```bash
   tezbake update-dal-profiles <your-baker-tz4-key> --force
   ```

## Installation (Advanced)

You can install different TezBake components on different systems. For example, you can have the Signer, Node and DAL on 3 different machines, all communicating together.

Follow the steps in [Baking with Prism](/en/tezbake/tutorials/baking-with-prism.md)

As with everything in life, complexity adds more failure points. Only separate the TezBake components if you have a compelling use case.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
