---
title: "Baking with TezSign"
weight: 3
type: docs
summary: TezSign setup is now part of the main TezBake setup flow.
---

## TezSign Setup Moved Into Baking on Mainnet

TezSign is the recommended signing setup for TezBake. The actual setup flow now lives inside the main baker setup guide:

**[Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/#recommended-set-up-tezsign-consensus-and-companion-keys)**

Use that guide when you are setting up a new baker or moving an existing baker to TezSign.

## TezSign KBs

These pages cover the deeper TezSign tasks that are important for reliability, recovery, and long-term operations. They are not all required to get your first baker running, but you should work through them after setup.

- [Hardware and Flashing](/tezsign/tutorials/hardware-and-flashing/) - Hardware choices, SD cards, and image flashing
- [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - SD-card data backup, restore, and migration
- [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/) - Set TezSign HWM levels during failover or migration
- [USB and Power Reliability](/tezsign/tutorials/usb-power-reliability/) - USB, OTG, power, and host-controller fixes
- [Alias Cleanup After Activation](/tezsign/tutorials/alias-cleanup-after-activation/) - Clean up temporary aliases after tz4 keys activate
- [Direct TezSign Backend](/tezsign/tutorials/direct-backend/) - Optional direct backend after TezSign is stable
- [Automatic Unlock](/tezsign/tutorials/automatic-unlock/) - Optional automatic unlock after you understand the tradeoff
- [Updating TezSign](/tezsign/tutorials/updating/) - Firmware and app updates

## Related TezBake Guides

- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Main setup guide
- [Baking on Testnets](/tezbake/tutorials/baking-on-testnets/) - Testnet setup
- [Failover Procedure](/tezbake/tutorials/failover-procedure/) - Safe failover
- [Slashing Explained](/getting-started/slashing-explained/) - Why duplicate signing is dangerous

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
