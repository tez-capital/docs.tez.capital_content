---
title: "Per-Block Votes"
weight: 6
type: docs
summary: "How to configure Liquidity Baking and Adaptive Issuance votes on your baker"
---

# Per-Block Votes

Every time your baker produces a block, it casts two automatic votes embedded in the block header. These are separate from [protocol upgrade voting via TezGov](https://gov.tez.capital) — per-block votes are configured directly on your baking node.

---

## The Two Per-Block Votes

### 1. Liquidity Baking (LB) Toggle

Liquidity Baking is a protocol feature that mints approximately **2.5 tez per block** into a tez/tzBTC liquidity pool on-chain. This subsidy has been active since the Granada protocol upgrade (2021).

| Vote | Meaning |
|------|---------|
| `"on"` | Continue the LB subsidy (default) |
| `"off"` | Vote to end the LB subsidy |
| `"pass"` | Abstain from this vote |

**How it works:** If enough bakers vote `"off"` over a rolling window of approximately 2,000 blocks, the protocol will pause the subsidy. If the vote flips back, it resumes.

### 2. Adaptive Issuance Toggle

Adaptive Issuance dynamically adjusts block rewards based on the network's global staking ratio. The protocol targets approximately **50% of total tez staked**:

- When the staking ratio is **below** the target → rewards **increase** to incentivize staking
- When the staking ratio is **above** the target → rewards **decrease** to reduce inflation

| Vote | Meaning |
|------|---------|
| `"on"` | Support adaptive issuance (default) |
| `"off"` | Oppose adaptive issuance |
| `"pass"` | Abstain from this vote |

---

## How to View Your Current Votes

Your votes are stored in a JSON file on your baking node:

```bash
cat /bake-buddy/node/data/vote-file.json
```

Example output:

```json
{
  "liquidity_baking_toggle_vote": "on",
  "adaptive_issuance_vote": "on"
}
```

---

## How to Change Your Votes

1. **Edit the vote file:**

```bash
nano /bake-buddy/node/data/vote-file.json
```

2. **Set your preferred values:**

```json
{
  "liquidity_baking_toggle_vote": "on",
  "adaptive_issuance_vote": "on"
}
```

Changes take effect immediately — the baker reads the vote file every time it produces a block. **No restart required.**

---

## TezBake Defaults

When you first set up TezBake, the vote file is created with these defaults:

| Vote | Default |
|------|---------|
| Liquidity Baking | `"on"` |
| Adaptive Issuance | `"on"` |

These defaults reflect the current community consensus. You are free to change them at any time.

---

## Important Notes

- **Per-block votes are NOT the same as protocol upgrade votes.** Protocol upgrade voting (YAY/NAY/PASS on proposals like Paris, Tallinn, etc.) is handled through [TezGov](https://gov.tez.capital). Per-block votes are configured on your node.
- **TezGov does NOT manage per-block votes.** You must edit the vote file manually on your baking node.
- **Changes take effect immediately.** The baker reads the vote file each time it bakes a block — no restart needed.
- **Your vote is public.** Anyone can see your per-block votes by inspecting the blocks you produce on a block explorer.

---

## FAQ

**Q: What happens if I delete the vote file?**
A: Your baker will use the protocol defaults, which may vary by upgrade. It's best to always have an explicit vote file.

**Q: Can I change my vote at any time?**
A: Yes. Just edit the file — your new vote will be picked up the next time you bake a block. No restart needed.

**Q: Does my voting power depend on my stake?**
A: Yes — bakers with more staking power produce more blocks and therefore cast more votes. The per-block vote mechanism is stake-weighted by nature.

**Q: Where can I see the current LB and AI vote tallies?**
A: Block explorers like [TzKT](https://tzkt.io) show per-block vote distributions. You can also check the current state on [tezos.systems](https://tezos.systems).
