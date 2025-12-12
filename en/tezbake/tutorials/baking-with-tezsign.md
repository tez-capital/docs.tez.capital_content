---
title: "Baking with TezSign"
weight: 3
type: docs
summary: Integrating the TezSign hardware signer with your TezBake setup.
---

## Overview

This guide covers how to configure **TezSign** (hardware signer) to work seamlessly with **TezBake**. It assumes you're setting up your hardware signer on the same machine as your baking machine, as part of an all-in-one setup. You do not have to use TezBake to setup TezSign; instead you can use the helper app.

- **Security First:** TezSign ensures your baking keys remain secure on hardware and never leave the device.
- **Prerequisites:** You must have a supported TezSign device ready.

> ⚠️ **Disclaimer**: TezSign is provided without any guarantee. Use it at your own risk.

---

## Phase 0: Purchase SBC and image SD card

Generally speaking, the Radxa Zero 3 (the lowest available configuration) as well as the Raspberry Pi Zero 2W are great choices for your TezSign setup. Purchase 2 devices if you worry about the possibility of downtime if your SBC fails. Also purchase multiple SD cards. It's good to have at least 3 SD cards: one for baking, one for backup of your baking SD card and one spare to replace a failed card. 8GB SD cards will do just fine. Ensure you are getting the "enterprise" or "endurance" versions if possible.

Sample shopping lists

- Radxa Zero 3
      1. Radxa ZERO 3W RK3566 4-core CPU SBC, GPU, NPU, HDMI with 1080P Output, and WiFi 6 & BT 5.4, Single Board Computer <https://www.aliexpress.us/item/3256807428419499.html?gatewayAdapt=glo2usa4itemAdapt>
      2. Radxa Heatsink 5519A, Designed for Radxa ZERO 3W / 3E, Easy to Install and Remove. <https://www.aliexpress.us/item/3256806829446736.html?gatewayAdapt=glo2usa4itemAdapt>
      3. USB A to USB C Cable10Gbps Data Transfer and 60W 3A Fast Charging Cable USBC 3.1/3.2 Cable <https://www.aliexpress.us/item/3256805660336073.html?spm=a2g0o.productlist.main.11.31ce63d3dP4ICu>
- Raspberry Pi Zero 2W
      1. Raspberry Pi Zero 2 W (add case & heatsink) <https://www.pishop.us/product/raspberry-pi-zero-2-w/>
      2. CableCreation Short Micro USB Cable, USB to Micro USB 24 AWG Triple Shielded Fast Charger Cable, Compatible with PS5/PS4, Raspberry Pi Zero, Chromecast, Phone, 0.5FT/6 inch Black <https://www.amazon.com/CableCreation-Charger-Compatible-Chromecast-Android/dp/B013G4EAEI>

### 1. Download gadget image and flash SD card

