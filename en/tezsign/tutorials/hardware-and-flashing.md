---
title: "Hardware and Flashing"
weight: 3
type: docs
summary: TezSign hardware choices, SD card recommendations, and image flashing on macOS, Windows, Ubuntu, and Debian
---

## Recommended Hardware

The Radxa Zero 3W and Raspberry Pi Zero 2W are both good TezSign devices. Buy two devices if you want a ready spare for failover.

Use high-endurance microSD cards. An 8 GB card is enough. Keep at least one active card and at least two TezSign-flashed backup cards.

## General Supplies

1. Dual microSD card reader with two slots, or at least one USB SD-card reader
2. High-endurance microSD cards, 8 GB or larger
3. Short, high-quality USB data cable

Example dual-slot reader:

- <https://a.co/d/iOrBvk6>

## Radxa Zero 3W

1. Radxa ZERO 3W RK3566 single-board computer
2. Radxa heatsink for Zero 3W / 3E
3. USB A to USB C data cable
4. Optional micro HDMI cable if you want to inspect the device screen
5. Optional independent USB-C power supply if OTG power is unreliable

Use the farthest USB port for the OTG data connection.

![Radxa Zero 3W](/tezbake/tutorial/tezbaketezsignRadxaOTG.png)

Example parts:

- Radxa ZERO 3W: <https://www.aliexpress.us/item/3256807428419499.html?gatewayAdapt=glo2usa4itemAdapt>
- Radxa heatsink: <https://www.aliexpress.us/item/3256806829446736.html?gatewayAdapt=glo2usa4itemAdapt>
- USB A to USB C data cable: <https://www.aliexpress.us/item/3256805660336073.html?spm=a2g0o.productlist.main.11.31ce63d3dP4ICu>
- Optional micro HDMI cable: <https://www.walmart.com/ip/Micro-USB-to-HDMI-Cable-Micro-USB-to-HDMI-Adapters-Black-E7Z3/10094271646>
- Optional independent power supply: <https://radxa.com/products/accessories/power-pd-30w/>

## Raspberry Pi Zero 2W

1. Raspberry Pi Zero 2W
2. Short USB to micro USB data cable
3. Optional mini HDMI cable if you want to inspect the device screen
4. Optional Raspberry Pi power supply if OTG power is unreliable

Use the USB port between the power USB port and the mini HDMI port.

![Raspberry Pi Zero 2W](/tezbake/tutorial/tezbaketezsignPiZeroOTG.png)

Example parts:

- Raspberry Pi Zero 2W: <https://www.pishop.us/product/raspberry-pi-zero-2-w/>
- Short micro USB data cable: <https://www.amazon.com/CableCreation-Charger-Compatible-Chromecast-Android/dp/B013G4EAEI>
- Optional mini HDMI cable: <https://www.walmart.com/ip/4K-HDMI-2-0-Cable-6ft-High-Speed-18Gbps-Mini-HDMI-HDMI-Cable-4K-60Hz-HDR-3D-2160P-1080P-Ethernet-Braided-Cord-32AWG-Audio-Return-ARC-Compatible-MacBo/14899622631>
- Optional Raspberry Pi power supply: <https://www.canakit.com/raspberry-pi-adapter-power-supply-2-5a.html>

## Flash the TezSign Image

