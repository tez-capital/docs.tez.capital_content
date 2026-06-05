---
title: "Back Up and Restore TezSign Data"
weight: 1
type: docs
summary: Copy the TezSign data folder from the SD card for backups, recovery, and hardware migrations
---

## When to Use This

Use this procedure when you want to:

- Back up the keys and signer state from a TezSign SD card
- Move a working TezSign setup to a newly flashed SD card
- Migrate to a newer TezSign image or hardware architecture
- Recover from a failing SD card by transplanting the TezSign data to a fresh card

This is an offline SD-card procedure. You remove the microSD card from the TezSign device, mount it on a Linux computer, copy the `tezsign` folder from the `data` partition, then restore that folder to the `data` partition on another flashed card.

> **Important:** macOS does not mount the TezSign `data` partition correctly. Use a Linux computer, a Linux live USB, or a trusted Linux VM with direct access to the card reader.

## What You Are Copying

A current TezSign image has three partitions. On Linux, you should see partitions labeled `app`, `boot`, and `data`. The one you need is `data`.

Inside the `data` partition is a folder named `tezsign`:

```text
data/
  tezsign/
```

Copy the entire `tezsign` folder as one unit. Do not copy only individual files inside it.

## Before You Begin

- Stop baking with this TezSign device before removing its SD card.
- Disconnect the TezSign device from the baker machine.
- Use a Linux machine you trust.
- Have a backup destination ready, such as another drive or an encrypted folder.
- Keep your TezSign master password safe. The backup is not useful without it.

> **Critical:** Never run two TezSign devices, cards, or restored copies with the same keys at the same time. That can cause double signing and slashing.

## Find the `data` Partition

Insert the TezSign microSD card into the Linux computer.

In a graphical file manager, the card may appear as several volumes. Look for the volume named `data`. Ignore the `app` and `boot` partitions for this procedure.

From the command line, you can identify the partition labels with:

```bash
lsblk -o NAME,LABEL,FSTYPE,SIZE,MOUNTPOINTS
```

Find the row with `LABEL` set to `data`. If your desktop auto-mounted it, the mount point may look like `/media/$USER/data` or `/run/media/$USER/data`.

## Back Up the `tezsign` Folder

### Option A: File Manager

1. Open the mounted `data` partition.
2. Find the `tezsign` folder at the top level of the partition.
3. Copy the whole `tezsign` folder to your backup location.
4. Safely eject or unmount the SD card before removing it.

### Option B: Command Line

Create a backup directory and copy the folder:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo rsync -aHAX --info=progress2 /media/$USER/data/tezsign/ "$HOME/tezsign-backups/tezsign/"
sync
```

If your `data` partition mounted somewhere else, replace `/media/$USER/data` with the mount point shown by `lsblk`.

For a portable archive, especially if you are storing the backup on a non-Linux filesystem, use:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo tar -C /media/$USER/data -czf "$HOME/tezsign-backups/tezsign-$(date +%Y%m%d).tar.gz" tezsign
sync
```

## Restore to a Newly Flashed Card

Flash the new TezSign image to a fresh SD card first. Then insert that card into the Linux computer and open or mount its `data` partition.

If you backed up the folder directly:

```bash
sudo rsync -aHAX --delete "$HOME/tezsign-backups/tezsign/" /media/$USER/data/tezsign/
sync
```

If you backed up a `.tar.gz` archive:

```bash
sudo rm -rf /media/$USER/data/tezsign
sudo tar -C /media/$USER/data -xzf "$HOME/tezsign-backups/tezsign-YYYYMMDD.tar.gz"
sync
```

Replace `tezsign-YYYYMMDD.tar.gz` with your actual backup file name.

When the restore is complete, safely eject or unmount the SD card before removing it.

## Verify After Restore

1. Insert the restored SD card into the TezSign device.
2. Make sure the original TezSign device or original SD card is not also connected.
3. Connect the restored device to the baker machine.
4. Check that TezBake sees the device:

```bash
tezbake tezsign status
```

Unlock the restored keys:

```bash
tezbake tezsign unlock
```

Then verify the baker and DAL status:

```bash
tezbake info
tezbake info --dal
```

## Safety Notes

- Treat the backup as sensitive signing material, even though the TezSign keys are protected by your master password.
- Store at least one backup offline.
- Label backups clearly so you know which baker and network they belong to.
- Test a restored card only when the primary TezSign device is unplugged.
- For routine disaster recovery, an SD-card clone is still useful. This `data/tezsign` transplant is the focused backup you need for image migrations and fresh-card restores.

## Related Guides

- [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - Full TezSign setup
- [Updating TezSign](/tezsign/tutorials/updating/) - Firmware and application updates
- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Standard baker setup
- [Slashing Explained](/getting-started/slashing-explained/) - Why duplicate signer operation is dangerous

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
