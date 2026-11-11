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

> **⚠️ WARNING:** macOS does not mount the TezSign `data` partition correctly. Use a Linux computer, a Linux live USB, or a trusted Linux VM with direct access to the card reader.

## Choose a Backup Method

There are two useful TezSign backup methods:

| Method | Best for | What it copies |
|--------|----------|----------------|
| **Clone the whole SD card** | Fast same-image spare cards | The entire card: `app`, `boot`, `data`, partition layout, TezSign image version, and keys |
| **Copy `data/tezsign` only** | Moving to a newly flashed card or newer TezSign generation | Only the TezSign keys and signer state |

Cloning is the quickest way to create ready spare cards on the same TezSign image generation. It is not an upgrade path.

> **⚠️ WARNING: New-Generation Images**
>
> A full-card clone preserves the old image. It will not move you to the new TezSign image generation, and it will not work around an older image that cannot use the built-in update process. For that, flash the latest full image first, then restore the `data/tezsign` folder onto the new card.

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

> **🚨 CRITICAL:** Never boot the cloned card at the same time as the original for the same baker. A clone contains the same signing keys and signer state.

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

A current TezSign image has three partitions: `boot`, `app`, and `data`. Legacy cards may have four, such as `RPICFG`, `armbi_root`, `app`, and `data`. In both cases, copy from the partition labeled `data`; its partition number can differ. See [Check Image Compatibility](/tezsign/tutorials/updating/#check-image-compatibility) before choosing an update method.

Inside the `data` partition is a folder named `tezsign`:

```text
data/
  tezsign/
```

Copy the entire `tezsign` folder as one unit. Do not copy only individual files inside it.

### Key Files and Signing State

A typical backup contains:

```text
tezsign/
  keystore/
    master.json
    seed.bin
    keys/
      consensus/
        meta.json
        encrypted.bin
        level2.bin       # signing state, when present
      companion/
        meta.json
        encrypted.bin
        level2.bin       # signing state, when present
  gadget.log             # may be present; not a key backup
```

Your device may use different key aliases. `master.json` and `seed.bin` alone are not a complete backup of the enrolled keys: preserve each alias's `meta.json` and `encrypted.bin` too. If those key files are missing and the original card is intact, copy the entire `tezsign` folder from it instead of assembling individual files or generating replacement keys.

**A missing `level2.bin` is a different case.** It holds per-key high-watermark (HWM) state, not the signing key. A backup taken before the keys were first unlocked or used may legitimately lack it. TezSign recreates missing state on unlock and advances it as the key signs. Its absence alone does not make the key backup unusable or require manual HWM recovery.

Copy `level2.bin` when it exists as part of the whole folder. Recreating it does not recover the previous signing history, and a copied file is only current as of the backup. For a normal migration, keep the original signer offline and use only one active baker with these keys. If you want to set an explicit starting level during recovery, follow [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/). This is an optional extra precaution, not a prerequisite for every restore.

## Before You Begin

If this is your active mainnet signer, [check for a maintenance gap on Tezos.Systems](/getting-started/baker-signal/#plan-maintenance) before removing its card. Include time to copy the data and bring signing back online.

- Stop baking with this TezSign device before removing its SD card.
- Disconnect the TezSign device from the baker machine.
- Take the microSD card out of the Raspberry Pi or Radxa board.
- Insert it into a computer's SD-card slot, or use a USB SD-card reader.
- Use a Linux machine you trust. If you do not have another Linux computer, the baker host itself is often the easiest option, especially if it has a desktop GUI.
- Have a backup destination ready, such as another drive or an encrypted folder.
- Keep your TezSign master/key decryption password safe. The backup is not useful without it.

> **🚨 CRITICAL:** Never run two TezSign devices, cards, or restored copies with the same keys at the same time. That can cause double signing and slashing.

> **ℹ️ INFO: Baker Host Staging:** If you use the baker machine itself as a temporary staging computer, delete the temporary backup folder or archive from the baker after you have restored it to your backup flashed card(s). Do not leave loose signer backups sitting on the baker host.

## Find the `data` Partition

Insert the TezSign microSD card into the Linux computer.

In a graphical file manager, the card may appear as several volumes. Look for the volume named `data` on the intended SD card. Ignore the other partitions for this procedure.

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

If the file manager reports permission errors or skips files, use the command-line method below. Do not treat a partially copied folder as a complete backup.

If you are using the baker host as the backup computer, treat any copied folder on that host as temporary. Once you have restored it to backup TezSign-flashed SD card(s), delete the temporary copy from the baker.

## Back Up the `tezsign` Folder without a GUI

Use this method on a headless Linux host over SSH or on a server without a graphical file manager.

Insert the TezSign SD card and identify the `data` partition:

```bash
lsblk -o NAME,LABEL,FSTYPE,SIZE,MOUNTPOINTS
```

Create a mount point and mount the `data` partition read-only for backup. Replace `/dev/sdX3` with the actual partition device labeled `data` in `lsblk`; on a legacy four-partition card it may be `/dev/sdX4`. If the desktop already mounted it, skip the mount commands and replace `/mnt/tezsign-data` in the commands below with its existing mount point.

```bash
sudo mkdir -p /mnt/tezsign-data
sudo mount -o ro /dev/sdX3 /mnt/tezsign-data
```

Create a temporary backup directory and copy the folder:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo rsync -aHAX --info=progress2 /mnt/tezsign-data/tezsign/ "$HOME/tezsign-backups/tezsign/"
sync
```

Verify the copied backup with a checksum comparison before flashing or reusing the source card:

```bash
sudo rsync -aHAXnci --delete /mnt/tezsign-data/tezsign/ "$HOME/tezsign-backups/tezsign/"
```

This is a dry run (`-n`); it does not change either copy. A successful comparison produces no output and exits without errors. Investigate any differences before proceeding.

To check which files were copied without displaying their contents:

```bash
sudo find "$HOME/tezsign-backups/tezsign/keystore" -maxdepth 3 -type f -printf '%P\n'
```

Check that all expected key aliases have `meta.json` and `encrypted.bin`. Do not reject an otherwise complete backup solely because `level2.bin` is absent; see [Key Files and Signing State](#key-files-and-signing-state).

For a portable archive, especially if you are storing the backup on a non-Linux filesystem, use this instead of the `rsync` copy:

```bash
mkdir -p "$HOME/tezsign-backups"
sudo tar -C /mnt/tezsign-data -czf "$HOME/tezsign-backups/tezsign-$(date +%Y%m%d).tar.gz" tezsign
sync
```

Compare the archive against the source folder before unmounting it. Use the actual archive filename you created:

```bash
sudo tar -C /mnt/tezsign-data --compare --gzip --file "$HOME/tezsign-backups/tezsign-YYYYMMDD.tar.gz"
```

Proceed only if the comparison exits successfully without reporting differences.

Unmount the card before removing it:

```bash
sudo umount /mnt/tezsign-data
```

If you are using the baker host as the temporary backup location, delete this folder or archive from the baker after restoring it to your backup card(s).

## Restore to a Newly Flashed Card

Flash the new TezSign image to a fresh SD card first. Then insert that card into the Linux computer and open or mount its `data` partition.

An unused `data` partition may contain only `lost+found`; that is expected. Restore the folder at its root to produce `data/tezsign/keystore`, not `data/tezsign/tezsign/keystore`. You do not need to initialize a new master store or create new keys to restore existing ones.

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

Verify the restored copy while the card is still mounted:

```bash
sudo rsync -aHAXnci --delete "$HOME/tezsign-backups/tezsign/" /mnt/tezsign-data/tezsign/
```

The comparison must exit successfully without differences. These filesystem checks can be completed without booting the spare or connecting it to a baker.

If you backed up a `.tar.gz` archive:

```bash
sudo rm -rf /mnt/tezsign-data/tezsign
sudo tar -C /mnt/tezsign-data -xzf "$HOME/tezsign-backups/tezsign-YYYYMMDD.tar.gz"
sync
```

Replace `tezsign-YYYYMMDD.tar.gz` with your actual backup file name.

For an archive restore, compare it with the mounted destination before proceeding:

```bash
sudo tar -C /mnt/tezsign-data --compare --gzip --file "$HOME/tezsign-backups/tezsign-YYYYMMDD.tar.gz"
```

Proceed only if the comparison exits successfully without differences.

When the restore is complete, safely eject or unmount the SD card before removing it:

```bash
sudo umount /mnt/tezsign-data
```

Repeat this restore for each backup card you want ready.

## Verify After Restore

1. Stop the baker and signer services on the test/restore host with `tezbake stop` and confirm they are stopped with `tezbake info`. Keep other bakers using these keys stopped too.
2. Disconnect the original signer and keep its card offline. Do not have an automatic-unlock service running during this controlled check.
3. Insert the restored card into the TezSign device and connect it to the test/restore host.
4. Check the image and key identities:

```bash
tezbake tezsign version
tezbake tezsign status
```

Confirm the expected aliases and public addresses, then unlock the restored keys with the existing password:

```bash
tezbake tezsign unlock
tezbake tezsign status --full
```

Both signing keys should show `UNLOCKED`. HWM counters are meaningful after unlock; zeros in a locked-key status report do not establish that the stored state was lost. A restored backup without HWM state can show zeros after unlock until it signs or you choose to set a level.

If you want to set a level manually, do it now using [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/), while baking remains stopped.

For a spare-card test only, disconnect and store the spare offline after verification. For an actual switch, start the baker with only the restored signer connected:

```bash
tezbake start
tezbake info --signer
tezbake info
tezbake info --dal
```

Check for successful signing in the signer/baker logs and resumed attestations. Detection and unlock alone do not prove that baking has resumed. Once either card signs again, the other card's saved HWM may be older; never run both copies together.

## Optional: Set High-Watermark Levels

Manual HWM setting is optional for an ordinary restore. Use the ordered [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/) procedure when you choose this extra precaution. It covers stopping signing, unlocking, obtaining a fresh chain level, selecting a level above the existing counters, and verifying the result before restart.

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
