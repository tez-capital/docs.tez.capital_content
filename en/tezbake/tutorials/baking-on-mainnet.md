---
title: "> Baking on Mainnet"
weight: 1
type: docs
summary: TezBake Baking Tutorial
---

{{< youtube t85LjgpRtvc >}}

## Table of Contents

1. [Preparation](#preparation)
2. [Installation (All-in-one)](#installation-all-in-one)
   * [Download and install tezbake](#download-and-install-tezbake)
   * [Setup Tezos node, signer, DAL](#setup-tezos-node-signer-dal-and-install-tezbake-dependencies)
   * [Bootstrap Tezos node](#bootstrap-tezos-node)
   * [Start Tezos node](#start-tezos-node)
   * [Import baking keys and register](#import-baking-keys-and-register-as-baker)
   * [Register as baker](#register-as-baker-on-the-tezos-mainnet)
   * [Stake your XTZ](#stake-your-baking-xtz-security-deposit)
   * [Import DAL attester profile](#import-your-dal-attester-profile)
3. [Installation (Advanced)](#installation-advanced)
4. [Related Guides](#related-guides)

---

## Preparation

Installing TezBake and using it to setup your Tezos baker is very simple. You will need the following tools:

1. Spare computer or existing computer with Linux installed. The recommended requirements are provided by [Nomadic Labs](https://research-development.nomadic-labs.com/paris-announcement.html#10s-block-times-bring-lower-latency-and-faster-finality)
    * 3 CPU cores (arm64 or amd64/x86-64 architectures) – 2 are needed by the Octez node and 1 is needed by the Octez baker;
    * 8GB RAM + 8GB swap (or 16GB RAM);
    * 100GB SSD storage (or similar I/O performance);
    * a low-latency, reliable broadband internet connection.
2. Ledger Nano S Plus or Nano X hardware wallet with Tezos Wallet app installed. This will be used for storing your funds, voting and changing your baking parameters.
   > **ℹ️ INFO:** You must use Ledger Live to install the Tezos Wallet app on your device.
3. **TezSign remote signer device** - A purpose-built hardware signer for Tezos baking, based on affordable single-board computers like Raspberry Pi Zero 2W (~$20-30 in hardware). Connects via USB to your baking node and is designed to do one thing perfectly: sign baking operations securely. TezSign supports tz4 signatures required by modern Tezos protocols, which Ledger devices no longer support for baking. Unlike a general-purpose Ledger wallet, TezSign can remain connected 24/7 dedicated solely to baking. [Learn more: Baking with TezSign](/tezbake/tutorials/baking-with-tezsign)

---

> **🚨 CRITICAL: DAL Node Mandatory**
>
> Running a DAL (Data Availability Layer) node is now **mandatory** for baking on the Tezos network. It's not currently mandatory to run the DAL on the same machine as your node or signer. Read more about advanced DAL configurations here: [Baking with DAL](/tezbake/tutorials/baking-with-dal)
>
> **What is the DAL?**
> The DAL acts like an overflow area for data, where large amounts of information can be kept available to the network without overloading the core blockchain. This means Tezos can safely handle far more transactions and complex operations, because the rollups can rely on the DAL to make their data available for everyone to verify.

## Installation (All-in-one)

### Download and install tezbake

To begin, run the script below, which will download the latest version of TezBake and copy it to your `/usr/sbin` directory. This script works with both x86_64 and arm64 architectures.

   ```bash
   wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
   # you may be prompted for sudo password
   ```

### Setup Tezos node, signer, DAL and install tezbake dependencies

   ```bash
   tezbake setup --with-dal
   # you may be prompted for sudo password
   ```

### Bootstrap Tezos node

At this stage, it's necessary to bootstrap your node, meaning to download a snapshot (copy) of the blockchain so you don't have to synchronize block-by-block, which would take many hours or days. We use a "rolling" snapshot which contains recent blockchain history (suitable for baking) rather than the complete archive from genesis.
  
   ```bash
   tezbake bootstrap-node <url> <block_hash>
   # example:
   tezbake bootstrap-node https://snapshots.tzinit.org/mainnet/rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
   ```

Get the block hash and block level from the snapshot provider's website:
<https://snapshots.eu.tzinit.org/mainnet/rolling.html>

> **ℹ️ INFO:** The `<block_hash>` argument is optional but encouraged for security verification. If you don't want to bother with this protection, you can skip it for a faster bootstrap.

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

> **Understanding "Level"**
>
> The level is the current block height (block number) on the blockchain. To verify your node is synchronized:
>
> 1. Check the level shown by `tezbake info`
> 2. Compare it to the latest block on <https://tzkt.io> or <https://tzstats.com>
> 3. Once they match, your node is fully synced and you can proceed
>
> **ℹ️ INFO:** Both blockchain explorers also provide Ghostnet and Testnet views. Make sure you're looking at the correct network (mainnet).

### Import baking keys and register as baker

Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You have the option to use the secure TezSign device, a Ledger hardware wallet or simply use a local, unencrypted software key (a.k.a. soft key). TezSign is now recommended for validating blocks securely on Tezos. Interoperation guides will be provided for alternative tz4 remote signing devices.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### (Option 1 - RECOMMENDED) Import TezSign consensus and companion keys

Follow the following guide and then proceed directly to [stake your XTZ](#stake-your-baking-xtz-security-deposit):

[Baking with TezSign](/tezbake/tutorials/baking-with-tezsign)

> **⚠️ WARNING: Key Alias Naming**
>
> When following the TezSign guide, use these alias names:
>
> * Use `baker` instead of `consensus` (for your consensus key)
> * Use `companion` as-is (for your companion key)
>
> This naming convention is required because you're managing your baker operations from a Ledger device using the <https://gov.tez.capital> interface.

#### (Option 2 - DEPRECATED) Import Ledger key to TezBake signer

In order to get the most out of your Ledger, it's now recommended to use the P-256 curve (tz3) for baking. This curve is faster than the default ed25519 curve (tz1). If you're setting up a baker that already exists on the network, you can use the same curve as before. If you're setting up a new baker, it's recommended to use the P-256 curve.

You can import your Ledger key by running the following command:

   ```bash
   tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --hwm 1
   ```

> **Derivation path:** You can specify a custom path like `--import-key="ed25519/0h/0h"` (the default). P-256 and secp256k1 curves offer faster signing than ed25519.
>
> **High Watermark (HWM):** `--hwm 1` sets the starting block level for safety. The HWM is a security feature that prevents double baking by tracking the highest block your device has signed. For first-time setup, use `--hwm 1`. If your device previously baked, set this to the current block height to prevent accidentally signing older blocks.
> If you're importing for the second time after already trying again but failing, you can use `--force` to force the import.
> Once imported, you can see your baker address by running `tezbake info`
> The ledger will ask you twice to confirm this operation. Make sure the baker you see on the ledger screen matches the one you want to use. If you don't have this information yet, don't worry. To get the address of the ledger that's used by default simply go to <https://gov.tez.capital> and login with ledger, accepting the default derivation path.
> Putting the baker on a non-default derivation path provides an additional layer of security for your baker at the cost of extra complexity for you. Make sure your setup is clearly documented for your own records.
> If your device was used to bake before it might have a "high watermark" aka HWM. If you try to use this device on a testnet, it will not work because the block height on test networks usually starts with 1 while mainnet is up to over a couple of million blocks at the time of writing.
If you used to bake on mainnet with the same ledger as you're trying to use now but it's been a while, it's highly recommended to change the 1 above to the current block on the network that will be used for the device going forward.
> The watermark is simply a record of the lack block number your ledger helped to bake or attest. If you're setting up a brand new device that's not been used for baking before, there is no need to alter the default command above.
> **What is Double Baking?** Running two bakers with the same key on the same network simultaneously. This means your key signs conflicting blocks or attestations, which is considered a malicious attack on the network. The protocol detects this and slashes (confiscates) your staked tez as punishment.
>
> **Prevention - Critical Rules:**
> * **Never use bakers with the same seed on any network** - even different networks (mainnet vs testnet)
> * **Never use your TezSign backup device to bake** - backup devices are only for emergency failover
> * **Always create testnet-specific devices** - use separate Ledger or TezSign devices dedicated to testnet
> * **Always use different computers for different functions** - one computer for mainnet baking, a separate computer for testnet baking
> * The HWM protection helps prevent accidental double baking, but careful operational procedures are your primary defense

#### (Option 3 - INSECURE) Import Soft key to TezBake node

First, generate the baker key for TezBake:

   ```bash
   tezbake setup-soft-wallet --generate bls --key-alias baker
   ```

> Make sure to backup your key in a secure location and never share it.

You can get the secret/private key by running the following command:

   ```bash
   tezbake node client show address baker --show-secret
   ```

### Register as baker on the Tezos Mainnet

For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (**note**: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

#### Using TezGov

Use the `Become a Baker` button on the <https://gov.tez.capital> portal.

#### Using CLI (not recommended)

   ```bash
   tezbake register-key
   ```

> **ℹ️ When Registration is Required:**
>
> You must register your baker if:
>
> * You're setting up a new baker (first time)
> * Your baker has been inactive for over 3 days
> * Your baking rights have stopped appearing in the schedule
>
> You do NOT need to register if your baker has been inactive for less than 3 days. Check your baking rights schedule to confirm if re-registration is needed.

### Stake your baking XTZ security deposit

To bake on the Tezos network, you need to stake your XTZ security deposit. This is a slashable security deposit that you will get back when you stop baking.

**Staking Requirements:**

The minimum bond to get baking rights is **6000 XTZ**. However, you have several options to meet this requirement:

| Your XTZ | From Stakers | From Delegators | Notes                      |
| -------- | ------------ | --------------- | -------------------------- |
| 6000+    | 0            | 0               | Simplest - full self-stake |
| 1000     | 5000         | 0               | Requires 5X multiplier     |
| 1000     | 0            | 15,000          | Pure delegation model      |
| 1000     | 2000         | 9000            | Mixed approach             |

**How delegation weight works (Paris protocol):**

* **Your stake + Stakers:** Each XTZ counts as **1.0** toward the requirement
* **Delegators:** Each XTZ counts as **≈0.33** (1/3) toward the requirement
* **Formula:** (Your XTZ + Staker XTZ) + (Delegator XTZ × 0.33) ≥ 6000

**Example calculations:**

* 1000 (self) + 5000 (stakers) = 6000 ✓
* 1000 (self) + (15,000 delegators × 0.33) = 1000 + 5000 = 6000 ✓
* 1000 (self) + 2000 (stakers) + (9000 delegators × 0.33) = 1000 + 2000 + 3000 = 6000 ✓

#### Using TezGov to stake

Use the `Stake` button on the <https://gov.tez.capital> portal.

#### Using CLI to stake (not recommended)

You can stake your security deposit by running the following command, after opening your Ledger Tezos Wallet app:

   ```bash
   tezbake signer client stake 6000 for baker 
   ```

### Import your DAL attester profile

Your baker needs to register as a DAL attester to participate in the Data Availability Layer. This tells the network which DAL node to use for your baker.

   ```bash
   tezbake update-dal-profiles --auto
   # If for some reason the auto method is not working, use the command below to bypass it.
   tezbake update-dal-profiles <your-baker-tz4-key> --force
   ```

> **ℹ️ INFO:** The tz4 key referenced here is your baker's public key address (starts with tz4), not your consensus key or companion key.

## Installation (Advanced)

You can install different TezBake components on different systems. For example, you can have the Signer, Node and DAL on 3 different machines, all communicating together.

Follow the steps in [Baking with Prism](/tezbake/tutorials/baking-with-prism/)

As with everything in life, complexity adds more failure points. Only separate the TezBake components if you have a compelling use case.

---

## Related Guides

**Next Steps:**

* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Monitor your baker's health
* [TezPay Setup](/tezpay/tutorials/setup/) - Automate reward payments to delegators
* [Best Practices](/getting-started/best-practices/) - Hardware and operational recommendations

**Advanced Configurations:**

* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - Detailed TezSign setup guide
* [Baking with DAL](/tezbake/tutorials/baking-with-dal/) - Advanced DAL configurations
* [Baking with Prism](/tezbake/tutorials/baking-with-prism/) - Distributed component setup
* [Baking with Consensus Key](/tezbake/tutorials/baking-with-consensus-key/) - Separate consensus key management

**Troubleshooting:**

* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Common issues and solutions
* [Missing Attestations](/tezbake/tutorials/missing-attestations/) - Debug attestation problems

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
