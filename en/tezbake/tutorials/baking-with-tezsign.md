---
title: "Tezbake + TezSign Setup"
weight: 3
type: docs
summary: Integrating the TezSign hardware signer with your Tezbake setup
---

## Command Cheatsheet

| Task                               | Command Example                                                       |
| ---------------------------------- | --------------------------------------------------------------------- |
| Initialize TezSign Platform        | `tezbake setup-tezsign --init --platform`                             |
| Initialize Device                  | `tezbake tezsign init`                                                |
| Generate New Keys                  | `tezbake tezsign new <key-name-1> <key-name-2>`                       |
| Import Key to Tezbake              | `tezbake setup-tezsign --import-key=<tezsign-alias> --key-alias=<alias>` |
| Verify Keys                        | `tezbake info`                                                        |

---

## Overview

> **⚠️ This guide is a work in progress and may be subject to change. ⚠️**


This guide covers how to configure **TezSign** (hardware signer) to work seamlessly with **Tezbake**.

- **Security First:** TezSign ensures your baking keys remain secure on hardware.
- **Prerequisites:** You must have a supported device ready.

**TezSign is provided without any guarantee; use it at your own risk.**

> **Recommendation:** > It is recommended to prepare your device on a different machine than your baker. Follow the [TezSign Guide](https://github.com/tez-capital/tezsign) for initial device preparation before proceeding below.

---

## Setup Steps

### Step 1: Install Tezbake Prerelease

To use TezSign features, you currently need the prerelease version of Tezbake.

1. **Download and install the prerelease:**
```bash
   wget -q [https://github.com/tez-capital/tezbake/raw/main/install.sh](https://github.com/tez-capital/tezbake/raw/main/install.sh) -O /tmp/install.sh && sudo sh /tmp/install.sh --prerelease
```
2. Upgrade the instance: Ensure all packages are up to date.
```bash
tezbake upgrade
```
### Step 2: Configure TezSign Platform

Initialize the signer configuration on your baker.

```bash
tezbake setup-tezsign --init --platform
```

This creates the configuration file at `/bake-buddy/signer/tezsign.config.hjson`.

> **Configuration Note** > You generally do not need to edit this file.
> However, you may want to modify it if you need to automatically unlock TezSign after a reboot or if you are managing multiple devices.

### Step 3: Initialize the Device

If you haven't initialized the device on a separate machine yet, connect it and run:

```bash
tezbake tezsign init
```

> **Critical Warning**: > You will be prompted to set a Master Password. Choose this carefully. It CANNOT be changed later.

### Step 4: Generate Keys

Generate your baking keys directly on the device.

```bash
tezbake tezsign new consensus dal
```

In this example, we generate two keys named "consensus" and "dal". You can create as many as you need.

> **Why two keys?** > For baking with DAL (Data Availability Layer), you need two tz4 keys.

### Step 5: Import Keys

Now, link the keys on your TezSign device to your Tezbake configuration.

**Syntax:**
```bash
tezbake setup-tezsign --import-key=<tezsign key alias> --key-alias=<octez key alias> [--force]
```
**Example (Importing the Consensus Key):**
```bash
tezbake setup-tezsign --import-key=consensus --key-alias=consensus
```

> **Caution**: > Be careful not to accidentally overwrite existing keys that you still need. Use unique aliases or back up your current configuration.

### Step 6: Verify and Bake

1. Restart your setup to apply changes.
2. Check that Tezbake sees your new keys:
```bash
tezbake info
```
If your keys appear in the info output, you are ready to bake!

### Advanced: Direct TezSign Backend

You can configure Tezbake to bake directly through TezSign, bypassing the standard octez-signer.

Pros: Faster execution, lower latency.
Cons: Less flexible (relies solely on TezSign tz4 keys)

**How to Enable**

1. Open the signer configuration file: `/bake-buddy/signer/app.json`
2. Add `BACKEND: tezsign` inside the `configuration` block:
```yaml
// ...
        "configuration": {
            "BACKEND": "tezsign"
        },
// ...
```
3. Apply the changes:
```bash
tezbake upgrade
```
4. Restart the service.

> **Reverting:**: To switch back, simply remove the `BACKEND` line from `app.json`, run tezbake upgrade, and restart.

## Support & Questions

Need help or have questions?

- [Discord](https://discord.gg/cVGMA4MaNM)  
- [Telegram](https://t.me/tezcapital)
