---
title: "Baking with TezSign"
weight: 3
type: docs
summary: Integrating the TezSign hardware signer with your TezBake setup.

## Overview

This guide covers how to configure **TezSign** (hardware signer) to work seamlessly with **TezBake**. It assumes you're setting up your hardware signer on the same machine as your baking machine, as part of an all-in-one setup. You do not have to use TezBake to setup TezSign; instead you can use the helper app.

- **Security First:** TezSign ensures your baking keys remain secure on hardware and never leave the device.
- **Prerequisites:** You must have a supported TezSign device ready.

> ⚠️ **Disclaimer**: TezSign is provided without any guarantee. Use it at your own risk.

---

## Phase 0: Purchase SBC and image SD card

Generally speaking, the Radxa Zero 3 (the lowest available configuration) as well as the Raspberry Pi Zero 2W are great choices for your TezSign setup. Purchase 2 devices if you worry about the possibility of downtime if your SBC fails. Also purchase multiple SD cards. It's good to have at least 3 SD cards: one for baking, one for backup of your baking SD card and one spare to replace a failed card. 8GB SD cards will do just fine. Ensure you are getting the "enterprise" or "endurance" versions if possible.

### 1. Download gadget image and flash SD card

Navitate to the release page and download the latest image for your RPI or Radxa device: (TezSign Releases)[https://github.com/tez-capital/tezsign/releases]

Once downloaded, extract the image. The image will be around 1.5GB - 2GB once it's been extracted.

Use (Balena Etcher)[https://etcher.balena.io/] (or a tool you are familiar with) to flash the gadget image to your SD card.

Once done, plug in the SD card into your gadget and connect it to your baking computer with just a single USB cable. A separate power source is not required. Use the data connection USB port, not the power connection USB port.

## Phase 1: Baker Configuration

### 1. Install Latest TezBake
TezSign support is now available in the latest version of TezBake. 

```bash
wget -q https://github.com/tez-capital/TezBake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
```

Upgrade the instance to ensure all packages are up to date:

```bash
tezbake upgrade
```

### 2. Initialize the TezSign setup.
Initialize the signer configuration. This creates the `/bake-buddy/signer/tezsign.config.hjson` file and runs TezBake's side TezSign setup.

```bash
tezbake setup-tezsign --init --platform
```

> **Configuration Note:** You typically do not need to modify the generated configuration file unless you are managing multiple devices or have specific advanced requirements.

## Phase 2: Device Preparation

Please note that it's highly recommended to complete this step on a machine separate from your TezBake baking machine. This ensures the highest level of security.

Use the companion app to complete the setup, available here: (TezSign Releases)[https://github.com/tez-capital/tezsign/releases]

Command examples are provided for using the companion app and TezBake, if you go for the ease of setup.

### 1. Initialize device
Connect your TezSign device to your workstation and run:

```bash
# Using companion app
./tezsign-host init
# Using TezBake
tezbake tezsign init
```

> **Critical Warning**: > You will be prompted to set a Master Password. Choose this carefully. It CANNOT be changed later.

### 2. Generate your baking keys directly on the device.
Generate your baking keys directly on the hardware. In this example, we will generate keys for Consensus and DAL (i.e., companion key).

```bash
# Using companion app
./tezsign-host new consensus companion
# Using TezBake
tezbake tezsign new consensus companion
```

In this example, we generate two keys named "consensus" and "companion". You can create as many as you need.

> **Note:**  For baking with the Data Availability Layer (DAL), distinct tz4 keys are required - the consensus and companion key.

> **Backup:** Now that you have your keys on the TezSign device, it's a good idea to use Balena Etcher or a similar tool to clone the SD card such that you have a backup in case your original SD card fails.

## Phase 3: Node Configuration

### 1. Import Keys to TezBake
Link the keys stored on your TezSign device to TezBake's configuration using local aliases.

**Syntax:**

```bash
tezbake setup-tezsign --import-key=<tezsign key alias> --key-alias=<octez key alias> [--force]
```
**Example (Importing the Consensus and Companion Keys):**

```bash
tezbake setup-tezsign --import-key=consensus --key-alias=consensus
tezbake setup-tezsign --import-key=companion --key-alias=companion
```

> **Caution**: Ensure your `--key-alias` does not conflict with existing keys you wish to keep. Use the --force flag only if you intend to overwrite an alias.

### 2. Retrieve Public Keys & Proofs
To register your keys on-chain (via [tezgov](https://gov.tez.capital/) or `octez-client`), you need the Public Key (BLpk) and the Proof of Possession (PoP)

```bash
tezbake tezsign status --full
```

### 3. Register Additional Keys
To bake with multiple keys (e.g., separate keys for consensus and DAL), you must register them in the node configuration.

Create or edit the following file: `/bake-buddy/node/additional_key_aliases.list`.
Add the key aliases you used in the `Import Keys to TezBake` step.

As per example we used consensus and companion so our file would look as follows:
```toml
consensus
companion
```

> NOTE: Later on when your `consensus` key activates you can reimport it under `baker` alias (the default one) with `--force`. Then you can remove consensus from additional keys.

> NOTE: Only the import alias from `--key-alias=<THIS ALIAS>` is relevant. The internal tezsign alias can differ.

### 2. Reconfigure

```bash
tezbake upgrade
```

## Phase 4: Verify and Bake

### 1. Restart Services

Restart your setup to apply changes.
```bash
tezbake stop && tezbake start
```

### 2. Check Status
Check that TezBake sees your new keys:

```bash
tezbake info
```

If your keys appear in the info output, you are ready to bake!

## Advanced: Direct TezSign Backend

> **Caution:** Avoid switching to the TezSign backend during the transition phase. To ensure a seamless transition, continue using the default backend. Once you fully rely on tz4 TezSign keys, you can safely switch to the TezSign backend.

You can configure TezBake to bake directly through the TezSign backend, bypassing the standard `octez-signer`.

This setup offers slightly faster execution and lower latency by directly utilizing TezSign's hardware capabilities. 

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
tezbake upgrade --signer
```
4. Restart the service.

    **To Revert:**: Remove the `BACKEND` line from `app.json`, upgrade, and restart.

## Advanced: Automatic Unlock for TezSign

You can configure TezSign to unlock automatically by securely storing the device password.

### Steps to Enable Automatic Unlock

1. Run the following command to set and store the password:

```bash
tezbake setup-tezsign --password
```
This will prompt you to enter the password for your TezSign device.

*To unset the automatic unlock, simply set an empty password when prompted.*

2. Apply the changes by upgrading the configuration:

```bash
tezbake upgrade --signer
```

3. Restart if Required

```bash
tezbake stop && tezbake start
```

---

## Command Cheatsheet

| Task                               | Command Example                                                       |
| ---------------------------------- | --------------------------------------------------------------------- |
| Initialize TezSign Platform        | `TezBake setup-tezsign --init --platform`                             |
| Initialize Device                  | `TezBake tezsign init`                                                |
| Generate New Keys                  | `TezBake tezsign new <key-name-1> <key-name-2>`                       |
| Import Key to TezBake              | `TezBake setup-tezsign --import-key=<tezsign-alias> --key-alias=<alias>` |
| Verify Keys                        | `TezBake info`                                                        |

---

## Support & Questions

Need help or have questions?

- [Discord](https://discord.gg/cVGMA4MaNM)  
- [Telegram](https://t.me/tezcapital)
