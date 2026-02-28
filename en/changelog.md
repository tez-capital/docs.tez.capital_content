---
title: "What's New"
weight: 99
type: docs
summary: "Recent changes to Tez Capital tools and documentation"
---

## What's New

A reverse-chronological log of significant changes to Tez Capital tools, the Tezos protocol, and this documentation. Subscribe to [Tez Capital on Telegram](https://t.me/tezcapital) or [Discord](https://discord.gg/cVGMA4MaNM) to stay up to date.

---

## 2026

### Documentation: Reverting from tz4 to tz1/tz3 Keys

**What changed:** Added a new section to [Baking with Consensus Key](/tezbake/tutorials/baking-with-consensus-key/) explaining how to revert from tz4/BLS (TezSign) back to tz1/tz3 signing. Key steps: re-import your tz1/tz3 key, set both consensus and companion keys to the same tz1/tz3 address via TezGov, wait 3 cycles, then remove the companion alias from TezBake config.

---

## 2025

### DAL Now Mandatory for Bakers

**What changed:** The Data Availability Layer (DAL) is now a required component for all active bakers. Previously optional, DAL participation is now enforced at the reward level — bakers who do not run a DAL node and attest to DAL shards will receive reduced rewards.

**What to do:** If you installed TezBake before DAL was released, re-run the setup to add the DAL node:

```bash
tezbake setup
```

> **⚠️ WARNING:** Bakers who skip this step will see lower-than-expected rewards each cycle.

**→ See:** [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/)

---

### TezSign Now Recommended Over Ledger

**What changed:** TezSign is now the officially recommended signing solution for Tezos bakers, replacing Ledger as the default recommendation. This reflects the maturity of the TezSign platform, its support for tz4 keys, and the limitations of Ledger for modern baking.

**Key reasons for the change:**
- Ledger hardware wallets do not support **tz4 (BLS12-381) consensus keys** for baking
- TezSign is purpose-built for 24/7 baking and does not conflict with governance operations
- TezSign hardware (Pi Zero 2W) costs ~$20 — significantly cheaper than a Ledger

**Ledger is still supported** for existing bakers. There is no urgent need to migrate unless you want tz4 keys or a cleaner setup.

**→ See:** [Choose Your Setup](/getting-started/choose-your-setup/) | [Baking with TezSign](/tezsign/tutorials/)

---

### Tallinn Protocol: Faster Blocks, Shorter Cycles

**What changed:** The **Tallinn** protocol upgrade changed the fundamental time constants of the Tezos blockchain:

| Parameter | Before Tallinn | After Tallinn |
|-----------|---------------|--------------|
| Block time | ~15 seconds | **6 seconds** |
| Blocks per cycle | 10,800 | **14,400** |
| Cycle duration | ~2.8 days | **~1 day** |

**Impact on bakers:**
- Baking rights are now scheduled in advance for a shorter window
- Reward and staking parameter changes (which take effect after N cycles) now apply faster
- Monitoring and alerting are more time-sensitive — a few minutes of downtime is a larger fraction of a cycle

> **ℹ️ INFO:** Any documentation or community resource that refers to cycles as lasting "~3 days" or "~2-3 days" is outdated. Since Tallinn, one cycle ≈ 1 day.

---

### tz4 Consensus Keys

**What changed:** Tezos now supports **tz4 (BLS12-381)** addresses as consensus keys. BLS keys enable more efficient signature aggregation, which improves network performance at scale.

**Key facts:**
- tz4 keys are supported by **TezSign** (Pi Zero 2W and Radxa Zero 3)
- tz4 keys are **not supported by Ledger** hardware wallets for baking operations
- Bakers currently using tz1 keys can continue without any changes
- To use tz4, you must set up or migrate to TezSign

**→ See:** [Glossary — tz1/tz4](/getting-started/glossary/#tz1--tz4-address-types) | [Choose Your Setup](/getting-started/choose-your-setup/)

---

### TezPay: Staker-Aware Payouts

**What changed:** TezPay now correctly accounts for the staker/delegator split introduced by Adaptive Issuance. Staker rewards are distributed directly by the protocol; TezPay handles delegator rewards.

**→ See:** [TezPay Setup](/tezpay/tutorials/setup/)

---

## Documentation Updates

### New Pages Added

- **[Choose Your Setup](/getting-started/choose-your-setup/)** — Decision tree and comparison table for signing options (TezSign vs Ledger)
- **[Hardware Requirements](/getting-started/hardware-requirements/)** — Minimum and recommended specs for baking nodes and TezSign devices
- **[Glossary](/getting-started/glossary/)** — Definitions of key Tezos baking terms
- **[FAQ](/faq/)** — Unified frequently asked questions with short answers and links to full guides

### Updated Pages

- **[Best Practices](/getting-started/best-practices/)** — Added TezSign as primary recommendation, updated hardware signer guidance
- **[Ledger vs TezSign](/getting-started/ledger-vs-tezsign/)** — Clarified tz4 limitation for Ledger
- **[Getting Started](/getting-started/)** — Updated learning path to include new pages

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
