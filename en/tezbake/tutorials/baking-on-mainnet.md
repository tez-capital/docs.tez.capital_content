---
title: "Baking on Mainnet"
weight: 1
type: docs
summary: Complete guide to setting up a Tezos baker on mainnet with TezBake, from system preparation to first baking rights
---

{{< youtube t85LjgpRtvc >}}

> **ℹ️ System Requirements**
> - **OS:** Ubuntu 22.04+ or Debian 12+ (other Linux distributions may work but are untested)
> - **Hardware:** See [Hardware Requirements](/getting-started/hardware-requirements/)
> - macOS and Windows are **not supported**

## Prerequisites

- **Ubuntu 22.04+ or Debian 12+** Linux server or desktop with SSH access
- **Minimum 6000 XTZ** for staking as security deposit (or 1000 XTZ with external stakers/delegators — see staking table below)
- **Ledger hardware wallet** with Tezos Wallet app installed, OR a [TezSign device](/tezbake/tutorials/baking-with-tezsign/)
- **Hardware meeting minimum specs:** 3 CPU cores, 8GB RAM + 8GB swap, 100GB SSD, reliable broadband

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

## Installation Checklist & Time Estimates

| Step | Estimated Time |
|------|---------------|
| System setup (Ubuntu, user, firewall) | 15-30 min |
| Install TezBake | ~2 min |
| Setup node + DAL | ~5 min |
| Bootstrap node | 30 min - 3 hours (depends on internet) |
| Wait for sync | 5-30 min after bootstrap |
| Import keys | ~10 min |
| Register as baker | ~5 min |
| Stake XTZ | ~5 min |
| **Total active work** | **~1-4 hours** |

> **ℹ️ INFO:** After registration, it takes up to **2 cycles (~2 days)** before you receive your first baking rights.

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
   > **ℹ️ INFO:** Baking with a Ledger is no longer recommended for new bakers. It's still possible to bake with the Ledger but we recommend you implement TezSign right away.

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
wget -q https://bake.tez.capital/install -O /tmp/install.sh && sudo sh /tmp/install.sh
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

> **ℹ️ INFO:** Baking with a Ledger is no longer recommended for new bakers. TezSign is the preferred signing solution. These instructions remain for existing Ledger bakers.

##### Derivation Path

The derivation path controls which key on your Ledger is used for baking. You can specify any supported curve and path:

| Path | Curve | Notes |
|------|-------|-------|
| `P-256/0h/0h` | tz3 | **Recommended** — faster signing |
| `ed25519/0h/0h` | tz1 | Default — slower, widely supported |
| `secp256k1/0h/0h` | tz2 | Alternative — faster than ed25519 |

> **💡 TIP:** P-256 and secp256k1 offer faster signing than ed25519. If you're setting up a new baker, use P-256. If you're migrating an existing baker, keep the same curve you've been using.
>
> Using a non-default derivation path adds an extra layer of security but also complexity. Make sure your choice is clearly documented for your own records. To find out which key a given path corresponds to, go to <https://gov.tez.capital> and log in with Ledger — it shows the address for the default path.

##### Importing the Key

Make sure your Ledger has the **Tezos Baking app** open before running this command.

```bash
tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --hwm 1
```

The Ledger will ask you **twice** to confirm the operation. Check that the baker address shown on the Ledger screen is the one you intend to use.

> **💡 TIP:** If you're importing for the second time after a failed attempt, add `--force` to override the previous import.

> **⚠️ Ledger Baking App Behavior**
> - The Ledger **must** stay on the Tezos Baking app at all times while baking
> - The screensaver activating is normal — baking continues
> - Do **NOT** open Ledger Live while baking — it will disconnect the signer
> - To vote or transfer funds, stop the baker first, switch apps, then switch back

##### High Watermark (HWM)

The `--hwm 1` flag sets the High Watermark — a security counter stored on the Ledger that records the highest block level it has ever signed. The Ledger refuses to sign any block at or below this level, which prevents accidentally signing the same block twice.

- **New baker / new device:** Use `--hwm 1` (the default command above is safe).
- **Existing baker returning after a break:** Set `--hwm` to the **current block height** to prevent signing any historical blocks. You can find the current level at <https://tzkt.io>.
- **Migrating from mainnet to testnet:** Testnet block levels usually start at 1, which is below a mainnet HWM of millions. Use a testnet-specific device, never reuse a mainnet device.

> **💡 TIP:** The HWM is device-specific. Each Ledger maintains its own watermark independently.

##### Verification

After importing, confirm the key was imported successfully:

```bash
tezbake info
```

Look for your baker address in the output. If it appears and the signer status shows as connected, the import was successful. See [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) for a full explanation of the `tezbake info` output.

##### ⚠️ Double Baking Prevention

