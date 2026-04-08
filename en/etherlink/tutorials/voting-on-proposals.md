---
title: "Voting on Etherlink Proposals"
weight: 10
type: docs
summary: How to vote on Etherlink kernel and sequencer upgrade proposals using TezGov
---

## Etherlink governance overview

Etherlink is governed by Tezos bakers through an on-chain mechanism similar to Tezos protocol voting. Bakers vote on **kernel upgrades** (slow or fast track) and **sequencer operator changes** using a smart contract on Tezos layer 1.

The voting mechanics mirror Tezos governance:

| Stage | Tezos | Etherlink (slow) | Etherlink (fast) | Etherlink (sequencer) |
|-------|-------|-------------------|-------------------|------------------------|
| Submit/upvote proposals | ✓ | ✓ | ✓ | ✓ |
| Voting period | Exploration + Promotion | Proposal + Promotion | Proposal + Promotion | Proposal + Promotion |
| Quorum | Dynamic (20–70%) | 5% | 5% Proposal / 15% Promotion | 1% Proposal / 8% Promotion |
| Supermajority | 80% | 75% | 80% | 75% |
| Testing | Cooldown + Adoption | Cooldown | Cooldown | Cooldown |
| Period length | ~14 days each | ~4.5 days each | ~8 hours each | ~4.5 days each |

All Etherlink governance is handled **on Tezos L1** — you don't need a separate Etherlink node or account. Your voting power is determined by the Tez staked with your baker.

## Prerequisites