Navitate to the release page and download the latest image for your RPI or Radxa device: [TezSign Releases](https://github.com/tez-capital/tezsign/releases)

Once downloaded, extract the image. The image will be around 1.5GB - 2GB once it's been extracted.

Use [Balena Etcher](https://etcher.balena.io/) (or a tool you are familiar with) to flash the gadget image to your SD card.

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

### 2. Initialize the TezSign setup

Initialize the signer configuration. This creates the `/bake-buddy/signer/tezsign.config.hjson` file and runs TezBake's side TezSign setup.

```bash
tezbake setup-tezsign --init --platform
```

> **Configuration Note:** You typically do not need to modify the generated configuration file unless you are managing multiple devices or have specific advanced requirements.

## Phase 2: Device Preparation

Please note that it's highly recommended to complete this step on a machine separate from your TezBake baking machine. This ensures the highest level of security.

Use the companion app to complete the setup, available here: [TezSign Releases](https://github.com/tez-capital/tezsign/releases)

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

### 2. Generate your baking keys directly on the device

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

### 1. Import Keys to TezBake client

Link the keys stored on your TezSign device to TezBake's configuration using local aliases.

Depending on the context of your setup, you will determine how to name your TezSign wallet aliases within TezBake itself.

When you're first switching to a new consensus key or to tz4 baking, you will already have a key called `baker`. This is the default key that always gets used in TezBake. In addition to this key, you will also import your consensus and companion keys, from TezSign. Once you are actively using your TezSign keys, you can then import the `consensus` key as the `baker` key as described below.

If you're aleady baking using tz4 keys, it's recommended to alias your `consensus` key on the TezSign signer to the `baker` key.

The desired end stake of your TezBake signing experience is to use `baker` for `consensus` and to use the TezSign backend directly (see below). This way there are no missing keys reported by `tezbake info`. You need to ensure the migration to this desired state is performed one step at a time, respecting the in-between states.

**Syntax:**

```bash
tezbake setup-tezsign --import-key=<tezsign key alias> --key-alias=<octez key alias> [--force]
```

**Example (Importing the Consensus and Companion Keys) -- IF MIGRATING TO BLS/tz4:**

```bash
tezbake setup-tezsign --import-key=consensus --key-alias=consensus
tezbake setup-tezsign --import-key=companion --key-alias=companion
```

**Example (Importing the Consensus and Companion Keys -- IF ALREADY ON BLS/tz4:**

```bash
tezbake setup-tezsign --import-key=consensus --key-alias=baker
tezbake setup-tezsign --import-key=companion --key-alias=companion
```

> **Caution**: Ensure your `--key-alias` does not conflict with existing keys you wish to keep. Use the --force flag only if you intend to overwrite an alias.

### 2. Import Keys to TezBake node and signer

To bake with multiple keys (e.g., separate keys for consensus and DAL), you must register them in the node configuration.

Create or edit the following file: `/bake-buddy/node/additional_key_aliases.list`.
Add the key aliases you used in the `Import Keys to TezBake` step.

**IF MIGRATING TO BLS/tz4:***
As per the example we used consensus and companion so our file would look as follows:

```toml
consensus
companion
```

> NOTE: Later on when your `consensus` key activates you can reimport it under `baker` alias (the default one) with `--force`. Then you can remove consensus from additional keys and run `tezbake upgrade` again.
> NOTE: Only the import alias from `--key-alias=<THIS ALIAS>` is relevant. The internal tezsign alias can differ.

**IF ALREADY ON BLS/TZ4:**

```toml
companion
```

> The consensus keys is not listed because it's the default assumed baking key by TezBake

### 3. Retrieve Public Keys & Proofs & Register them on the chain

To register your keys on-chain (via [TezGov](https://gov.tez.capital/) or `octez-client`), you need the Public Key (BLpk) and the Proof of Possession (PoP)

```bash
tezbake tezsign status --full
```

The easiest way to activate your newly generated keys is by using the [TezGov](https://gov.tez.capital/) web portal. Connect with your manager Ledger or similar and within the `Baker Management` area, set your consensus and companion key details. You will need your BLpk's, first, and after filling them out, the prompt will ask you for your PoP's.

Bakers must now set their new consensus and companion keys together when changing to tz4 signing at first. Later down the road, you will be able to only rotate your consensus key if so desired. The companion key is a **must** for tz4 BLS signing, to perform the DAL-related duties. When you see "companion," think "DAL." **Consensus and companion always go together.**

Registering using the CLI is somewhat more challenging and no longer recommended but possible.

Here's an example to fill in your own details, assuming you have your baker manager wallet imported as `baker` (this is no longer recommended, you should have your consensus key imported as `baker` and manage your baker parameters with <gov.tez.capital>):

```bash
tezbake signer client set consensus key for baker to consensus --consensus-key-pop BLsig9xyDJ29WjExJgRAvPXrD5u46aTn1uEmrhz12VKXtrUSzP34FCqgUF56L3otpzB1kLuzUCaLk2tzT2309GKLDgj209jeslkjes3mr43590jfgLKDJd09j3092jLKDFJnlksjodijF3
tezbake signer client set companion key for baker to companion --companion-key-pop BLsig9xyDJ29WjExJgRAvPXrD5u46aTn1uEmrhz12VKXtrUSzP34FCqgUF56L3otpzB1kLuzUCaLk2tzT2309GKLDgj209jeslkjes3mr43590jfgLKDJd09j3092jLKDFJnlksjodijF3
```

### 4. Reconfigure

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

### 3. Unlock

```bash
tezbake tezsign unlock
```

Select both keys and input the passphrase you chose when initializing the device.

You can check the lock/unlock status of your TezSign keys

```bash
tezbake tezsign status
```

## Advanced: Direct TezSign Backend

> **Caution:** Avoid switching to the TezSign backend during the initial transition phase. To ensure a seamless transition, continue using the default backend. Once you fully rely on tz4 TezSign keys, you can safely switch to the TezSign backend.

You can configure TezBake to bake directly through the TezSign backend, bypassing the standard `octez-signer`.

Before: `<octez-node><octez-signer><tezsign>`
After: `<octez-node><tezsign>`

This setup offers slightly faster execution and lower latency by directly utilizing TezSign's hardware capabilities.

**How to Enable:**

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
