---
title: "High-Watermark Recovery"
weight: 6
type: docs
summary: Set TezSign high-watermark levels during failover, restore, and migration
---

## When to Use This

Use this during TezSign failover, SD-card restore, or migration when a backup TezSign card may resume signing for an active baker.

The high watermark (HWM) prevents a signer from signing at or below a level it has already signed. Setting it correctly reduces the risk of accidental double signing during recovery.

## Set Levels for Each Signing Alias

Use the current chain level, or current level plus a small safety margin such as `+10` during failover:

```bash
tezbake tezsign advanced set-level consensus <level>
tezbake tezsign advanced set-level companion <level>
```

Run the command for each TezSign device key alias that signs for the baker. The common aliases are `consensus` and `companion`. If you used different TezSign device aliases, replace them.

These are TezSign device key aliases, not necessarily the local Octez aliases loaded by the baker. For example, your TezSign device key may still be named `consensus` even if TezBake loads it locally as `baker`.

## Failover Example

If the current level is `8000000` and you want a small safety margin:

```bash
tezbake tezsign advanced set-level consensus 8000010
tezbake tezsign advanced set-level companion 8000010
```

Do not lower a high-watermark level casually. Only change it when you are intentionally preparing a restored, migrated, or backup TezSign card to resume baking safely.

## Related Guides

- [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - Restore the TezSign data folder
- [Failover Procedure](/tezbake/tutorials/failover-procedure/) - Safe baker failover
- [Slashing Explained](/getting-started/slashing-explained/) - Why duplicate signing is dangerous

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
