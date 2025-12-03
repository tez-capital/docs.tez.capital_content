---
title: "Updating"
weight: 1
type: docs
summary: How to update TezSign firmware and application
---

## Keeping TezSign Up-to-Date

> **⚠️ WARNING: Experimental Updater**
> The `tezsign_updater` is currently experimental. **Please backup all files from your data partition before proceeding.**

> **IMPORTANT: Image Compatibility Note**
> This update process **only works if you are using an image from 21.11.2025 or newer**.
>
> **If you are using an older image:**
> 1. Backup your data partition.
> 2. Re-flash the SD card with the latest full image.
> 3. Copy your backed-up files into the dedicated `tezsign` folder on the new data partition. (This is required due to a one-time layout change).

### Update Procedure

1.  **Stop the device:** Disconnect the TezSign device from the baker machine. If you use external power, power it off.
2.  **Remove SD Card:** Eject the SD card from the device.
3.  **Connect to Manager:** Insert the SD card into the reader on your manager machine.
4.  **Download Updater:**
    If you do not have it already, download the `tezsign_updater` from the [GitHub Releases page](https://github.com/tez-capital/tezsign/releases).
    
    *Example command to download and make executable:*
    ```bash
    wget [https://github.com/tez-capital/tezsign/releases/download/release-202512022318/tezsign_updater_linux_amd64](https://github.com/tez-capital/tezsign/releases/download/release-202512022318/tezsign_updater_linux_amd64) -O tezsign_updater && chmod +x tezsign_updater
    ```

5.  **Run the Updater:**
    Execute the updater tool. It will automatically scan for viable SD cards, check partition layouts, and verify the existence of TezSign files.

    ```bash
    sudo ./tezsign_updater
    ```
    > **Note:** You can run `./tezsign_updater -h` to see all available options.

6.  **Select SD Card:**
    Choose the SD card you wish to flash from the available list. The tool will indicate **OK** for devices that match the TezSign layout.

7.  **Choose Update Type:**
    Select the kind of update you wish to perform:
    * **Full:** Updates all partitions except the data partition. *(Slower)*
    * **App:** Updates only the TezSign application. *(Fast)*

8.  **Finish:**
    Once the update is complete, remove the SD card from your manager machine, insert it back into your TezSign device, and reconnect it to the baker machine.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)