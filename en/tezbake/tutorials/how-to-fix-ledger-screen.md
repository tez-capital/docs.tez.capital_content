---
title: "How to Fix Ledger Screen"
weight: 1
type: docs
summary: Ledger Screen Blanking Fix for Nano S Plus
---

## Ledger Screen Blanking Issue
What is the Ledger screen blanking issue?
* When baking with a Ledger Nano S, the device's screen is blanked out to preserve it, upon the first attestation (i.e. endorsement) of a block.
* When baking with a Ledger Nano S Plus or Nano X, the device's screen is never blanked out, which can cause the screen to burn out or burn in over time.

Since the Tezos protocol block time has been reduced to 15 seconds, bakers have had to migrate from the Ledger Nano S, to the faster Nano S Plus devices and even the Nano X's. Unfortunately, this means their Ledger devices are now susceptible to screen burn in and burn out, before an official Ledger Live patch for this issue is released.

We have developed an internal fix to the screen blanking issue which we eventually made public, so that other bakers can benefit from it as well. We are now recommending side loading our fix on all Ledger Nano S Plus devices in order to preserve our baker Ledger screens while we try to convince the Ledger organization to include our fix in their official Ledger Live release. There is currently no way of knowing how long this will take.

**The patch described below is a temporary fix for this issue, until Ledger Live releases an official patch. We do not recommend side loading Ledger apps in general but this is a necessary evil for the time being if bakers want to preserve their device screens. We need everyone's help to gently encourage Ledger to include the fix in their official Ledger Live release.**

**Unfortunately, the patch is not currently loadable on the Ledger Nano X because it doesn't allow app side loading by design. This patch is also not to be used on the regular Ledger Nano S because the official firmware properly handles the screen blanking. Once Ledger officially releases our patch, it will work seamlessly on all Ledger Nano devices.**

The source code can be found here: [https://github.com/alis-is/app-tezos/tree/baking-burn-in-protection](https://github.com/alis-is/app-tezos/tree/baking-burn-in-protection)

The patch has been reviewed by several highly skilled engineers and has been used by a few bakers over the period of a few months without any issues.

## Patch Side Loading Instructions

### Install Prerequisites

You'll need to manually delete your old Tezos Baking app using Ledger Live, before you can side load the patched app. You'll also need to install docker and add your current user to the docker group.

   ```
   sudo apt update && sudo apt install docker.io -y
   sudo usermod -aG docker $USER
   ```

Reboot after this step.

**To use the patched app, you will need to update your Ledger Nano S Plus to Ledger firmware version 1.1.0+**
[https://support.ledger.com/hc/en-us/articles/4445777839901-Update-Ledger-Nano-S-Plus-firmware](https://support.ledger.com/hc/en-us/articles/4445777839901-Update-Ledger-Nano-S-Plus-firmware)

After finishing this step, ensure you do not have Ledger Live running when trying to perform the rest of the steps or while trying to bake.

### Install Patched App (version 2.3.3) on Ledger Nano Plus

Side loading the patch is a simple process, but it does require a few steps. Please follow the instructions below carefully.

   ```
   cd ~
   git clone https://github.com/alis-is/app-tezos.git
   cd app-tezos
   git checkout baking-burn-in-protection
   docker run --privileged --rm -ti -v "$(realpath .):/app" ghcr.io/ledgerhq/ledger-app-builder/ledger-app-builder-lite:latest
   cd tools
   ./gen-delegates.sh
   BOLOS_SDK=/opt/nanosplus-secure-sdk APP=tezos_baking TARGET_NAME=nanos2 make load
   ```

After running the `BOLOS_SDK` command, your Ledger Nano Plus will display a message asking you to confirm the installation of the app. Press both buttons to confirm the installation.

**When starting the app for the first time, you will be asked to confirm the execution of the app. Press both buttons to confirm. This is because the app is not published by Ledger.**

### Setup baker again

After uninstallin the old app and installing the new one (2.3.3), you will need to setup your Ledger Nano S Plus for baking again.

Plug in Ledger, input pin, open patched Tezos Baking app, and then run the following command:

   ```
   tezbake setup-ledger --main-hwm 3826854
   ```

Replace `3826854` with the latest current head block height. You can find this information on [tzstats.com](https://tzstats.com/).

Confirm the operation on your Ledger device and you're done!

### Blanking out Ledger Nano S Plus Screen

1. Plug in Ledger Nano S Plus
2. Input pin
3. Open patched Tezos Baking app (accept warning)
4. Hit the left button until you see `Application is ready`
5. Hit both buttons to blank out screen

The screen will also blank out when you perform your first attestation (i.e. endorsement) of a block.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 