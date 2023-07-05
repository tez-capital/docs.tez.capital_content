---
title: "How to Fix Ledger Screen (WIP)"
weight: 1
type: docs
summary: Ledger Screen Blanking Fix for Nano S Plus and Nano X
---

## Ledger Screen Blanking Issue
* When baking with a Ledger Nano S, the device's screen is blanked out to preserve it, upon the first attestation (i.e. endorsement) of a block.
* When baking with a Ledger Nano S Plus or Nano X, the device's screen is never blanked out, which can cause the screen to burn out or burn in over time.

Since the Tezos protocol block time has been reduced to 15 seconds, bakers have had to migrate from the Ledger Nano S, to the faster Nano S Plus devices and even the Nano X's. Unfortunately, this means their Ledger devices are now susceptible to screen burn in and burn out, before an official Ledger Live patch for this issue is released.

We have developed an internal fix to the screen blanking issue which we eventually made public, so that other bakers can benefit from it as well. We are now recommending side loading our fix in order to preserve our baker Ledger screens while we try to convince the Ledger organization to include our fix in their official Ledger Live release. There is currently no way of knowing how long this will take.

**The patch described below is a temporary fix for this issue, until Ledger Live releases an official patch. We do not recommend side loading Ledger apps in general, but this is a necessary evil for the time being if bakers want to preserve their device screens. We need everyone's help to gently encourage Ledger to include the fix in their official Ledger Live release. The patch has been reviewed by several highly skilled engineers and has been used by a few bakers over the period of a few months without any issues.**

**Unfortunately, the patch is not compatible with the Ledger Nano X because it doesn't allow app side loading**

The source code can be found here: [https://github.com/alis-is/app-tezos/tree/baking-burn-in-protection](https://github.com/alis-is/app-tezos/tree/baking-burn-in-protection)

## Patch Side Loading Instructions

Side loading the patch is a simple process, but it does require a few steps. Please follow the instructions below carefully.

   ```
   cd ~
   git clone https://github.com/alis-is/app-tezos.git
   cd app-tezos
   git checkout baking-burn-in-protection
   sudo docker run --privileged --rm -ti -v "$(realpath .):/app" ghcr.io/ledgerhq/ledger-app-builder/ledger-app-builder-lite:latest
   APP=tezos_baking TARGET_NAME=nanos make load
   ```



---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 