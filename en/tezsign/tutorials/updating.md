---
title: "Updating"
weight: 2
type: docs
summary: How to update TezSign firmware and application
---

## Keeping TezSign Up-to-Date

> **⚠️ WARNING: Experimental Updater**
>
> The `tezsign_updater` is currently experimental. Before updating or reflashing, **back up the `data/tezsign` folder** with [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/).
>
> macOS does not mount the TezSign `data` partition correctly. Use Linux to make this backup.

If you are recovering from a failure after a signer upgrade, first check [Host and Gadget Compatibility](/tezsign/tutorials/troubleshooting/#host-and-gadget-compatibility). The host executable and SD-card image are separate components; `tezbake tezsign version` reports the gadget image. Update an incompatible older gadget before installing a newer host.

### Check Image Compatibility

**Check the SD card's partition layout before choosing an update method. The image date alone does not establish updater compatibility.**

After stopping baking, powering off TezSign, and inserting its SD card into a Linux computer, inspect the card with:

```bash
lsblk -o NAME,TYPE,SIZE,MODEL,LABEL,FSTYPE,MOUNTPOINTS
```

Identify the whole SD card by its size and partitions. A whole disk is named something like `/dev/sdc` or `/dev/mmcblk0`; `/dev/sdc1` and `/dev/mmcblk0p1` are individual partitions.

| Card layout | Update method |
|-------------|---------------|
| Three partitions: `boot`, `app`, `data` | Candidate for the current updater. It must also pass the file and image-compatibility checks described below. |
| Four partitions, such as `RPICFG`, `armbi_root`, `app`, `data` | Legacy layout rejected by the current updater's automatic detection. Use [Migrate a Legacy Card](#migrate-a-legacy-card). |
| Missing labels, unexpected partitions, or unreadable filesystems | Confirm that you selected the correct card and diagnose the problem before writing to it. See [Troubleshooting](#troubleshooting). |

The current updater's detection requires exactly three non-empty partitions, an `app` filesystem containing `/tezsign`, and a `data` filesystem. Automatic image download also needs a supported `/.image-flavour` value in `app`. Before copying, the updater checks that the source image and destination have matching boot/app partition sizes and rootfs layout. **Three partitions alone do not guarantee a successful update.**

For example, this is a legacy card, even if its reported version is January 18, 2026:

```text
sdc      7.4G
├─sdc1   128M RPICFG     vfat
├─sdc2   1.1G armbi_root ext4
├─sdc3    64M app        ext4
└─sdc4   128M data       ext4
```

> **ℹ️ INFO: Layout History**
>
> The three-partition image design was merged on April 19, 2026 in [TezSign PR #29](https://github.com/tez-capital/tezsign/pull/29). The first published release containing that design is [release-202604282229 (April 28, 2026)](https://github.com/tez-capital/tezsign/releases/tag/release-202604282229). Use the actual card layout and updater checks to choose the update method.

### Migrate a Legacy Card

The current updater does not convert a legacy four-partition card to the new three-partition image layout.

1. Stop baking with this signer, disconnect it, and power it off before removing the SD card.
2. On Linux, [back up the entire `tezsign` folder from the `data` partition](/tezsign/tutorials/back-up-and-restore-data/). Verify the backup before flashing anything. On the legacy card shown above, `data` is `/dev/sdc4`, not `/dev/sdc3`.
3. [Flash the latest full image for your board](/tezsign/tutorials/hardware-and-flashing/#flash-the-tezsign-image), preferably to a fresh SD card so the original remains intact until the replacement is verified.
4. [Restore the backed-up folder](/tezsign/tutorials/back-up-and-restore-data/#restore-to-a-newly-flashed-card) to `data/tezsign` on the new card. Copy the whole folder to preserve the keys and signer state.
5. Follow [Verify After Restore](/tezsign/tutorials/back-up-and-restore-data/#verify-after-restore). A missing `level2.bin` alone does not invalidate the restored keys or require manual HWM setting; see [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/) for the optional procedure. Keep the original card offline; never run two copies with the same keys at the same time.

A full-card clone preserves the old image and partition layout, so it cannot perform this migration. Do not delete partitions or bypass the updater's layout checks to try to convert the card in place.

> **⚠️ WARNING: Signer Compatibility**
>
> `xtz.signer` **25.2.1 and newer requires TezSign `release-202610041210` or newer**. Update TezSign before upgrading the signer to 25.2.1 or later, or the signer may have trouble detecting the device.

### Update Procedure

Use this procedure only after confirming that the card has a compatible layout and backing up `data/tezsign`.

1. **Stop the device:** Stop baking with this signer, disconnect the TezSign device from the baker machine, and power it off, including any external power supply.
2. **Remove SD Card:** Eject the SD card from the device.
3. **Connect to Manager:** Insert the SD card into the reader on your Linux manager machine.
4. **Download Updater:**
    Download the current `tezsign_updater` for your Linux manager's CPU architecture from the [GitHub Releases page](https://github.com/tez-capital/tezsign/releases).

    *Example command format to download and make executable. Replace `<release-tag>` and the asset name with the current release from GitHub:*

```bash
wget https://github.com/tez-capital/tezsign/releases/download/<release-tag>/tezsign_updater_linux_amd64 -O tezsign_updater && chmod +x tezsign_updater
```

5. **Run the Updater:**
    Execute the updater tool. It will automatically scan for viable SD cards, check partition layouts, and verify the existence of TezSign files.

```bash
sudo ./tezsign_updater
```

> **ℹ️ INFO:** You can run `./tezsign_updater -h` to see all available options.

6. **Select SD Card:**
    Check the disk path, size, and model against `lsblk`, then choose the intended SD card. The tool will indicate **OK** for devices that pass its detection checks. Selecting the card starts the automatic image download and update flow.

7. **Wait for the Full Update:**
    The current updater performs a **full** update automatically; there is no **App** update choice. On a compatible three-partition card, it updates `boot` and `app` while preserving `data`. Wait for the success message before removing the card.

8. **Finish:**
    Safely eject the SD card from your manager machine, insert it back into your TezSign device, and reconnect it to the baker machine. Check `tezbake tezsign status`, unlock the keys as usual, and verify the signer and baker status before considering the update complete.

### Troubleshooting

**`device does not match TezSign layout`** means the selected disk failed the updater's detection checks. It does not by itself mean the card is damaged.

- If `lsblk` shows four partitions like the example above, use [Migrate a Legacy Card](#migrate-a-legacy-card).
- If it shows three partitions, confirm the disk identity, the `app` and `data` labels, and that the `app` filesystem is readable and contains `/tezsign`. Partition count alone is insufficient.
- If automatic image detection fails because `/.image-flavour` is missing or unsupported, confirm the board model and use the backup, fresh-image, and restore procedure above. Do not guess or edit the flavour marker to force an update.
- If the updater reports a boot/app partition size mismatch or a rootfs presence mismatch, the source image and card layout are incompatible. Use the migration procedure with the correct image for your board.

If the layout is unclear, contact the team with the updater error and `lsblk` output before flashing. Do not share the contents of `data/tezsign`.

---

## Related Guides

* [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - Linux-only data partition backup and restore
* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Main TezSign setup
* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Fix common issues

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
