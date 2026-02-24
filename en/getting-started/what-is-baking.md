---
title: "What is Tezos Baking"
weight: 1
type: docs
summary: What is Tezos Baking?
---

## What is Tezos Baking?

"Baking" is Tezos terminology for validating transactions and creating new blocks on the blockchain. The term comes from the process of "baking" new blocks into the chain. If you're familiar with other blockchains, bakers on Tezos are equivalent to validators on Ethereum.

Fundamentally, baking on Tezos involves 2 responsibilities:

1. **Block Production & Attestation** - Creating new blocks every 6 seconds and attesting (validating) blocks produced by other bakers. Bakers stake their tez (Tezos tokens) as collateral to participate. This staked tez acts as a security deposit — honest bakers earn rewards, while malicious behavior (like creating or validating invalid blocks) results in losing the staked tez through [slashing](/getting-started/slashing-explained/).

2. **Governance Participation** - Voting on protocol upgrade proposals that can modify any aspect of the Tezos protocol, including technical changes and funding allocations from the protocol treasury.

---

## Who Should Bake?

Baking is open to anyone, but there are two main paths:

### Solo Baking
Run your own baker node and sign blocks yourself. You keep all the rewards. Requirements:
- **Minimum 6,000 XTZ** staked as a security deposit (can be partially covered by delegators and stakers — see below)
- A dedicated computer running 24/7 (see [hardware recommendations](/getting-started/best-practices/))
- Some technical comfort with Linux

### Baking via a Delegation Service
If you have less than 6,000 XTZ or don't want to run hardware, you can **delegate** your XTZ to an existing baker. You give up nothing — your XTZ stays in your wallet. The baker earns rewards and shares a portion with you.

> **💡 TIP:** Delegation is risk-free and requires no technical setup. Solo baking earns more but requires hardware and ongoing attention.

---

## Staking vs. Delegation — Simply Explained

These two terms are often confused:

| | **Staking** | **Delegation** |
|---|---|---|
| **What you do** | Lock XTZ as a slashable security deposit | Point your XTZ at a baker (no lockup) |
| **Risk** | Your XTZ can be slashed if the baker misbehaves | No risk — XTZ never leaves your wallet |
| **Rewards** | Higher (~full protocol rate) | Lower (~⅓ of staking rewards) |
| **Who does it** | Bakers and stakers who trust their baker | Anyone with any amount of XTZ |

**In short:** Stakers earn more but share the baker's slashing risk. Delegators earn less but bear no risk.

---

## Hardware Overview

Baking requires a computer running 24/7 with a reliable internet connection. The good news: you don't need powerful hardware — a modest machine handles baking comfortably.

**Minimum specifications:**
- 3 CPU cores (arm64 or amd64)
- 8 GB RAM + 8 GB swap (or 16 GB RAM)
- 100 GB SSD storage
- Low-latency broadband internet

See the full [hardware and best practices guide](/getting-started/best-practices/) for recommended setups including backup hardware and UPS.

---

## Expected Returns

Baking rewards on Tezos are approximately **5–6% APY**, but this varies based on:

- The network's overall staking ratio (Adaptive Issuance adjusts rewards automatically)
- Whether you're a solo baker, staker, or delegator
- Baker fees (if using a delegation service)

> **ℹ️ INFO:** Rewards are not fixed. The Tezos protocol targets 50% of all XTZ staked and adjusts issuance rates accordingly. When staking is low, rewards increase; when staking is high, rewards decrease.

---

## Risks

### Slashing
The most important risk to understand. If a baker **double bakes** (signs two conflicting blocks) or **double attests**, the protocol automatically confiscates a portion of the staked XTZ. This is rare and almost always caused by running the same key on two machines simultaneously.

> **⚠️ WARNING:** If you stake with a baker and that baker gets slashed, **your staked XTZ is also at risk**. Delegators (not stakers) are immune to slashing.

See [Slashing Explained](/getting-started/slashing-explained/) for the full details on penalties.

### Nonce Forfeiture
Bakers occasionally produce blocks with a nonce (a random number commitment). If you re-bootstrap your node mid-cycle and lose this data, you may forfeit attestation rewards for that cycle.

---

## Time Commitment

Baking is **mostly automated** — once your baker is set up and running, it operates on its own. However, you should plan for:

- **Daily:** A quick check that your baker is healthy (1–2 minutes)
- **Weekly:** Review logs for any missed attestations or alerts
- **As needed:** Protocol upgrades (usually every 3 months) require updating TezBake
- **Ongoing:** Monitoring hardware, internet stability, and UPS battery

> **💡 TIP:** Tools like [TezPeak](/tezpeak/tutorials/setup/) provide a web dashboard so you can check your baker status at a glance without SSH. [TezWatch](/tezwatch/tutorials/setup/) sends Discord/Telegram alerts when something goes wrong.

---

## Watch: Tezos Baking Introduction

{{< youtube t85LjgpRtvc >}}

---

## Ready to Start?

1. **Choose your setup** → [Choose Your Setup](/getting-started/choose-your-setup/)
2. **Follow the full guide** → [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/)

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
