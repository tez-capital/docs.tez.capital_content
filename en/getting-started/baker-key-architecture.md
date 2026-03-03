---
title: "Baker Key Architecture"
weight: 3
type: docs
summary: "Understanding the three baker key roles — manager, consensus, and companion — and how they work together"
---

## Overview

Tezos baking uses three distinct key roles. Understanding what each one does, and how they relate to each other, is essential before setting up your baker — regardless of whether you are using TezSign, a Ledger, or soft keys.

---

## The Three Key Roles

| Role | Default alias | Purpose | Key type | Changeable? |
|------|--------------|---------|----------|-------------|
| **Manager key** | `baker` | Your baker's permanent identity on the blockchain. Controls funds, governance votes, staking, and has exclusive authority to set or rotate the consensus and companion keys. | Any (tz1/tz2/tz3/tz4) | ❌ Never — this is your baker address forever |
| **Consensus key** | `baker` (initially) | Signs blocks and attestations on behalf of the manager. By default this is the same address as the manager key. Can be rotated to a separate key at any time without changing your baker address. | Any (tz1/tz2/tz3/tz4) | ✅ Yes — 3-cycle activation delay |
| **Companion key** | `companion` | **Mandatory when your consensus key is tz4 (BLS).** Signs DAL-specific content in consensus operations. Think of it as "the DAL key." Always a separate tz4 key, distinct from the manager and consensus keys. | tz4 only | ✅ Yes — 3-cycle activation delay |

---

## How the Roles Relate

```
Manager key (Ledger)
  ├── Controls: staking, governance, parameters
  ├── Sets/rotates → Consensus key (TezSign)
  └── Sets/rotates → Companion key (TezSign)

Consensus key (TezSign)
  └── Signs: blocks, pre-attestations, attestations

Companion key (TezSign)
  └── Signs: DAL payloads within attestations
```

**Key principles:**

- The **manager always controls consensus**. It is the only key that can register or change the consensus and companion keys. The consensus key cannot modify its own registration.
- By default, manager = consensus. They are the same address until you explicitly set a separate consensus key.
- The **companion key is mandatory when your consensus key is tz4**. Without it, your baker will produce attestations without DAL payloads and forfeit approximately 10% of baking rewards.
- The companion key is **only used** when the active consensus key is tz4. If you are baking with a tz1/tz2/tz3 consensus key, no companion key is needed.

---

## Recommended Architecture

The recommended setup for new bakers is:

**Ledger** = manager key · **TezSign** = tz4 consensus key + tz4 companion key

| Device | Role | Why |
|--------|------|-----|
| **Ledger** | Manager | Stays cold. Used only for governance votes, staking changes, and registering/rotating TezSign keys. Ledger does not support tz4, so it holds a tz1/tz2/tz3 address. |
| **TezSign** | Consensus + Companion | Signs every block and attestation 24/7. tz4 BLS keys. Keys never leave the hardware device. |

**Why separate the manager from the signing keys?**

- Your funds are never on the hot signing path. TezSign cannot transfer your tez — only the manager key (Ledger) can authorize fund movements.
- If TezSign is ever compromised, an attacker can cause double-signing slashing but cannot drain your wallet. You can rotate the consensus key immediately from [TezGov](https://gov.tez.capital) using your Ledger.
- The Ledger remains essential even after full tz4 migration — it is not a stepping stone you graduate past. It is the root of trust for your baker.

---

## How Key Registration Works

When you want to activate a new consensus or companion key:

1. **Connect to [TezGov](https://gov.tez.capital) with your manager key (Ledger)**
2. Navigate to **Baker Management → Keys**
3. Provide the new key's public key (`BLpk...`) and its Proof of Possession (PoP)
4. Confirm on your Ledger
5. **Wait 3 cycles (~3 days)** for the new key to become active

> **ℹ️ Proof of Possession (PoP):** A cryptographic proof that you own the private key corresponding to the public key you are registering. Required for all tz4 key registrations. Generated automatically by TezBake/TezSign during setup, or via `tezbake signer client get proof of possession for <alias>` for soft keys.

The manager key signs the on-chain operation that registers the new consensus/companion keys. The consensus and companion keys themselves only need to be available on the signing device — they do not need to be accessible by the client at registration time (only the PoP is needed).

---

## Key Lifecycle

```
Setup                          Active baking                  Key rotation
─────                          ─────────────                  ────────────
Generate manager key     →     Manager stays cold       →     Manager signs rotation op
Generate consensus key   →     Consensus signs blocks   →     New consensus activated
Generate companion key   →     Companion signs DAL      →     New companion activated
Register via TezGov      →     (3-cycle delay)          →     (3-cycle delay)
```

**What happens during the 3-cycle transition:**
- Your old keys remain active and keep baking until the new keys activate
- Do not remove or stop your old signing setup during this window
- Once the new keys activate, the old consensus/companion keys are no longer used for baking (the manager key is permanent)

---

## Common Misunderstandings

**"I need to use my Ledger every time I bake"**
No. Once your TezSign consensus and companion keys are registered and active, your Ledger is only needed for governance votes, staking/unstaking, and future key rotations.

**"I can set my companion key to the same address as my consensus key"**
No. The companion key must be a distinct tz4 address. However, you *can* set both the consensus key and companion key back to your manager key's address if you are reverting from tz4 to tz1/tz2/tz3 — this effectively unregisters the separate companion.

**"The companion key is optional"**
It is optional only if your consensus key is tz1/tz2/tz3. If your consensus key is tz4, the companion key is mandatory for DAL participation. Skipping it means ~10% income loss.

**"Losing my TezSign means losing my baker"**
No. Your manager key (Ledger) is the permanent baker identity. If TezSign is lost or fails, use your Ledger in TezGov to register a new consensus key. Your baker address, delegators, and stakers are unaffected — only the signing device changes.

---

## Related Guides

- [Choose Your Setup](/getting-started/choose-your-setup/) — Compare TezSign, Ledger, and soft key signing options
- [Ledger vs TezSign](/getting-started/ledger-vs-tezsign/) — Detailed comparison
- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) — Full baker setup guide
- [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) — TezSign hardware setup
- [Baking with Consensus Key](/tezbake/tutorials/baking-with-consensus-key/) — Activating tz4 consensus and companion keys
- [Managing Baker Settings](/tezgov/tutorials/managing-baker-settings/) — Setting keys via TezGov

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
