---
title: "TezSign Tutorials"
weight: 1
type: docs
summary: Guides for setting up and using TezSign hardware signer
---
**TezSign**
---

*A purpose-built hardware signer for Tezos baking*

TezSign is a hardware signer for Tezos baking, based on affordable single-board computers like Raspberry Pi Zero 2W. It connects via USB to your baking node and is designed to sign baking operations securely.

For an overview of TezSign vs Ledger, see [Ledger vs TezSign](/getting-started/ledger-vs-tezsign/).

---

## Backups and Migrations

Before updating, reflashing, or migrating a TezSign SD card, back up the `tezsign` folder from the Linux-only `data` partition.

- [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/)
- [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/)

This is the focused backup path for moving TezSign keys and signer state to a newly flashed card.

---

## Setup Options

- **Mainnet baker:** Follow [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/)
- **TezSign setup hub:** See [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/)
- **Separate machine (remote signer):** Follow [Baking with Prism](/tezbake/tutorials/baking-with-prism/)

---

## Knowledge Base

- [Hardware and Flashing](/tezsign/tutorials/hardware-and-flashing/) - Hardware, SD cards, and image flashing
- [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - Backups, restores, and SD-card migration
- [USB and Power Reliability](/tezsign/tutorials/usb-power-reliability/) - USB, OTG, power, and BIOS troubleshooting
- [Alias Cleanup After Activation](/tezsign/tutorials/alias-cleanup-after-activation/) - Local alias cleanup after tz4 activation
- [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/) - HWM handling for failover and migration
- [Direct TezSign Backend](/tezsign/tutorials/direct-backend/) - Optional direct TezSign backend
- [Automatic Unlock](/tezsign/tutorials/automatic-unlock/) - Optional automatic unlock
- [Updating](/tezsign/tutorials/updating/) - TezSign image and app updates
