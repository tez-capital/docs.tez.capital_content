---
title: "Slashing Explained"
weight: 4
type: docs
summary: Understanding Tezos slashing penalties, how they're calculated, and how to avoid them
---

## What is Slashing?

Slashing is a penalty mechanism that confiscates a portion of a baker's (and their stakers') security deposit when they commit protocol violations. It protects the network from malicious behavior by making attacks economically costly.

## Slashable Offenses

There are two types of protocol violations that trigger slashing:

### Double Baking

Double baking occurs when a baker signs two different blocks at the same block height (level) and round. This is considered an attempt to fork the blockchain.

**Common causes:**
- Running two baker instances with the same key simultaneously
- Restoring a backup signer without properly deactivating the original
- Using the same Ledger/TezSign device on multiple machines

**Penalty:** 5% of the baker's total stake

### Double Attestation

Double attestation (formerly "double endorsing") occurs when a baker signs two conflicting attestations for the same block slot.

**Common causes:**
- Same as double baking - running duplicate baker setups
- Network issues causing retry logic to sign twice

**Penalty:** Calculated using an adaptive formula (see below)

## How Slashing Amounts Are Calculated

### Double Baking: Fixed Percentage

Double baking incurs a **fixed 5% penalty** of the delegate's total stake. This replaced the previous fixed 640 tez penalty to scale appropriately with stake size.

### Double Attestation: Adaptive Quadratic Formula

Double attestation uses **adaptive slashing** - the penalty scales based on how much of the network's attesting power was involved in misbehavior:

**Formula:** `S(B) = min(100%, (1/T²) × f(B)² × 100%)`

Where:
- `f(B)` = fraction of double attestations in block B
- `T` = threshold (typically 1/3 of consensus committee size)

| Misbehavior Fraction | Approximate Penalty |
|----------------------|---------------------|
| 1% | ~0.09% |
| 5% | ~2.25% |
| 10% | ~9% |
| 20% | ~36% |
| 33%+ | Up to 100% |

This design ensures:
- **Isolated accidents** (small fraction) result in minimal penalties
- **Coordinated attacks** (large fraction) face severe consequences

## Slashing Distribution: Baker vs Stakers

When a baker is slashed, the penalty is distributed **proportionally to stake contribution**:

- If a baker has 60% of total stake and stakers have 40%, the baker pays 60% of the penalty and stakers collectively pay 40%
- Each staker's share is proportional to their individual contribution to the baker's staking balance

> **ℹ️ Important:** The `edge_of_baking_over_staking` parameter affects **reward distribution only**, not slashing. Slashing is always proportional to stake regardless of edge setting.

### Overstaked Funds

If stakers exceed the baker's `limit_of_staking_over_baking`, the excess is "overstaked" - it remains frozen and slashable but doesn't contribute to baking power. This is a bad scenario for stakers who get no benefit but still face slashing risk.

## The Denunciation Process

Slashing doesn't happen automatically. Another network participant must **denounce** the misbehavior:

1. A baker commits a slashable offense (double bake/attest)
2. Any node can detect the conflicting signatures
3. A denouncer submits proof to the blockchain
4. The protocol verifies the evidence
5. Slashing occurs at the end of the cycle
6. The denouncer receives **1/11 of the slashed amount** as a reward

**Timing Constants:**
- **Denunciation period**: 1 cycle - denouncements must be submitted within this window
- **Slashing delay**: 1 cycle - slashing is applied at the end of the cycle after denunciation

> **ℹ️ Note:** The denunciation reward was reduced from 1/2 to 1/11 to prevent adversarial delegates from profiting by intentionally misbehaving and self-denouncing at the expense of their stakers.

## The Forbidden Period

Upon denunciation, a **forbidden period** begins immediately:

- **Duration:** Minimum 2 cycles
- **Effect:** Baker cannot propose blocks or make attestations
- **Lifting conditions:**
  1. All pending slashings have been applied
  2. Total frozen stake recovers to match the active stake from prior cycles

## Preventing Slashing

### Technical Safeguards

1. **High Watermark (HWM)** - Your signer tracks the highest block level it has signed and refuses to sign anything at or below that level
2. **Single signer rule** - Never run multiple signers with the same key
3. **Proper backup procedures** - Only activate backup hardware after confirming primary is fully stopped

### Operational Best Practices

| Do | Don't |
|----|-------|
| Use dedicated baking hardware | Share your baking computer for other tasks |
| Keep backup signer unauthorized until needed | Pre-authorize backup "just in case" |
| Set HWM to current block height when migrating | Leave HWM at 1 when moving to a used device |
| Wait for full shutdown before failover | Rush failover during outages |
| Use separate devices for mainnet and testnet | Reuse the same signer across networks |

### TezBake Protections

TezBake includes built-in safeguards:
- Automatic HWM management during setup
- Clear warnings about double-baking risks
- Single-instance enforcement

## Historical Context

Since the start of the Tezos mainnet, slashing has been extremely rare - occurring in only **0.003%** of over 5.9 million blocks. Most slashing events are accidental rather than malicious attacks.

## Checking Your Status

You can verify your baker hasn't been denounced by checking:
- [TzKT](https://tzkt.io) - Search your baker address and check for denunciation operations
- [TzStats](https://tzstats.com) - View baker history and any penalties

## What To Do If Slashed

If you discover you've been slashed:

1. **Stop all baking immediately** - Prevent further offenses
2. **Identify the cause** - Check logs for duplicate signing evidence
3. **Wait out the forbidden period** - Minimum 2 cycles
4. **Fix the root cause** - Ensure only one signer is active
5. **Restart carefully** - Verify HWM is set correctly before resuming

---

## Related Guides

* [What is Baking?](/getting-started/what-is-baking/) - Baking fundamentals
* [Best Practices](/getting-started/best-practices/) - Prevent operational issues
* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - Secure signer setup
* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Complete setup guide

---

**Sources:**
- [Octez Adaptive Slashing Documentation](https://octez.tezos.com/docs/active/adaptive_slashing.html)
- [Octez Staking Mechanism Documentation](https://octez.tezos.com/docs/active/staking.html)

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
