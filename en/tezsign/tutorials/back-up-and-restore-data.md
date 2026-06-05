---
title: "Back Up and Restore TezSign Data"
weight: 1
type: docs
summary: Clone TezSign SD cards or copy the data folder for backups, recovery, and hardware migrations
---

## When to Use This

Use this guide when you want to:

- Back up the keys and signer state from a TezSign SD card
- Clone a working TezSign SD card to make a ready spare
- Move a working TezSign setup to a newly flashed SD card
- Migrate to a newer TezSign image or hardware architecture
- Recover from a failing SD card by transplanting the TezSign data to a fresh card

These are offline SD-card procedures. You remove the microSD card from the TezSign device, use a computer to clone the whole card or copy the `tezsign` folder from the `data` partition, then restore the backup to another card.

> **Important:** macOS does not mount the TezSign `data` partition correctly. Use a Linux computer, a Linux live USB, or a trusted Linux VM with direct access to the card reader.

## Choose a Backup Method

There are two useful TezSign backup methods:

| Method | Best for | What it copies |
|--------|----------|----------------|
| **Clone the whole SD card** | Fast same-image spare cards | The entire card: `app`, `boot`, `data`, partition layout, TezSign image version, and keys |
| **Copy `data/tezsign` only** | Moving to a newly flashed card or newer TezSign generation | Only the TezSign keys and signer state |

Cloning is the quickest way to create ready spare cards on the same TezSign image generation. It is not an upgrade path.

> **New-generation image warning:** A full-card clone preserves the old image. It will not move you to the new TezSign image generation, and it will not work around an older image that cannot use the built-in update process. For that, flash the latest full image first, then restore the `data/tezsign` folder onto the new card.

## Clone the Whole SD Card

Use this when you want a bit-for-bit spare card for the same TezSign image version.

The easiest setup is a computer with two SD-card slots, or two USB SD-card readers:

1. Insert the working TezSign SD card as the source.
2. Insert a blank target SD card of the same size or larger.
3. Use a cloning or imaging tool to copy the source card to the target card.
4. Safely eject both cards when the clone is complete.
5. Label the clone clearly and store it offline.

Tools people commonly use:

