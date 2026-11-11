---
title: "Troubleshooting TezSign"
weight: 9
type: docs
summary: Diagnose host and gadget mismatches, USB errors, incomplete backups, and locked keys
---

## Start with the Symptom

| Symptom | Next step |
|---------|-----------|
| `vendor probe failed`, `bad reply (iface) n=0`, or a signer restart loop after an upgrade | Check [Host and Gadget Compatibility](#host-and-gadget-compatibility). |
| Device absent from `lsusb`, intermittent disconnects, or USB timeouts | Collect the checks below, then see [USB and Power Reliability](/tezsign/tutorials/usb-power-reliability/). |
| Updater says `device does not match TezSign layout` | Inspect the card using [Check Image Compatibility](/tezsign/tutorials/updating/#check-image-compatibility). |
| Backup contains only `master.json` and `seed.bin` | Recover the whole folder, including per-alias key files; see [Key Files and Signing State](/tezsign/tutorials/back-up-and-restore-data/#key-files-and-signing-state). |
| Backup has the keys but no `level2.bin`, or unlocked HWM counters are zero | Read [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/). Missing HWM state alone does not require another restore or mandatory manual setting. |
| Keys are `LOCKED` and the password is forgotten | See [Forgotten Unlock Password](/tezsign/tutorials/automatic-unlock/#forgotten-unlock-password). |
| Keys are unlocked but the baker says a tz4 key was not provided | Check [Missing tz4 Consensus or Companion Alias](/tezbake/tutorials/troubleshooting/#missing-tz4-consensus-or-companion-alias). |

## Collect Read-Only Checks First

On the baker host, save the output of:

```bash
tezbake info --signer
tezbake signer info
tezbake tezsign list-devices
tezbake tezsign version
tezbake tezsign status --full
lsusb
```

If a device query fails, save its exact error and continue with the available checks. A failed version query does not identify which image is on the card.

On a default Linux TezBake installation, collect a bounded log sample:

```bash
sudo journalctl -u bb-default-signer-tezsign.service -n 200 --no-pager
sudo journalctl -k -n 200 --no-pager
```

If your instance uses another service name, use the name shown by `tezbake info --signer`. Record the Pi/Radxa model, power source, cable/port arrangement, and what changed before the failure. Keep passwords, private key files, and automatic-unlock configuration out of shared logs.

### Know Which Version You Are Reading

| Check | What it identifies |
|-------|--------------------|
| `tezbake info --signer` | Signer service and wallet status. |
| `tezbake signer info` | Installed signer package metadata, including its package version. |
| `tezbake tezsign version` | The connected **gadget's SD-card image** version and build date. It does not report the host executable's build. |
| `app/.image-version`, `.image-date`, `.image-flavour` on the SD card | Offline gadget-image identity and board flavour. |
| Installed host executable checksum matched to an official release asset | The specific host binary installed on the baker. |

The host is the program running on the baker; the gadget is the TezSign device and its SD-card image. Neither the TezBake version nor the date you flashed a card identifies both components. A TezBake signer upgrade can replace the host program even when you did not download TezSign separately.

For the standard `/bake-buddy` installation, the host checksum can be collected without running it:

```bash
sha256sum /bake-buddy/signer/bin/tezsign
```

Use the actual signer installation path if yours differs. Compare the checksum with the corresponding host asset on [TezSign Releases](https://github.com/tez-capital/tezsign/releases). Do not infer the host build from a gadget version response.

### Identify an Unreachable Gadget Offline

Stop baking with this signer, disconnect it, and remove all power before moving its SD card to a trusted Linux computer. Inspect the partition labels:

```bash
lsblk -p -o NAME,SIZE,FSTYPE,LABEL,MOUNTPOINTS
```

Find `app` on the intended card. Replace `/dev/APP_PARTITION` below with that exact partition path; do not assume its number from another card's layout. If it is already mounted, use its existing mount point in the `cat` commands instead.

```bash
sudo mkdir -p /mnt/tezsign-app
sudo mount -o ro /dev/APP_PARTITION /mnt/tezsign-app
sudo cat /mnt/tezsign-app/.image-version
sudo cat /mnt/tezsign-app/.image-date
sudo cat /mnt/tezsign-app/.image-flavour
sudo umount /mnt/tezsign-app
```

These are image metadata files in `app`; you do not need to read private files from `data/tezsign`. Missing metadata should be reported as missing, rather than replaced with a guessed version.

## Host and Gadget Compatibility

The October 4, 2026 [release-202610041210](https://github.com/tez-capital/tezsign/releases/tag/release-202610041210) changed the host's USB readiness probe. The matching gadget accepts both the new and legacy probe formats, so **update an older gadget before installing the newer host**.

| Host and gadget combination | Readiness-probe compatibility |
|-----------------------------|-------------------------------|
| June 15, 2026 host (`5bdd894d8511`) + June gadget | Both use the legacy probe. This is not the new-host/old-gadget mismatch. |
| June host + October 4 or newer gadget | The newer gadget accepts the legacy host probe. |
| October 4 or newer host + older gadget | Incompatible probe formats; update the gadget image. |
| October 4 or newer host + matching/newer gadget | Probe formats are compatible; investigate remaining errors rather than assuming a mismatch. |

The [registrar compatibility note](https://github.com/tez-capital/tezsign/blob/release-202610041210/app/ffs_registrar/readme.md) describes the new `0xC1` and legacy `0x81` request types. Errors such as `vendor probe failed: iface 1`, `libusb: i/o error [code -1]`, and `usbfs: process tezsign did not claim interface 1 before use` are diagnostic clues, not proof of one cause on their own.

### Choose the Update Order

1. If the gadget is already on a compatible October 4 or newer image, keep that card. Update an older host through `tezbake upgrade --signer` during maintenance; there is no need to reflash the gadget just because the host is older.
2. If the gadget is older, [back up the whole `data/tezsign` folder](/tezsign/tutorials/back-up-and-restore-data/) and [update or migrate the card](/tezsign/tutorials/updating/) first. Then update the signer package with `tezbake upgrade --signer` if needed. If the host was already upgraded, bringing the gadget to a compatible release addresses that mismatch.
3. If both versions are compatible and errors persist, follow [USB and Power Reliability](/tezsign/tutorials/usb-power-reliability/). A port reset does not change firmware compatibility.

An older spare is not automatically a useful recovery card: check its image compatibility and key identity before switching. Reflashing it with a supported image and restoring the whole folder can keep the original card intact. Keep only one baker using these keys active.

### Verify Recovery

With only the intended signer connected, confirm the gadget version, expected public addresses, and unlocked key state. Check `tezbake info --signer`, then inspect recent signer logs to ensure the restart loop and probe errors have stopped. Finally, verify successful signing and resumed attestations in the baker logs and [on Tezos.Systems after the restart](/getting-started/baker-signal/#check-attestation-and-dal). USB enumeration or a successful unlock alone is not proof of resumed baking.

If the failure remains, send the team the version evidence, exact error, and bounded logs collected above. Avoid changing key identities, HWM, and backend configuration together while diagnosing a USB connection failure.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