> **🚨 CRITICAL: Double Baking**
>
> **What is Double Baking?** Running two bakers with the same key on the same network simultaneously. This means your key signs conflicting blocks or attestations, which is treated as a malicious attack. The protocol detects this and **slashes (confiscates) your staked tez** as punishment. See [Slashing Explained](/getting-started/slashing-explained/) for penalty details.
>
> **Critical Rules:**
> * **Never run two bakers with the same key** — even briefly, even on different machines
> * **Never use your TezSign backup device to bake** — backup devices are only for emergency failover
> * **Always use separate devices for testnet** — never reuse a mainnet Ledger or TezSign on a testnet
> * **Always use separate computers per network** — one machine for mainnet, a different machine for testnet
> * The HWM helps prevent *accidental* double baking, but careful operational procedures are your primary defense

#### (Option 3 - INSECURE) Import Soft key to TezBake node

> **⚠️ WARNING: tz4 Soft Keys Require a Companion Key**
>
> When using a tz4 (BLS) key as your baker/consensus key, you **must** also register a separate tz4 companion key. The companion key is mandatory for DAL attestation. Without it, your baker will produce attestations without DAL payloads and you will forfeit ~10% of your baking rewards.

**Step 1 — Generate the baker (consensus) key:**

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

#### Option A: TezGov (recommended)

1. Go to [gov.tez.capital](https://gov.tez.capital) and connect with your baker's manager key (Ledger Wallet app)
2. Navigate to **Baker Management → Keys**
3. Set your **Consensus Key**: paste the `BLpk...` public key and POP for `baker`
4. Set your **Companion Key**: paste the `BLpk...` public key and POP for `companion`
5. Confirm both operations on your Ledger

#### Option B: CLI

```bash
tezbake signer client set consensus key for baker to baker
tezbake signer client set companion key for baker to companion
```

> **⏱️ Activation:** Both keys take effect after **3 cycles (~3 days)**. Monitor activation at:
> `https://tzkt.io/<your_baker_address>/secondary-keys`

**Step 5 — Add the companion key alias to the baker configuration:**

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
tezbake upgrade
```

Verify it was set:

```bash
tezbake node show configuration.additional_key_aliases
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
> * Your baker has been inactive for over 2 cycles (~2 days)
> * Your baking rights have stopped appearing in the schedule
>
> You do NOT need to register if your baker has been inactive for less than 2 cycles (~2 days). Check your baking rights schedule to confirm if re-registration is needed.

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

**How delegation weight works:**

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

### Baker Per-Block Votes

Bakers cast two types of votes with every block they produce. TezBake configures sensible defaults, but you should understand what you're voting for:

**Liquidity Baking Toggle Vote:**
- Controls a protocol feature that mints 5 tez per minute (0.5 tez per block at 6-second block times) into a tez/tzBTC liquidity pool
- Options: `on` (continue subsidy), `off` (end subsidy), `pass` (abstain)
- If enough bakers vote `off` over ~2000 blocks, the subsidy pauses
- Default in TezBake: `on`

**Adaptive Issuance Vote:**
- Controls whether the protocol adjusts reward rates based on the network's staked ratio
- The protocol targets 50% of tez staked; rewards increase when staking is low, decrease when high
- Options: `on`, `off`, `pass`
- Default in TezBake: `on`

To view or modify your votes, edit the vote file:

```bash
nano /bake-buddy/node/data/vote-file.json
```

Example contents:

```json
{"adaptive_issuance_vote":"on","liquidity_baking_toggle_vote":"on"}
```

Changes take effect immediately — the baker reads the vote file each time it produces a block. No restart required.

## Installation (Advanced)

You can install different TezBake components on different systems. For example, you can have the Signer, Node and DAL on 3 different machines, all communicating together.

Follow the steps in [Baking with Prism](/tezbake/tutorials/baking-with-prism/)

As with everything in life, complexity adds more failure points. Only separate the TezBake components if you have a compelling use case.

---

## What's Next

✅ **Your baker is running!** Here's what to set up next:

1. [Monitor your baker](/tezbake/tutorials/monitoring-logs-and-status/) — Track sync status and performance
2. [Set up TezWatch alerts](/tezwatch/tutorials/setup/) — Get notified about missed attestations
3. [Configure TezPay](/tezpay/tutorials/setup/) — Automate delegator payouts
4. [Install TezPeak dashboard](/tezpeak/tutorials/setup/) — Visual monitoring interface
5. [Set up TezGov](/tezgov/tutorials/voting-on-proposals/) — Participate in governance
6. [Review best practices](/getting-started/best-practices/) — Security, UPS, monitoring

---

## Related Guides

**Next Steps:**

* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Monitor your baker's health via CLI
* [TezPeak Setup](/tezpeak/tutorials/setup/) - Monitor your baker with a graphical web dashboard
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