- **[Running baker on mainnet](/tezbake/tutorials/baking-on-mainnet/)** — you must be an active registered baker with staked Tez
- **Staked XTZ** — enough to meet the minimum bond requirement for your baker
- **Ledger hardware wallet** with the Tezos **Wallet** app installed (not the Baking app)
- **[TezGov web portal access](https://gov.tez.capital)** — works best with Chromium-based browsers (Chrome, Brave, Edge)
- **Etherlink voting key** — your baker's Etherlink voting key is derived from the same Ledger seed as your Tezos baker key. TezGov handles the derivation automatically when you vote on Etherlink proposals.

## Understanding what you're voting on

Etherlink has three separate governance tracks:

**Kernel (slow)** — For major kernel upgrades. ~4.5 days for Proposal, ~4.5 days for Promotion, then a ~1 day Cooldown. Designed for larger changes that need more evaluation time.

**Kernel (fast)** — For smaller, simpler, or urgent changes. ~8 hours per period. Higher quorum requirements (15% in Promotion vs 5% for slow). Code may be shared with only a subset of bakers before approval to avoid exposing unpatched vulnerabilities.

**Sequencer** — For selecting the sequencer operator. Same timing as slow kernel (~4.5 days each period). Passed proposals trigger a sequencer operator transition.

All three tracks use the same vote interface in TezGov — you'll select which governance track you want to vote on.

## Proposal voting with TezGov

TezGov allows you to participate in Etherlink governance using your Ledger hardware wallet, with the same login flow as Tezos mainnet voting.

> **💡 Use a secondary Ledger device** (with the same seed) to vote while your primary device is baking. Or briefly stop your baker, vote, then restart.

> **⚠️ Voting while baking:** If you're voting from your baking machine while your baker is running, stop the baker first:
> ```bash
> sudo tezbake stop
> ```
> Vote via TezGov, then restart:
> ```bash
> sudo tezbake start
> ```
> Failure to stop the baker may result in a "Transaction Not Trusted" error — the Ledger Wallet app cannot share the device with a running baker process.

---

## Step-by-step

### 1. Connect to TezGov

Navigate to [https://gov.tez.capital](https://gov.tez.capital) and log in using **Ledger Direct** (recommended — you'll see the transaction details on your Ledger screen before signing).

Alternatively use **Remote signer** (if you have a signer on your LAN) or **Beacon wallet** (Temple, Kukai, etc. — you won't see specific vote details on the Ledger).

### 2. Navigate to Etherlink Governance

Once logged in, look for a **governance selector** or tab in the TezGov interface that lets you switch between **Tezos** and **Etherlink** governance tracks.

Select the relevant Etherlink governance track (Kernel / Fast Kernel / Sequencer) based on which proposal you want to vote on.

> **💡** Check [governance.etherlink.com](https://governance.etherlink.com) for a list of active proposals and their current period.

### 3. Review the Proposal

During the **Proposal period**, you'll see the list of submitted kernel or sequencer proposals. Each proposal shows:
- The proposed kernel/sequencer version or address
- The submitting baker
- Current support (% of voting power gathered)

You can submit a new proposal or upvote an existing one. Bakers can support up to **20 proposals** in a single Proposal period.

### 4. Vote

During the **Promotion period**, the leading proposal is presented with **YAY**, **NAY**, and **PASS** buttons.

Click your vote and confirm on your Ledger screen. Your voting power is proportional to your staked Tez — the more XTZ staked with your baker, the more weight your vote carries.

After confirmation, the page refreshes within ~30 seconds to reflect your vote.

> **⚠️ PASS votes count toward quorum** (participation requirement) but don't affect the supermajority tally. If you want to support a proposal but don't feel strongly, PASS is better than abstaining.

### 5. Cooldown and Activation

After a successful Promotion vote, the **Cooldown period** (~24 hours) begins. During Cooldown, no voting is possible — this gives developers and bakers time to adapt their infrastructure.

At the end of the Cooldown, any Etherlink user can trigger the kernel upgrade. The exact timing depends on when this trigger is called.

---

## How voting power is calculated

Unlike Tezos mainnet where voting power is fixed at the start of a governance period, Etherlink's voting power is determined by the **baker's staked Tez at the time of each individual vote**. This means:

- Your voting power is dynamic — it reflects your current stake at the moment you cast each vote
- If your stake changes mid-period (e.g., from unstaking or new delegations), your next vote uses the updated amount
- All of your staked Tez counts, not just the frozen bond

---

## Synchronization with Tezos L1 governance

Etherlink governance periods are **synchronized with Tezos L1 governance periods**:

- Each Etherlink Proposal or Promotion period must fit entirely within a **single Tezos governance period** — it cannot span two Tezos periods
- This ensures each baker's voting power is consistent throughout the Etherlink period
- The start of a Tezos governance period always coincides with the start of an Etherlink Proposal or Promotion period
- Because Etherlink periods are shorter than Tezos periods, multiple Etherlink periods can occur within one Tezos governance period

This means timing is predictable: you can always check the current Tezos governance status to know where you are in the Etherlink cycle.

---

## Troubleshooting

### "Transaction Not Trusted" error

This happens when your Ledger is already in use by the baking app while trying to vote. Stop your baker before voting:

```bash
sudo tezbake stop
# vote via TezGov
sudo tezbake start
```

### No Etherlink proposals visible in TezGov

Check [governance.etherlink.com](https://governance.etherlink.com) to confirm whether a Proposal or Promotion period is active. If no period is active, TezGov may not show Etherlink voting options. Voting is only possible during active periods.

### Voting power shows zero or unexpected value

Your voting power is derived from your **current staked Tez** — not your total baker balance. Make sure your baker has staked XTZ. Also verify you are connected with the correct Ledger account (and that it's the right derivation path for your Etherlink voting key).

### Vote not registering on-chain

Votes are submitted as on-chain transactions to the Etherlink governance contract. If the transaction fails, check:
1. Is your baker active and staked?
2. Is the governance period still in the correct phase?
3. Is your Ledger transaction confirmed on-screen?

For support, contact the Tez Capital team on [Discord](https://discord.gg/tezcapital) or [Telegram](https://t.me/tezcapital).

---

## Further Resources

- [Etherlink Governance Docs](https://docs.etherlink.com/governance/how-is-etherlink-governed/)
- [Etherlink Governance Portal](https://governance.etherlink.com)
- [TezGov Voting on Mainnet](/tezgov/tutorials/voting-on-proposals/)
- [Managing Baker Settings](/tezgov/tutorials/managing-baker-settings/)
- [Tez Capital Discord](https://discord.gg/tezcapital)
