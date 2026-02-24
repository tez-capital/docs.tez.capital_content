---
title: "Glossary"
weight: 10
type: docs
summary: "Definitions of key Tezos baking terms and concepts"
---

## Tezos Baking Glossary

A reference guide to terminology used across the Tez Capital documentation and the Tezos ecosystem. Terms are listed alphabetically.

---

### Adaptive Issuance

A protocol mechanism that dynamically adjusts the rate of new XTZ issued as baking rewards based on the percentage of total supply that is staked. When more XTZ is staked, issuance decreases; when less is staked, issuance increases to incentivise participation. Introduced in the Paris protocol.

---

### Attestation

An operation broadcast by a baker to certify that a block they observed is valid and should be included in the chain. Attestations replaced the older term *endorsements*. Bakers must maintain a 67% attestation success rate per cycle or face reduced rewards. See also: [Rights](#rights), [Cycle](#cycle).

---

### Baker

A Tezos node operator who participates in consensus by producing blocks and broadcasting attestations. Bakers stake XTZ as collateral. In return the protocol rewards them with newly issued XTZ each cycle. A baker can also accept delegations and stakers from the community. See [What is Baking?](/getting-started/what-is-baking/)

---

### Block

The unit of data appended to the Tezos blockchain each slot. Under the **Tallinn** protocol, blocks are produced approximately every **6 seconds**. Each block contains operations (transactions, attestations, governance votes, etc.) and is baked by the baker holding the block production right for that slot.

---

### Bootstrapping

The initial synchronisation phase when a Tezos node first starts or reconnects after a long absence. The node must download and validate the chain history before it is ready to participate in consensus. Bootstrapping can take 30 minutes to several hours depending on hardware and network speed. A node showing "bootstrapping" is working normally — not stuck.

> **ℹ️ INFO:** See [FAQ](/faq/#my-node-says-bootstrapping-for-hours-is-it-stuck) for common questions about bootstrapping time.

---

### Companion Key

A secondary key associated with a baker that can be used for specific operations (such as submitting DAL shards) without requiring the primary consensus key. Separating responsibilities between keys improves security.

---

### Consensus Key

The key that a baker uses to sign blocks and attestations. This is separate from the *manager key* (which controls the baker's funds). Using a separate consensus key means the hot key used for baking never has access to the baker's full XTZ balance.

On modern Tezos protocols, consensus keys can be **tz4** (BLS12-381) addresses, which enables more efficient aggregated signatures. **TezSign** supports tz4; **Ledger does not** for baking operations.

See also: [tz1 / tz4](#tz1--tz4-address-types), [TezSign](/tezsign/tutorials/).

---

### Cycle

A unit of time on the Tezos blockchain, measured in blocks. Under the **Tallinn** protocol (6-second blocks), one cycle is approximately **1 day**. Many protocol parameters — rewards, rights, staking changes — are denominated in cycles.

| Protocol era | Blocks per cycle | Cycle duration |
|---|---|---|
| Pre-Tallinn | 8,192 | ~2.8 days |
| **Tallinn+** | **12,288** | **~1 day** |

> **ℹ️ INFO:** Older documentation may refer to cycles as lasting ~2–3 days. Since Tallinn, a cycle is approximately 1 day.

---

### DAL (Data Availability Layer)

A scalability layer added to Tezos that allows large amounts of data to be made available to the network without storing all of it on-chain. Bakers participate in DAL by downloading and attesting to data shards. **DAL participation is now mandatory** for bakers — failure to run a DAL node will result in missed rewards.

> **💡 TIP:** See [TezBake DAL setup](/tezbake/tutorials/baking-on-mainnet/) to enable the DAL node alongside your baker.

---

### Delegation

The act of assigning your XTZ's baking power to a baker without giving up custody of your funds. Delegated XTZ never leaves your wallet. Bakers receive increased block production rights proportional to delegations, and in return distribute a share of rewards to delegators. Delegators face **no slashing risk**.

See [Public Baking](/getting-started/public-baking/) for baker-side configuration.

---

### Double Baking / Double Attesting

A protocol violation in which a baker signs two conflicting blocks or attestations for the same slot. This is automatically detected and results in **slashing** — the baker loses a portion of their frozen stake. Always ensure only one signing device is active for a baker address at any time.

> **⚠️ WARNING:** Never run two signers for the same baker address simultaneously. See [Slashing Explained](/getting-started/slashing-explained/).

---

### Edge (of baking over staking)

The fee a baker charges on staker rewards, expressed as a decimal from 0 to 1. An edge of `0.1` means the baker takes 10% of staker reward income as a fee. This is configured via `edge_of_baking_over_staking` in the baker's on-chain parameters.

See [Public Baking](/getting-started/public-baking/).

---

### HWM (High Watermark)

A monotonically increasing counter maintained by the signing device (TezSign or Ledger) that records the highest block level and round ever signed. The HWM prevents a signer from ever signing a block at a level it has already signed — the primary defence against accidental double baking.

> **⚠️ WARNING:** Resetting or tampering with the HWM is dangerous. Only do so if explicitly instructed by the TezBake documentation.

---

### LB (Liquidity Baking)

A protocol-level automated market maker (AMM) baked into Tezos that provides XTZ/tzBTC liquidity. Each block, the protocol mints a small amount of XTZ into the LB contract. Bakers vote each block on whether to continue or sunset LB via the `liquidity_baking_toggle_vote` field.

---

### Nonce Revelation

Each cycle, one baker per block is selected to commit to a random seed. This is a two-step process: the baker includes a hash (commitment) in their block during the cycle, then reveals the pre-image (the nonce) in the following cycle. Failure to reveal results in **nonce forfeiture** — a small reward penalty.

> **💡 TIP:** TezBake handles nonce revelation automatically. If your node was down during the revelation window you may see a forfeiture warning — see [FAQ](/faq/#what-is-nonce-forfeiture-and-how-do-i-avoid-it).

---

### POP (Proof of Possession)

A cryptographic proof that the holder of a key actually has access to the corresponding private key. Required when registering or updating a consensus key to prevent key-squatting attacks.

---

### Rights

The schedule of which bakers are assigned to produce blocks or submit attestations at specific future slots. Rights are computed at the start of each cycle based on each baker's stake weight, and are known **2 cycles in advance**. A baker must be operational when their rights come due or they will miss the reward.

---

### Slashing

A protocol penalty applied when a baker provably misbehaves (e.g., double baking or double attesting). A portion of the baker's **frozen stake** — and potentially their stakers' frozen stake — is forfeited. Downtime alone (hardware failure, power outage) does **not** trigger slashing.

See [Slashing Explained](/getting-started/slashing-explained/).

---

### Staking

A mechanism introduced with Adaptive Issuance that allows external participants (stakers) to freeze XTZ with a specific baker to increase that baker's baking power. Stakers earn higher rewards than delegators but their funds are subject to a **~3-day unstaking period** and carry **slashing risk** proportional to their stake.

See [Public Baking — Understanding Staking](/getting-started/public-baking/).

---

### TezSign

A purpose-built hardware signing device for Tezos baking, implemented on single-board computers (Raspberry Pi Zero 2W, Radxa Zero 3). Connects via USB to the baking node. Recommended for all new bakers. Supports tz4 consensus keys.

See [TezSign documentation](/tezsign/tutorials/).

---

### tz1 / tz4 Address Types

Tezos addresses encode the key type in their prefix:

| Prefix | Key type | Algorithm | Notes |
|--------|----------|-----------|-------|
| `tz1` | Ed25519 | EdDSA | Original baking key type; supported by Ledger |
| `tz2` | Secp256k1 | ECDSA | Bitcoin-style curve |
| `tz3` | P-256 | ECDSA | NIST P-256 curve |
| `tz4` | BLS12-381 | BLS | Modern consensus key; supports aggregation; **not supported by Ledger for baking** |

For baking, `tz1` and `tz4` are the most relevant. TezSign supports both; Ledger supports only `tz1` for baking operations.

---

### Voting / Governance

Tezos uses an on-chain governance process where bakers vote on protocol upgrade proposals over several periods. Voting rights are proportional to stake. TezBake bakers can participate using [TezGov](/tezgov/).

> **ℹ️ INFO:** If you bake with a Ledger, you must stop the baker before voting, as you cannot use the Ledger for two operations simultaneously on the same machine. With TezSign + a backup device, you can vote without stopping baking.

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
