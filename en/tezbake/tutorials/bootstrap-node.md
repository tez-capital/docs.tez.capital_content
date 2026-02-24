---
title: "Bootstrap Node"
weight: 7
type: docs
summary: How to bootstrap your Tezos node quickly using blockchain snapshots
---

Follow along on Youtube!
{{< youtube 2BQZ1SY3PD4 >}}

> **Quick Reference**
> ```bash
> tezbake stop
> tezbake bootstrap-node https://snapshots.tzinit.org/mainnet/rolling --no-check  # Fast, trusts source
> tezbake start
> ```

## Prerequisites

- **[TezBake installed and configured](/tezbake/tutorials/baking-on-mainnet/)** — TezBake must already be set up on your machine
- **Your baker stopped** — always run `tezbake stop` before bootstrapping to avoid data corruption

## TezBake Bootstrapping

To bootstrap your TezBake node means to download someone else's snapshot of the blockchain and import it into your node. This is much faster than synchronizing the blockchain from scratch. There is a way to quickly bootstrap your node using a snapshot and a way to bootstrap your node using a snapshot and a block hash. The latter is the most reliable and robust method but it is also the slowest. The former is faster but it assumes you trust the source of the snapshot.

---

> **🚨 CRITICAL: Nonce Forfeiture Risk**
>
> Re-bootstrapping your node mid-cycle can cause you to forfeit attestation rights for an entire cycle — meaning lost income.
>
> **To minimize risk:**
> 1. Use a snapshot that is **at least 5-6 days old**
> 2. Do **NOT** delete your `.tezos-client` directory
> 3. If possible, wait until a cycle boundary
>
> See the [detailed explanation below](#nonce-forfeiture-explained).

---

### Bootstrap using a snapshot

You can get Tezos node snapshots in the following place run by the Tezos Foundation

* <https://snapshots.tzinit.org/>

*Before bootstrapping your node, make sure to stop your node as shown below.*

Using the first bootstrap method below ensures that the snapshot is checked for consistency both programmatically and by your checking the blockchain explorer(s) to confirm the block hash. This is the most reliable and robust method but it is also the slowest.

```bash
tezbake stop
tezbake bootstrap-node <url> <block_hash>
tezbake start
# example:
tezbake bootstrap-node https://snapshots.eu.tzinit.org/mainnet/rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
```

> **💡 TIP:** You can replace `eu` above with `us` or `asia` if you prefer to use a different mirror closer to you.

Get the block hash and block level from the snapshot provider's website:
<https://snapshots.eu.tzinit.org/mainnet/rolling.html>

> **ℹ️ INFO:** The `<block_hash>` argument is optional but encouraged for security verification. If you don't want to bother with this protection, use the second method below which will also be faster.

Verify the hash/checksum provided by the snapshot provider to ensure the snapshot is valid. You can find the correct hashes for all blocks on Tezos blockchain explorers such as:
<https://tzkt.io/blocks>

Simply search for the block level in the search field and verify the hash of the block matches the hash provided by the snapshot provider.

Using the second bootstrap method below is faster but it assumes you trust the source of the snapshot. Sometimes one doesn't have a choice and must make such trade-offs when time is of the essence.

```bash
tezbake stop
tezbake bootstrap-node <url> --no-check
tezbake start
# example:
tezbake bootstrap-node https://snapshots.eu.tzinit.org/mainnet/rolling --no-check
```

> **💡 TIP:** You can replace `eu` above with `us` or `asia` if you prefer to use a different mirror closer to you.

---

## Nonce Forfeiture Explained

When a baker produces certain blocks, it commits a **nonce** — a random number that must be revealed in the following cycle. This is part of Tezos' on-chain randomness mechanism.

Bootstrapping your node replaces the blockchain data with a snapshot. If you use a **recent snapshot** (less than 5-6 days old) and **wipe your `.tezos-client` directory**, two things go wrong:

1. **The node loses the full block history** for the current cycle — it no longer has the data needed to reveal the nonce.
2. **The nonce file is deleted** from `.tezos-client`, so the baker can no longer prove what it committed.

When the reveal deadline arrives, your baker cannot respond. The protocol treats this as a forfeiture and you lose your attestation rewards for that cycle.

**Why a 5-6 day old snapshot helps:**

Tezos cycles last roughly 2-3 days. By using a snapshot that is at least 5-6 days old, you ensure that the imported blockchain already contains the full block history for at least one complete past cycle — so any outstanding nonce reveals are already covered in the snapshot.

**Why keeping `.tezos-client` matters:**

The nonce file lives inside `.tezos-client`. As long as you don't delete this directory, your baker retains its nonce commitments across the bootstrap and can reveal them when required.

> **💡 TIP:** The downside of using an older snapshot is a slower bootstrap — it takes extra time to sync the additional blocks (up to 2 hours on slow home networks). This trade-off is worth it to protect your income.

---

## What's Next

→ Return to [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) to continue setup

---

## Related Guides

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Complete setup guide
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Check sync progress
* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Fix common issues

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