Download the latest image for your device from [TezSign Releases](https://github.com/tez-capital/tezsign/releases).

Choose the `.img.xz` asset for your board, such as `radxa-zero3.img.xz` for the Radxa Zero 3W or `rpi0-2w.img.xz` for the Raspberry Pi Zero 2W. Extract it to an `.img` file on your computer before flashing. On Ubuntu or Debian, you can use your archive manager or the terminal commands below.

> **Warning:** Flashing erases the entire selected card, including any TezSign keys and signer state. Before reusing a working TezSign card, follow [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) and verify your backup. Check the target card's size and device name carefully so you do not overwrite your computer's system disk or another drive.

You do not need to format or partition the card first. Flashing writes the image and its partition layout to the whole card; copying the `.img` file onto a mounted card does not flash it.

### macOS or Windows: Balena Etcher

1. Install and open [Balena Etcher](https://etcher.balena.io/).
2. Insert the microSD card and choose **Flash from file**, then select the extracted `.img` file.
3. Choose **Select target** and select the microSD card.
4. Click **Flash!** and wait for both writing and validation to finish successfully before removing the card.

If the operating system offers to format or initialize the flashed card, cancel that prompt.

### Ubuntu or Debian Desktop: GNOME Disks

Use **Disks** (GNOME Disks) if your computer has a graphical desktop. If it is not installed, open a terminal and run:

```bash
sudo apt update
sudo apt install gnome-disk-utility
```

1. Insert the microSD card and open **Disks** from the application menu, or run `gnome-disks`.
2. Select the microSD card in the left sidebar. Confirm its capacity and device name match the intended card.
3. Open the drive menu in the window header and choose **Restore Disk Image…**.
4. Select the extracted `.img` file, click **Start Restoring…**, and confirm the target. Enter your administrator password if asked.
5. Wait for the operation to finish without errors, then use the eject or power-off button for the card before removing it.

Use the drive-level **Restore Disk Image…** action, not a partition formatting action. See [Ubuntu's GNOME Disks instructions](https://ubuntu.com/desktop/docs/en/latest/how-to/create-a-bootable-usb-stick/#using-disks) for an illustrated walkthrough of the same process.

### Ubuntu or Debian Terminal: No Desktop Required

This method also works on a machine without a graphical desktop, including over SSH with the card reader attached to that machine.

1. Extract the downloaded image if you have not already done so. Replace the example path with your downloaded `.img.xz` file. `--keep` preserves the compressed download.

   ```bash
   sudo apt update
   sudo apt install xz-utils
   xz --decompress --keep "/path/to/tezsign.img.xz"
   ```

   Continue only when extraction succeeds. The next steps use the resulting `.img` file, not the compressed `.img.xz` file.

2. Run this command before inserting the card, then again after inserting it. Identify the newly appearing card by its size and model:

   ```bash
   lsblk -p -o NAME,SIZE,MODEL,TYPE,MOUNTPOINTS
   ```

   The target must be the **whole card**, such as `/dev/sdb` or `/dev/mmcblk0`, not a partition such as `/dev/sdb1` or `/dev/mmcblk0p1`. These are examples only; use the device name shown on your machine. Never select the disk containing your system's `/`, `/boot`, or swap.

3. Unmount each mounted partition on the target card. For example, if a card partition appears as `/dev/sdX1` with a mount point:

   ```bash
   sudo umount /dev/sdX1
   ```

   Replace that placeholder with the actual partition name, such as `/dev/mmcblk0p1` for a built-in reader. Repeat for every mounted partition on the card, then run `lsblk` again to confirm that none has a mount point. If a partition is busy, close any file manager or terminal using it and retry; do not flash while it is mounted.

4. Replace both placeholders below: `if=` must point to your extracted image, and `of=` must point to the **whole target card**. Check them before running the command:

   ```bash
   sudo dd if="/path/to/tezsign.img" of=/dev/sdX bs=4M status=progress conv=fsync,nocreat
   ```

   `status=progress` displays write progress. `conv=fsync` flushes the write before `dd` finishes, and `nocreat` prevents a mistyped, nonexistent target path from being created as a regular file. `dd` does not ask for confirmation or validate the image after writing.

5. Wait until `dd` returns to the prompt without errors, then run:

   ```bash
   sync
   ```

   Wait for `sync` to finish. If the desktop has mounted any card partitions again, unmount them before removing the card. If writing reported an error, do not use the card as a completed flash.

### After Flashing

On Linux, a current TezSign card has three partitions:

- `app`
- `boot`
- `data`

The `data` partition contains the `tezsign` folder used for backup, restore, and migration. macOS does not mount the `data` partition correctly, so use Linux for TezSign data backup and restore work.

Legacy cards may instead have four partitions, such as `RPICFG`, `armbi_root`, `app`, and `data`. These need a fresh image plus a `data/tezsign` restore to migrate to the current layout. Check [Updating TezSign](/tezsign/tutorials/updating/#check-image-compatibility) using the actual card layout rather than its image date.

See [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) before updating, reflashing, or migrating a TezSign card.

For same-image spare cards, you can also clone a working SD card. A dual-slot reader or two USB SD-card readers makes this easiest. Cloning is a backup method, not a way to move onto a newer TezSign image generation.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
