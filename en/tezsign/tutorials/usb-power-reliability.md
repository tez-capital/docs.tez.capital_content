---
title: "USB and Power Reliability"
weight: 4
type: docs
summary: Fix TezSign USB, OTG, power, and host-controller issues
---

## When to Use This

Use this KB if TezSign does not appear over USB, disconnects unexpectedly, fails to boot from host power, or shows USB timeout errors.

## Power Checklist

In some cases, powering the TezSign device only through the OTG USB port may not provide consistent power.

Try these options:

1. Use the dedicated power port on the TezSign device for independent power.
2. Use a powered USB hub on the OTG USB port.
3. Check host BIOS settings so the USB port stays powered.

## BIOS Settings

Check these settings on the baker host:

- USB power delivery in Soft Off state (S5): enabled
- ErP Ready / EuP Ready / ErP Compliance: disabled
- Deep S4/S5 / Deep Power Saving / Pseudo G3: disabled
- Resume by USB Device / Power On by USB: enabled

To enter BIOS from Linux:

```bash
sudo systemctl reboot --firmware-setup
```

## USB 3.0 Controller Issues

TezSign USB gadget mode may have issues with USB 3.0 (xHCI) controllers.

Symptoms can include:

- `error -110` or `error -62` in `dmesg`
- Device gets no power or does not boot
- Device never appears in `lsusb`
- Repeated timeout errors

Try these fixes in order:

1. Use a different short, high-quality data cable. Avoid charge-only cables.
2. Use a USB 2.0 port if the host has one.
3. Place a USB 2.0 hub between the host and TezSign device to force USB 2.0 negotiation.
4. Check BIOS settings for xHCI mode, USB suspend, legacy USB support, and always-on USB power.

## Reset the USB Port

If the device stops responding:

```bash
tezbake tezsign advanced usb-port-reset
tezbake stop --signer
tezbake start --signer
```

Then verify status:

```bash
tezbake tezsign status
tezbake info --signer
```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
