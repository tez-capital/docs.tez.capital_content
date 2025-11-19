---
title: "Tezbake + TezSign Setup"
weight: 3
type: docs
summary: Integrating the TezSign hardware signer with your Tezbake setup.

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

This guide covers how to configure **TezSign** (hardware signer) to work seamlessly with **Tezbake**.

This guide covers how to configure **TezSign** (hardware signer) to work seamlessly with **Tezbake**.

- **Security First:** TezSign ensures your baking keys remain secure on hardware and never leave the device.
- **Prerequisites:** You must have a supported TezSign device ready..

> ⚠️ **Disclaimer**: TezSign is provided without any guarantee; use it at your own risk.

---

## Phase 1: Device Preparation

Recommended Environment: A separate, secure workstation (not your baking server).

### 1. Initialize device
Connect your TezSign device to your workstation and run:
```bash
tezbake tezsign init
```
> **Critical Warning**: > You will be prompted to set a Master Password. Choose this carefully. It CANNOT be changed later.

### 2. Generate your baking keys directly on the device.
Generate your baking keys directly on the hardware. In this example, we will generate keys for Consensus and DAL.
```bash
tezbake tezsign new consensus dal
```

In this example, we generate two keys named "consensus" and "dal". You can create as many as you need.

> **Note:**  For baking with the Data Availability Layer (DAL), distinct tz4 keys are required - the consensus and companion key.

### 3. Retrieve Public Keys & Proofs
To register your keys on-chain (via [tezgov](https://gov.tez.capital/) or `octez-client`), you need the Public Key (BLpk) and the Proof of Possession (PoP)
```bash
tezbake tezsign status --full
```
Once you have recorded your key information, **move the TezSign device to your Baker machine**.

## Phase 2: Baker Configuration
Environment: Your Tezbake server.

### 1. Install Latest TezBake
TezSign support is now available in the latest version of TezBake.
```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
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

> **Configuration Note** You generally do not need to edit the generated config file unless you require auto-unlocking functionality or manage multiple devices.

### 3. Import Keys to TezBake
Link the keys stored on your TezSign device to TezBake's configuration using local aliases.

**Syntax:**
```bash
tezbake setup-tezsign --import-key=<tezsign key alias> --key-alias=<octez key alias> [--force]
```
**Example (Importing the Consensus Key):**
```bash
tezbake setup-tezsign --import-key=consensus --key-alias=consensus
tezbake setup-tezsign --import-key=dal --key-alias=dal
```

> **Caution**: Ensure your `--key-alias` does not conflict with existing keys you wish to keep. Use the --force flag only if you intend to overwrite an alias.

## Phase 3: Node Configuration

### 1. Register Additional Keys
To bake with multiple keys (e.g., separate keys for consensus and DAL), you must register them in the node configuration.

Create or edit the following file: `/bake-buddy/node/additional_key_aliases.list`.
Add the key aliases you used in the `Import Keys to TezBake` step.

As per example we used consensus and dal so our file would look as follows:
```toml
consensus
dal
```

> NOTE: Later on when your `consensus` key activates you can reimport it under `baker` alias (the default one) with `--force`. Then you can remove consensus from additional keys.

> NOTE: : Only the import alias from `--key-alias=<THIS ALIAS>` is relevant. The internal tezsign alias can differ.

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
Check that Tezbake sees your new keys:
```bash
tezbake info
```
If your keys appear in the info output, you are ready to bake!

## Advanced: Direct TezSign Backend

> **Caution:** Avoid switching to the TezSign backend during the transition phase. To ensure a seamless transition, continue using the default backend. Once you fully rely on tz4 TezSign keys, you can safely switch to the TezSign backend.

You can configure Tezbake to bake directly through the TezSign backend, bypassing the standard `octez-signer`.

This setup offers faster execution and lower latency by directly utilizing TezSign's hardware capabilities.  

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

    **To Revert:**: Remove the `BACKEND` line from `app.json`, upgrade, and restart.

## Support & Questions

Need help or have questions?

- [Discord](https://discord.gg/cVGMA4MaNM)  
- [Telegram](https://t.me/tezcapital)
