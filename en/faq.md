---
title: "FAQ"
weight: 100
type: docs
summary: "Frequently asked questions about Tezos baking with Tez Capital tools"
---

## Frequently Asked Questions

Quick answers to the most common questions. Click the links for full guides where available.

---

### How long until I get my first baking rights?

After you register your baker and stake XTZ, you can expect your first baking rights within **up to 2 cycles** (~2 days). Rights are computed at the beginning of each cycle for the cycle 2 periods ahead. If you register near the end of a cycle, you may wait up to 2 full cycles before baking.

> **ℹ️ INFO:** You must have the minimum required stake (currently 6,000 XTZ) to be assigned rights. Check your rights on [TzKT](https://tzkt.io) by searching your baker address.

**→ More:** [What is Baking?](/getting-started/what-is-baking/)

---

### My node says "bootstrapping" for hours — is it stuck?

Almost certainly not. Bootstrapping is the initial chain synchronisation process and is completely normal. On a first install or after a long downtime, bootstrapping typically takes:

- **30 minutes** on fast hardware with a good internet connection
- **1–3 hours** on modest hardware
- **Longer** if your network is slow or you are far from peers

You can watch progress in the logs:

```bash
tezbake log node
```

The node is stuck only if the block level shown has not increased for more than 10–15 minutes.

> **💡 TIP:** The TezBake installer will wait for bootstrapping to complete before starting the baker. This is expected behaviour, not a hang.

---

### Can I use my Ledger for baking and daily transactions at the same time?

**No — not on the same machine simultaneously.** The Ledger device can only handle one Tezos operation at a time. If you interact with it (e.g., confirming a transaction in a wallet app), the baker process may fail to get a signature response and miss an attestation.

Best practice:
- Use your **primary Ledger** exclusively for baking
- Use a **separate Ledger or software wallet** for daily spending
- If you need to use the baking Ledger for a transaction or governance vote, **stop the baker first**, complete the operation, then restart

> **💡 TIP:** TezSign eliminates this problem. A dedicated TezSign device is used only for baking, so your daily wallet and baking operations never conflict. See [Choose Your Setup](/getting-started/choose-your-setup/).

---

### How do I know if DAL is working?

DAL (Data Availability Layer) participation is now **mandatory** for bakers. If your DAL node is not running or not attesting, you will see reduced rewards.

Check DAL node status:

```bash
tezbake status
```

Look for `dal-node` in the output — it should show as running. You can also check on [TzKT](https://tzkt.io) by looking at your recent attestations; DAL attestations are indicated separately from regular attestations.

> **ℹ️ INFO:** If DAL was not part of your original TezBake installation, re-run the setup to add it. See [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/).

---

### What happens if my internet goes down?

Your baker will miss blocks and attestations during the outage. This means **missed rewards**, but no slashing — Tezos does not slash for downtime.

When your internet comes back, the node will re-sync (usually quickly if the outage was short) and resume baking at the next available right.

> **💡 TIP:** A UPS (uninterruptible power supply) on both your baking node and your ISP router will keep you online during brief power outages. For longer outages, there is nothing to do except wait for connectivity to return. See [Hardware Requirements](/getting-started/hardware-requirements/).

---

### Do I need to do anything during protocol upgrades?

Generally, **no manual action is required** if you are running TezBake. The Tezos node automatically activates the new protocol at the designated block level.

However, you should:

1. **Monitor the TezBake changelog** and Tez Capital announcements before known upgrade dates
2. **Update TezBake** if a new version is recommended for the new protocol:
   ```bash
   tezbake upgrade
   ```
3. **Re-check your configuration** after major upgrades — some parameter names or defaults may change

> **⚠️ WARNING:** Some major protocol upgrades (like the introduction of DAL) add new **mandatory** components. Always read the upgrade announcement carefully. See [What's New](/changelog/).

---

### How do I check if my baker is actually baking?

```bash
tezbake status
```

This shows whether all services (node, baker, accuser, DAL node, TezSign/Ledger) are running.

To verify your baker is actively participating:
1. Find your baker address (tz1… or KT1…)
2. Look it up on [TzKT](https://tzkt.io) or [Baking Bad](https://baking-bad.org)
3. Check the **Baking** tab for recent blocks baked and attestations submitted

You should also set up [TezWatch](/tezwatch/) monitoring so you receive instant alerts if your baker goes offline.

---

### What is nonce forfeiture and how do I avoid it?

Each cycle, some bakers are selected to commit a random seed (nonce). The process has two steps:

1. **Commit** — include a hash in your block (done automatically by TezBake)
2. **Reveal** — broadcast the pre-image in the following cycle (also done automatically)

If your node was **offline during the revelation window**, TezBake cannot reveal the nonce and you will receive a forfeiture penalty (a small reward reduction for that cycle).

**To avoid nonce forfeiture:**
- Ensure your baker has good uptime around cycle boundaries
- Set up [TezWatch](/tezwatch/) alerts so you know immediately if your baker goes offline

> **ℹ️ INFO:** Nonce forfeiture is a minor penalty and does not affect your stake. It only affects the reward for the cycle where the revelation was missed.

---

### How do I migrate from Ledger to TezSign?

Migration involves:
1. Setting up TezSign on a Pi Zero 2W or Radxa Zero 3
2. Updating your baker's consensus key to point to TezSign
3. Optionally rotating to a tz4 key (recommended)
4. Decommissioning the Ledger from baking

Full step-by-step instructions: **[Baking with TezSign](/tezsign/tutorials/)**

> **⚠️ WARNING:** Never have both the Ledger and TezSign active for the same baker address at the same time — this would cause double baking and result in slashing. See [Slashing Explained](/getting-started/slashing-explained/).

---

### TezPay paid the wrong amount — what do I do?

First, check the TezPay payout log for that cycle to understand what was calculated and why:

```bash
tezpay generate-payouts
```

Common reasons for unexpected amounts:

| Symptom | Likely cause |
|---------|-------------|
| Delegator received less than expected | Baker fee applied, or delegator was below minimum payout threshold |
| Delegator received nothing | Balance was below minimum delegation threshold, or was blacklisted |
| Baker received more / delegator less | `edge_of_baking_over_staking` or fee setting is higher than intended |
| Payout for wrong cycle | TezPay pays after a configurable delay — check `payment_offset` in config |

> **💡 TIP:** Run `tezpay generate-payouts --cycle <N>` to preview what would be paid for a specific cycle without sending anything.

**→ More:** [TezPay Setup](/tezpay/tutorials/setup/) | [TezPay Troubleshooting](/tezpay/tutorials/troubleshooting/)

If the issue persists, share your TezPay config (with sensitive data removed) in the [Discord](https://discord.gg/cVGMA4MaNM) for help.

---

### How do I set up monitoring alerts?

Use [TezWatch](/tezwatch/) to receive Telegram or Discord notifications when:
- Your baker misses a block or attestation
- A service goes down
- Your baker is scheduled to bake soon

```bash
tezbake setup --add tezwatch
```

**→ More:** [TezWatch documentation](/tezwatch/)

---

### I see "missed endorsements" / "missed attestations" in my stats — is something wrong?

An occasional missed attestation is normal and expected (network latency, momentary CPU spike, etc.). The protocol requires only a **67% attestation rate** per cycle to receive full rewards.

If you are consistently missing more than 33% of attestations, investigate:
- Is the baker process running? (`tezbake status`)
- Is the signer (TezSign or Ledger) connected and responsive?
- Is the node fully synced?
- Is DAL running and keeping up?

> **💡 TIP:** Set up TezWatch alerts to catch problems in real time rather than discovering them after the fact.

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
