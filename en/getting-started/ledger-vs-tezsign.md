---
title: "Ledger vs TezSign"
weight: 3
type: docs
summary: Understanding the difference between Ledger and TezSign for Tezos baking
---

## Overview

There are two options for signing baking operations on Tezos:

| Option | Description | Recommendation |
|--------|-------------|----------------|
| **Ledger** | Hardware wallet (Nano S Plus, Nano X) | Legacy option, still supported |
| **TezSign** | Purpose-built hardware signer (Radxa/RPi) | Recommended for new bakers |

## TezSign

TezSign is a purpose-built hardware signer for Tezos baking, based on affordable single-board computers like Raspberry Pi Zero 2W (~$20-30 in hardware). It connects via USB to your baking node and is designed to do one thing: sign baking operations securely.

**Key benefits:**
- Supports tz4 signatures required by modern Tezos protocols
- Can remain connected 24/7 dedicated solely to baking
- Purpose-built for baking operations

**Setup guide:** [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/)

## Ledger

Ledger hardware wallets (Nano S Plus, Nano X) can still be used for baking, but are no longer the recommended option for new bakers.

**Limitations:**
- Ledger no longer supports tz4 signatures for baking operations
- General-purpose device not optimized for 24/7 baking
- Cannot be used simultaneously for baking and governance on the same machine

**Note:** You must stop your baker before using your Ledger for governance voting on the same machine. See [TezGov Troubleshooting](/tezgov/tutorials/troubleshooting/) for details.

## TezBake Support

TezBake supports both Ledger and TezSign:

- **New bakers:** We recommend implementing TezSign from the start
- **Existing Ledger bakers:** Can continue using Ledger or migrate to TezSign

## Migrating from Ledger to TezSign

If you're currently baking with a Ledger and want to migrate to TezSign, follow the [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) guide.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
