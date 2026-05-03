---
title: "Baking on Testnets"
weight: 2
type: docs
summary: Using TezBake to bake on the Tezos testnets
---

## Tezos Testnets

* Bakingnet is the recommended testnet for most bakers. It is a long-running baker testnet that switches to new Tezos protocol proposals about one week before they go live on Mainnet, giving bakers time to test upgrades before production activation.
* Tallinnnet is a protocol testnet with 20 minute cycles. Use it when you want rapid testing for setup, key activation, registration, or inactivity behavior.
* Shadownet is a long-running testnet for application and staging tests. Do not set up a baker on Shadownet unless you have been explicitly invited or coordinated with the Shadownet operators. Bakingnet is the preferred baker testnet.
* Ghostnet is the original Tezos testnet and is now deprecated. Prefer Bakingnet or Tallinnnet for new baker testing.

## Prerequisites

- **Linux OS installed** (Ubuntu 22.04+ recommended) with SSH or physical access
- **[TezBake binary](/tezbake/tutorials/baking-on-mainnet/#download-and-install-tezbake)** — download and install before running setup
- **Test XTZ** from the [testnet faucet](https://teztnets.com) (no real funds required)
- **Hardware:** 8GB RAM minimum, SSD storage

## Table of Contents

1. [Preparation](#preparation)
2. [Installation (All-in-one)](#installation-all-in-one)
3. [Installation (Advanced)](#installation-advanced)

---

## Preparation

Installing TezBake and using it to setup your Tezos baker is very simple. You will need the following tools:

*  Spare computer or existing computer or VM with Linux installed. We recommend Ubuntu Linux.
   > **ℹ️ INFO:** You must have an SSD drive (or better) and at least 8GB RAM

All advanced TezBake configurations, including TezSign block signing apply here as well.

---

> **🚨 CRITICAL: DAL Node Mandatory**
> All bakers are required to run a DAL node. See [Baking with DAL](/tezbake/tutorials/baking-with-dal/) for setup instructions and details.

## Installation (All-in-one)

### Download and install tezbake

To begin, run the script below, which will download the latest version of TezBake and copy it to your `/usr/sbin` directory. This script works with both x86_64 and arm64 architectures.

```bash
wget -q https://bake.tez.capital/install -O /tmp/install.sh && sudo sh /tmp/install.sh
# you may be prompted for sudo password
```

### Setup Tezos node, signer, DAL and install tezbake dependencies

```bash
# Bakingnet setup (recommended for most bakers):
tezbake setup --with-dal --node-configuration=https://configs.tez.capital/bakingnet.json
# Tallinnnet setup (20 minute cycles for rapid testing):
tezbake setup --with-dal --node-configuration=https://configs.tez.capital/tallinnnet.json
# you may be prompted for sudo password
```

### Bootstrap Tezos node

At this stage, it's necessary to bootstrap your node, meaning to download a copy of the blockchain so you don't have to synchronize block-by-block, which takes hours at best.

```bash
tezbake bootstrap-node <url> <block_hash>
# Bakingnet example:
tezbake bootstrap-node https://snapshots.tzinit.org/bakingnet/rolling <BLOCK_HASH>
# Tallinnnet example:
tezbake bootstrap-node https://snapshots.tzinit.org/tallinnnet/rolling <BLOCK_HASH>
```

> **ℹ️ INFO:** Get the current block hash from the snapshot provider's website.

Get the block hash and block level from the snapshot provider's website:

* <https://snapshots.tzinit.org/bakingnet/rolling.html>
* <https://snapshots.tzinit.org/tallinnnet/rolling.html>

> **ℹ️ INFO:** The `<block_hash>` argument is optional but encouraged for security verification. If you don't want to bother with this protection, you can skip it for a faster bootstrap.

Verify the hash/checksum provided by the snapshot provider to ensure the snapshot is valid. You can find the correct hashes for all blocks on Tezos blockchain explorers such as:
<https://tzkt.io/blocks>

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
> 2. Compare it to the latest block on <https://tzkt.io>
> 3. Once they match, your node is fully synced and you can proceed
>
> **ℹ️ INFO:** Both blockchain explorers also provide Ghostnet and testnet views. Make sure you're looking at the correct network.

### Import baking keys and register as baker

Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### Import Soft key to TezBake

> **ℹ️ Key Roles: Manager / Consensus / Companion**
>
> Tezos baking uses three distinct key roles. For a full explanation, see [Baking on Mainnet — Understanding Baker Key Roles](/tezbake/tutorials/baking-on-mainnet/#understanding-baker-key-roles).
>
> **In this soft key testnet setup:** the `baker` key is your manager address AND your initial consensus key — both roles live at the same tz4 address.
>
> **⚠️ WARNING: Companion Key is Mandatory**
>
> When using a tz4 (BLS) key as your manager/consensus key, you **must** also register a separate tz4 companion key. Without it, your baker will forfeit ~10% of baking rewards.

**Step 1 — Generate the baker key (this becomes your manager address AND initial consensus key):**

```bash
tezbake setup-soft-wallet --generate bls --key-alias baker
```

**Step 2 — Generate the companion key:**

```bash
tezbake setup-soft-wallet --generate bls --key-alias companion
```

> **💾 Backup both keys.** You can retrieve each secret key with:
>
> ```bash
> tezbake signer client show address baker --show-secret
> tezbake signer client show address companion --show-secret
> ```
>
> Store these in a secure, offline location. Never share them.

**Step 3 — Get the public key (BLpk) and Proof of Possession (POP) for each key:**

The POP is a cryptographic proof that you own the private key. It is required when registering tz4 keys on-chain.

```bash
tezbake signer client show address baker
tezbake signer client get proof of possession for baker

tezbake signer client show address companion
tezbake signer client get proof of possession for companion
```

Note the `BLpk...` public key and POP output for both keys — you will need them in the next step.

**Step 4 — Register the consensus and companion keys:**

##### Option A: TezGov (recommended)

1. Go to [gov.tez.capital](https://gov.tez.capital) and connect with your baker's manager key
2. Navigate to **Baker Management → Keys**
3. Set your **Consensus Key**: paste the `BLpk...` public key and POP for `baker`
4. Set your **Companion Key**: paste the `BLpk...` public key and POP for `companion`
5. Confirm both operations

##### Option B: CLI

```bash
tezbake signer client set consensus key for baker to baker
tezbake signer client set companion key for baker to companion
```

> **⏱️ Activation:** Both keys take effect after **3 cycles**. On 20 minute cycle testnets like Tallinnnet this is ~1 hour; on longer-cycle testnets it can take days. Monitor activation at:
> `https://tzkt.io/<your_baker_address>/secondary-keys`

**Step 5 — Add the companion key alias to the baker configuration:**

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
tezbake upgrade
```

### Register as baker on the Tezos blockchain Testnet

For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (__note__: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

To secure your XTZ security deposit on a testnet, use the faucet for your chosen network to get free test XTZ:

* Bakingnet: <https://faucet.bakingnet.teztnets.com>
* Tallinnnet: <https://faucet.tallinnnet.teztnets.com>

Current Tezos testnet faucets are listed at <https://teztnets.com>

```bash
tezbake register-key
```

> **ℹ️ When Registration is Required:**
>
> You must register your baker if:
>
> * You're setting up a new baker (first time)
> * Your baker has been inactive beyond the inactivity period (2 cycles - about 40 minutes on 20 minute cycle testnets like Tallinnnet, or longer on standard-cycle testnets)
> * Your baking rights have stopped appearing in the schedule
>
> Check your baking rights schedule to confirm if re-registration is needed.

### Stake your baking XTZ security deposit

To bake on the Tezos network, you need to stake your XTZ security deposit. This is a [slashable](/getting-started/slashing-explained/) security deposit that you will get back when you stop baking. The minimum bond to get baking rights is currently set at 6000 XTZ.

You can stake your security deposit by running the following command, after opening your Ledger Tezos Wallet app:

```bash
tezbake signer client stake 6000 for baker
```

> **ℹ️ Staking Options:**
>
> Change 6000 to the amount you want to stake. The minimum security deposit is 6000 XTZ.
>
> **Alternative Configurations:**
> You may start baking with as little as 1000 XTZ if you configure additional sources:
>
> * Set baking over staking multiplier to 5X + secure 5000 XTZ from stakers, OR
> * Secure 15,000 XTZ from delegators (each delegated XTZ counts as ≈0.33 towards the security deposit per Paris protocol), OR
> * Combine: 1000 XTZ + 2000 XTZ from stakers + 9000 XTZ from delegators

### Import your DAL attester profile

```bash
tezbake update-dal-profiles <your-baker-tz4-key> --force
```

## Installation (Advanced)

You can install different TezBake components on different systems. For example, you can have the Signer, Node and DAL on 3 different machines, all communicating together.

Follow the steps in [Baking with Prism](/tezbake/tutorials/baking-with-prism/)

As with everything in life, complexity adds more failure points. Only separate the TezBake components if you have a compelling use case.

---

## Related Guides

**Production Baking:**

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Production setup guide
* [Best Practices](/getting-started/best-practices/) - Hardware and operational recommendations

**After Setup:**

* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Monitor your baker
* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Common issues and solutions

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