- [Balena Etcher](https://etcher.balena.io/) - use **Clone drive** if your version offers it, or use **Flash from file** after creating an image.
- [GNOME Disks](https://wiki.gnome.org/Apps/Disks) - a simple Linux GUI app that can create and restore disk images.

Never boot the cloned card at the same time as the original for the same baker. A clone contains the same signing keys and signer state.

## Clone by Making an Image First

Use this two-step workflow if you have only one SD-card slot or one USB SD-card reader.

1. Insert the working TezSign SD card.
2. Use GNOME Disks or another disk imaging tool to create a full disk image from the SD card. Save the image somewhere secure with enough free space for the full card size.
3. Safely eject the source card.
4. Insert the new target SD card.
5. Restore or flash the saved image to the target card. GNOME Disks can restore disk images; Balena Etcher can flash from an image file.
6. Safely eject the target card, label it, and store it offline.

Keep full-card images secure. They contain the TezSign `data` partition and therefore the encrypted signer keys.

## Copy the `data/tezsign` Folder

Use this when you are moving keys and signer state to a freshly flashed TezSign card, especially when migrating to a newer TezSign image generation or hardware architecture.

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
- Take the microSD card out of the Raspberry Pi or Radxa board.
- Insert it into a computer's SD-card slot, or use a USB SD-card reader.
- Use a Linux machine you trust. If you do not have another Linux computer, the baker host itself is often the easiest option, especially if it has a desktop GUI.
- Have a backup destination ready, such as another drive or an encrypted folder.
- Keep your TezSign master/key decryption password safe. The backup is not useful without it.

> **Critical:** Never run two TezSign devices, cards, or restored copies with the same keys at the same time. That can cause double signing and slashing.

> **Baker host note:** If you use the baker machine itself as a temporary staging computer, delete the temporary backup folder or archive from the baker after you have restored it to your backup flashed card(s). Do not leave loose signer backups sitting on the baker host.

## Find the `data` Partition

Insert the TezSign microSD card into the Linux computer.

In a graphical file manager, the card may appear as several volumes. Look for the volume named `data`. Ignore the `app` and `boot` partitions for this procedure.

From the command line, you can identify the partition labels with:

```bash
lsblk -o NAME,LABEL,FSTYPE,SIZE,MOUNTPOINTS
```

Find the row with `LABEL` set to `data`. If your desktop auto-mounted it, the mount point may look like `/media/$USER/data` or `/run/media/$USER/data`.

## Back Up the `tezsign` Folder with a GUI

This is the easiest method if the Linux machine has a desktop environment.

1. Open the mounted `data` partition.
2. Find the `tezsign` folder at the top level of the partition.
3. Copy the whole `tezsign` folder to your backup location, or directly onto the `data` partition of another freshly flashed TezSign SD card.
4. Safely eject or unmount the SD card before removing it.

If you are using the baker host as the backup computer, treat any copied folder on that host as temporary. Once you have restored it to backup TezSign-flashed SD card(s), delete the temporary copy from the baker.

## Back Up the `tezsign` Folder without a GUI

Use this method on a headless Linux host over SSH or on a server without a graphical file manager.

Insert the TezSign SD card and identify the `data` partition:

```bash
lsblk -o NAME,LABEL,FSTYPE,SIZE,MOUNTPOINTS
```

Create a mount point and mount the `data` partition. Replace `/dev/sdX3` with the actual partition device shown by `lsblk`:

```bash
sudo mkdir -p /mnt/tezsign-data
sudo mount /dev/sdX3 /mnt/tezsign-data
```

Create a temporary backup directory and copy the folder:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo rsync -aHAX --info=progress2 /mnt/tezsign-data/tezsign/ "$HOME/tezsign-backups/tezsign/"
sync
```

For a portable archive, especially if you are storing the backup on a non-Linux filesystem, use this instead of the `rsync` copy:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo tar -C /mnt/tezsign-data -czf "$HOME/tezsign-backups/tezsign-$(date +%Y%m%d).tar.gz" tezsign
sync
```

Unmount the card before removing it:

```bash
sudo umount /mnt/tezsign-data
```

If you are using the baker host as the temporary backup location, delete this folder or archive from the baker after restoring it to your backup card(s).

## Restore to a Newly Flashed Card

Flash the new TezSign image to a fresh SD card first. Then insert that card into the Linux computer and open or mount its `data` partition.

If you are using a GUI, open the new card's `data` partition and copy the backed-up `tezsign` folder into it.

If you are using the command line, mount the new card's `data` partition first. Replace `/dev/sdX3` with the actual new-card `data` partition shown by `lsblk`:

```bash
sudo mkdir -p /mnt/tezsign-data
sudo mount /dev/sdX3 /mnt/tezsign-data
```

If you backed up the folder directly, restore it:

```bash
sudo rsync -aHAX --delete "$HOME/tezsign-backups/tezsign/" /mnt/tezsign-data/tezsign/
sync
```

If you backed up a `.tar.gz` archive:

```bash
sudo rm -rf /mnt/tezsign-data/tezsign
sudo tar -C /mnt/tezsign-data -xzf "$HOME/tezsign-backups/tezsign-YYYYMMDD.tar.gz"
sync
```

Replace `tezsign-YYYYMMDD.tar.gz` with your actual backup file name.

When the restore is complete, safely eject or unmount the SD card before removing it:

```bash
sudo umount /mnt/tezsign-data
```

Repeat this restore for each backup card you want ready.

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

## Optional: Set High-Watermark Levels

For extra safety during failover, recovery, or migration, you can set the TezSign high-watermark level before the restored card signs again.

Use the current chain level, or the current level plus a small safety margin such as `+10` during failover:

```bash
tezbake tezsign advanced set-level consensus <level>
tezbake tezsign advanced set-level companion <level>
```

Run the command for each TezSign key alias that signs for the baker. The common aliases are `consensus` and `companion`; if you used different TezSign device aliases, replace them. These are the TezSign device key aliases, not necessarily the local Octez aliases loaded by the baker.

Do not lower a high-watermark level casually. Only change it when you are intentionally preparing a restored, migrated, or backup TezSign card to resume baking safely.

## Safety Notes

- Treat the backup as sensitive signing material, even though the TezSign keys are protected by your master password.
- Keep at least two backup TezSign-flashed SD cards ready, plus additional backups if your operation needs them.
- Use full-card clones for same-image ready spares.
- Use the `data/tezsign` transplant method when moving to a new TezSign image generation or architecture.
- Store backup cards offline and clearly labeled.
- Label backups clearly so you know which baker and network they belong to.
- Save the TezSign master/key decryption password somewhere safe and separate from the SD cards.
- Test a restored card only when the primary TezSign device is unplugged.
- For routine disaster recovery, ready-to-use TezSign-flashed backup cards are the goal. This `data/tezsign` transplant is the focused backup you need for image migrations and fresh-card restores.
- If you used the baker host as temporary staging, remove the transient backup copy from the baker after the restore is complete.

## Related Guides

- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Main TezSign setup
- [Updating TezSign](/tezsign/tutorials/updating/) - Firmware and application updates
- [Slashing Explained](/getting-started/slashing-explained/) - Why duplicate signer operation is dangerous

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
