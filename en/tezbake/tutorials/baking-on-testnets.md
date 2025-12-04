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

Installing TezBake CLI and using it to setup your Tezos baker is very simple. You will need the following tools:

1. Spare computer or existing computer with Linux installed. We recommend Ubuntu Linux.
   > You must have an SSD drive or better & at least 8GB RAM
2. Ledger Nano S Plus or Ledger Nano X hardware wallet with Tezos Wallet & Baker apps installed.

   > It's necessary to use Ledger Live to install the Tezos Wallet & Baking applications; to install the latter you must enable developer mode in Ledger Live settings
   > Absolutely ensure you have properly configured the Ledger device settings so the device doesn't lock 10 minutes after inactivity. This is a common issue that can cause your baker to stop baking.

   ![<Ledger locking settings](/tezbake/tutorial/tezbakeLedgerLock.png)

   > Remove the Ledger wallet app from your baking device after you've finished setting up your baker. This is a security measure to prevent unauthorized access to your baker. You can always reinstall the app when you need to use the Ledger for baking or other purposes.

---

> 🚨 Please note that running a DAL node is now a mandatory requirement for baking on the Tezos network. After completing the instructions here, head over to [Baking with DAL](/tezbake/tutorials/baking-with-dal) to complete the setup.
> What is the DAL anyway? The DAL acts like an overflow area for data, where large amounts of information can be kept available to the network without overloading the core blockchain. This means Tezos can safely handle far more transactions and complex operations, because the rollups can rely on the DAL to make their data available for everyone to verify​.

## Installation

### Download and copy tezbake

To begin, run the script below, which will download the latest version of TezBake and copy it to your `/usr/sbin` directory. This script works with both x86_64 and arm64 architectures.

   ```bash
   wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
   # you may be prompted for sudo password
   ```

### Setup Tezos node, signer and install tezbake dependencies

   ```bash
   # Seoulnet setup:
   tezbake setup --node-configuration=https://raw.githubusercontent.com/tez-capital/xtz.configs/main/seoulnet.json
   # Ghostnet setup:
   tezbake setup --node-configuration=https://raw.githubusercontent.com/tez-capital/xtz.configs/main/ghostnet.json
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

### Import Ledger key or soft key and register as baker

Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You have the option to use the secure Ledger hardware wallet or simply use a local, unencrypted software key (a.k.a. soft key). The secure Ledger hardware wallet is the recommended option for mainnet baking.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### (Option 1 - RECOMMENDED) Import Ledger key to TezBake signer

Using a Ledger hardware wallet is a secure and cost effective way to bake on the Tezos network. The Ledger device is a secure element that stores your private key and signs transactions on the device itself. This means your private key never leaves the device and is never exposed to the internet.

In order to get the most out of your Ledger, it's now recommended to use the P-256 curve (tz3) for baking. This curve is faster than the default ed25519 curve (tz1). If you're setting up a baker that already exists on the network, you can use the same curve as before. If you're setting up a new baker, it's recommended to use the P-256 curve.

You can import your Ledger key by running the following command:

   ```bash
   tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --hwm 1
   ```

> If you have a custom derivation path, you can specify it as shown: (`--import-key="ed25519/0h/0h"`; the default is ed25519/0h/0h which works just fine but is not as fast as P-256 or secp256k1.
> `--hwm 1` works great if you're setting up for the first time. If you're setting up a device that's been used to bake before, you want to change this (`1`) to the current block height on the blockchain for your safety.
> If you're importing for the second time after already trying again but failing, you can use `--force` to force the import.
> Once imported, you can see your baker address by running `tezbake info`
> The ledger will ask you twice to confirm this operation. Make sure the baker you see on the ledger screen matches the one you want to use. If you don't have this information yet, don't worry. To get the address of the ledger that's used by default simply go to <https://gov.tez.capital> and login with ledger, accepting the default derivation path.
> Putting the baker on a non-default derivation path provides an additional layer of security for your baker at the cost of extra complexity for you. Make sure your setup is clearly documented for your own records.
> If your device was used to bake before it might have a "high watermark" aka HWM. If you try to use this device on a testnet, it will not work because the block height on test networks usually starts with 1 while mainnet is up to over a couple of million blocks at the time of writing.
If you used to bake on mainnet with the same ledger as you're trying to use now but it's been a while, it's highly recommended to change the 1 above to the current block on the network that will be used for the device going forward.
> The watermark is simply a record of the lack block number your ledger helped to bake or attest. If you're setting up a brand new device that's not been used for baking before, there is no need to alter the default command above.
> Always make sure you're not accidentally going to double bake by using your production ledger and/or setup to bake on a testnet. It's really easy to make this mistake and the only thing preventing it are your personal standard operating procedures, the documentation you keep, and the care you take when setting up your baker.
> To double bake or attest due to baker setup error means having 2 different bakers with the same key on the same network. This is a serious offense and can lead to loss of bond and other penalties. Always double-check your setup and make sure you're not accidentally double baking or attesting.

#### (Option 2 - INSECURE) Import Soft key to TezBake signer

First, generate the baker key for TezBake signer:

   ```bash
   tezbake setup-soft-wallet
   ```

> Make sure to backup your key in a secure location and never share it.

You can get the secret/private key by running the following command:

   ```bash
   tezbake signer client show address baker --show-secret
   ```

### Register as baker on the Tezos blockchain Ghostnet

For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (__note__: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

To secure your XTZ security deposit on Ghostnet you can use the faucet to get some free XTZ. You can find the faucet at <https://faucet.ghostnet.teztnets.com/>

   ```bash
   tezbake register-key
   ```

> Registering is not necessary if this is already an active baker ledger which is being setup on some kind of failover machine or in a situation where it has not been over 2 weeks of baking inactivity.
> Registering applies to new bakers and to inactive bakers. If you're setting up a new baker, you must register it. If you're setting up a baker that's been inactive for over 2 weeks, you must register it. If you're setting up a baker that's been inactive for less than 2 weeks, you don't need to register it. The best way to find out if you need to register your baker again is to look into your baking rights schedule and see if they stopped coming in. If they did, you need to register your baker again.

### Stake your baking XTZ security deposit

To bake on the Tezos network, you need to stake your XTZ security deposit. This is a slashable security deposit that you will get back when you stop baking. The minimum bond to get baking rights is currently set at 6000 XTZ.

You can stake your security deposit by running the following command, after opening your Ledger Tezos Wallet app:

   ```bash
   tezbake signer client stake 6000 for baker
   ```

> Change 6000 to the amount you want to stake. The minimum is 6000 XTZ. You may start baking with as little as 1000 XTZ but you will need to set your baking over staking multiplier to 5X and secure 5000 XTZ that will stake to your baker to cover the security deposit requirement. You can also secure delegators' XTZ to your baker to cover the security deposit requirement. Each XTZ delegated to your baker will be counted 0.5 towards the security deposit requirement. For example you can start baking with 1000 XTZ + 5000 XTZ secured from stakers, or you can start baking with 1000 XTZ + 10000 XTZ secured from delegators. You can also start baking with 1000 XTZ + 2000 XTZ secured from stakers + 6000 XTZ secured from delegators.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
