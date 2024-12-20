---
title: "> Baking on Mainnet"
weight: 1
type: docs
summary: TezBake Baking Tutorial
---

## Preparation

Installing TezBake CLI and using it to setup your Tezos baker is very simple. You will need the following tools:


1. Spare computer or existing computer with Linux installed. The recommended requirements are provided by [Nomadic Labs](https://research-development.nomadic-labs.com/paris-announcement.html#10s-block-times-bring-lower-latency-and-faster-finality)
    * 3 CPU cores (arm64 or amd64/x86-64 architectures) – 2 are needed by the Octez node and 1 is needed by the Octez baker;
    * 8GB RAM + 8GB swap (or 16GB RAM);
    * 100GB SSD storage (or similar I/O performance);
    * a low-latency, reliable broadband internet connection.
2. Ledger Nano S Plus or Nano X hardware wallet with Tezos Wallet & Baker apps installed.

   > It's necessary to use Ledger Live to install the Tezos Wallet & Baking applications; to install the latter you must enable developer mode in Ledger Live settings

   > Absolutely ensure you have properly configured the Ledger device settings so the device doesn't lock 10 minutes after inactivity. This is a common issue that can cause your baker to stop baking.
   
   ![<Ledger locking settings](/tezbake/tutorial/tezbakeLedgerLock.png)

   > Remove the Ledger wallet app from your baking device after you've finished setting up your baker. This is a security measure to prevent unauthorized access to your baker. You can always reinstall the app when you need to use the Ledger for baking or other purposes.

---

## Installation

### Download and copy tezbake
To begin, run the script below, which will download the latest version of TezBake and copy it to your `/usr/sbin` directory. This script works with both x86_64 and arm64 architectures.

   ```
   wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
   # you may be prompted for sudo password
   ```

### Setup Tezos node, signer and install tezbake dependencies

   ```
   tezbake setup -a
   # you may be prompted for sudo password
   ```

### Bootstrap Tezos node
At this stage, it's necessary to bootstrap your node, meaning to download a copy of the blockchain so you don't have to synchronize block-by-block, which takes hours at best.
  
   ```
   tezbake bootstrap-node <url> <block_hash>
   # example:
   tezbake bootstrap-node https://snapshots.eu.tzinit.org/mainnet/rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
   ```

> You can replace `eu` above with `us` or `asia` if you prefer to use a different mirror closer to you.

Get the block hash and block level from the snapshot provider's website:
https://snapshots.eu.tzinit.org/mainnet/rolling.html

> The `<block_hash>` argument is optional but encouraged. If you don't want to borther with this protection, use the second method below which will also be faster.

Verify the hash/checksum provided by the snapshot provider to ensure the snapshot is valid. You can find the correct hashes for all blocks on Tezos blockchain explorers such as:
https://tzkt.io/blocks
https://tzstats.com/

Simply search for the block level in the search field and verify the hash of the block matches the hash provided by the snapshot provider.

### Start Tezos node
After importing the snapshot, you need to start your node and wait until it's fully synchronized before importing your Ledger key.

   ```
   tezbake start
   ```

After starting the node, run the following command over and over every few minutes and monitor the "level" displayed.
   
   ```
   tezbake info
   ```

> Level refers to the latest block number on mainnet. Navigate to https://tzkt.io or https://tzstats.com and observe the latest block. Once the level in your command matches the latest block on your blockchain explorer, your node is in full sync and you can keep following the steps below.

> Both https://tzkt.io or https://tzstats.com provide Ghostnet and Testnet block explorers as well. Make sure you're looking at the right explorer.

### Import Ledger key or soft key and register as baker
Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You have the option to use the secure Ledger hardware wallet or simply use a local, unencrypted software key (a.k.a. soft key). The secure Ledger hardware wallet is the recommended option for mainnet baking.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### (Option 1 - RECOMMENDED) Import Ledger key to TezBake signer
   ```
   tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --hwm 1

   # If you have a custom derivation path, you can specify it as shown: (`--import-key="ed25519/0h/0h"`; the default is ed25519/0h/0h which works just fine but is not as fast as P-256 or secp256k1.
   # `--hwm 1` works great if you're setting up for the first time. If you're setting up a device that's been used to bake before, you want to change this (`1`) to the current block height on the blockchain for your safety.
   # If you're importing for the second time after already trying again but failing, you can use `--force` to force the import.
   ```

> Once imported, you can see your baker address by running `tezbake info`

> The ledger will ask you twice to confirm this operation. Make sure the baker you see on the ledger screen matches the one you want to use. If you don't have this information yet, don't worry. To get the address of the ledger that's used by default simply go to https://kukai.app and login with ledger, accepting the default derivation path.

> BLS (i.e. bip) signatures are designed to offer greater flexibility and scalability for certain applications compared to the default ED25519 algorithm. 

> Putting the baker on a non-default derivation path provides an additional layer of security for your baker at the cost of extra complexity for you. Make sure your setup is clearly documented for your own records.

> If your device was used to bake before it might have a "high watermark" aka HWM. If you try to use this device on a testnet, it will not work because the block height on test networks usually starts with 1 while mainnet is up to over a couple of million blocks at the time of writing.
If you used to bake on mainnet with the same ledger as you're trying to use now but it's been a while, it's highly recommended to change the 1 above to the current block on the network that will be used for the device going forward.

> The watermark is simply a record of the lack block number your ledger helped to bake or attest. If you're setting up a brand new device that's not been used for baking before, there is no need to alter the default command above.

> Always make sure you're not accidentally going to double bake by using your production ledger and/or setup to bake on a testnet. It's really easy to make this mistake and the only thing preventing it are your personal standard operating procedures, the documentation you keep, and the care you take when setting up your baker.

> To double bake or attest due to baker setup error means having 2 different bakers with the same key on the same network. This is a serious offense and can lead to loss of bond and other penalties. Always double-check your setup and make sure you're not accidentally double baking or attesting.

#### (Option 2 - INSECURE) Import Soft key to TezBake signer
First, generate the baker key for TezBake signer:

   ```
   tezbake setup-soft-wallet
   ```

> Make sure to backup your key in a secure location and never share it. 

You can get the secret/private key by running the following command:

   ```
   tezbake signer client show address baker --show-secret
   ```

### Register as baker on the Tezos blockchain Mainnet
For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (__note__: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

   ```
   tezbake register-key
   ```

> Registering is not necessary if this is already an active baker ledger which is being setup on some kind of failover machine or in a situation where it has not been over 2 weeks of baking inactivity.

> Registering applies to new bakers and to inactive bakers. If you're setting up a new baker, you must register it. If you're setting up a baker that's been inactive for over 2 weeks, you must register it. If you're setting up a baker that's been inactive for less than 2 weeks, you don't need to register it. The best way to find out if you need to register your baker again is to look into your baking rights schedule and see if they stopped coming in. If they did, you need to register your baker again.

### Stake your baking XTZ security deposit
To bake on the Tezos network, you need to stake your XTZ security deposit. This is a slashable security deposit that you will get back when you stop baking. The minimum bond to get baking rights is currently set at 6000 XTZ. 

You can stake your security deposit by running the following command, after opening your Ledger Tezos Wallet app:

   ```
   tezbake signer client stake 6000 for baker 
   ```

> Change 6000 to the amount you want to stake. The minimum is 6000 XTZ. You may start baking with as little as 1000 XTZ but you will need to set your baking over staking multiplier to 5X and secure 5000 XTZ that will stake to your baker to cover the security deposit requirement. You can also secure delegators' XTZ to your baker to cover the security deposit requirement. Each XTZ delegated to your baker will be counted 0.5 towards the security deposit requirement. For example you can start baking with 1000 XTZ + 5000 XTZ secured from stakers, or you can start baking with 1000 XTZ + 10000 XTZ secured from delegators. You can also start baking with 1000 XTZ + 2000 XTZ secured from stakers + 6000 XTZ secured from delegators.


---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 