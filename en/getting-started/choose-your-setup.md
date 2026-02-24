---
title: "Choose Your Setup"
weight: 2
type: docs
summary: "Compare signing options and choose the right baking setup for your needs"
---

## Choosing Your Baking Setup

Before you install anything, you need to decide how your baker will **sign** baking operations. The signing device holds the key that authorises your blocks and attestations — it's the most important security decision you'll make.

There are three options:

| Signer | What It Is | tz4 Support | Cost | Recommended? |
|--------|-----------|------------|------|--------------|
| **TezSign** | Dedicated single-board computer (Pi Zero 2W or Radxa Zero 3) running signing software | ✅ Yes | ~$20–30 hardware | ✅ **Yes — best choice for new bakers** |
| **Ledger** | General-purpose hardware wallet (Nano S Plus / Nano X) | ❌ No | ~$80–150 | ⚠️ Legacy — still supported |
| **Soft key** | Private key stored in a file on the baking node | ❌ N/A | Free | 🚫 Not recommended |

> **⚠️ WARNING: Ledger and tz4**
>
> Ledger hardware wallets **do not support tz4 consensus keys** for baking operations. Modern Tezos protocols use tz4 (BLS12-381) keys for enhanced efficiency. If you start with a Ledger you will be limited to tz1 keys for baking. TezSign supports tz4 natively.

---

## Decision Tree

Use this guide to find the right setup for you:

```
Are you setting up for the first time?
│
├── YES ──► Use TezSign ✅
│            Cheap, secure, tz4-capable, purpose-built for baking.
│            → See: Baking with TezSign
│
└── NO — I already have a Ledger
     │
     ├── Am I happy to stay on tz1 keys?
     │    └── YES ──► Keep your Ledger, continue as-is ✅
     │
     └── Do I want tz4 consensus keys or maximum future-proofing?
          └── YES ──► Migrate to TezSign
               → See: Baking with TezSign (migration section)
```

---

## Detailed Comparison

### TezSign ✅ Recommended

TezSign turns an inexpensive single-board computer into a dedicated hardware signing device. It connects to your baking node over USB and signs nothing except Tezos baking operations.

**Pros:**
- Supports tz4 (BLS12-381) consensus keys — required for cutting-edge protocol features
- Purpose-built: 100% of its job is signing baking ops
- Can stay plugged in 24/7 without conflicts
- Very affordable (~$20–30 for the hardware)
- No need to stop the baker to vote on governance proposals (use a second device)

**Cons:**
- Requires a small amount of initial setup (flashing an SD card / eMMC)
- Less familiar to people who already own a Ledger

**Supported hardware:**
- Raspberry Pi Zero 2W
- Radxa Zero 3

> **💡 TIP:** You can buy two Pi Zero 2W boards for less than the price of one Ledger. Running a primary + backup TezSign is the gold standard setup.

**→ Get started:** [Baking with TezSign](/tezsign/tutorials/)

---

### Ledger ⚠️ Legacy

Ledger Nano S Plus and Nano X are general-purpose hardware wallets that can be used for baking. They are no longer the recommended option for new bakers but remain fully supported.

**Pros:**
- Many existing bakers already own one
- Well-known, battle-tested device

**Cons:**
- **Does not support tz4 consensus keys** for baking — you are locked to tz1 keys
- General-purpose device, not optimised for 24/7 baking
- Can't be used for governance voting at the same time as baking (must stop baker first)
- Original Nano S is **not supported** (too slow — causes missed attestations)

> **ℹ️ INFO:** If you already bake with a Ledger and are satisfied, there is no urgent need to migrate. tz4 keys provide efficiency and future-proofing, but tz1 baking continues to work.

**→ See also:** [Ledger vs TezSign](/getting-started/ledger-vs-tezsign/)

---

### Soft Key 🚫 Not Recommended

A soft key stores your private baking key as a file on the baking node itself. If the node is compromised, the key is exposed.

> **⚠️ WARNING:** Soft keys are not recommended for any baker with real stake. Use a hardware signer. Soft keys may be acceptable for testnet or experimentation only.

---

## Recommended Setup Summary

| Role | Hardware |
|------|----------|
| Baking node | Dedicated computer — see [Hardware Requirements](/getting-started/hardware-requirements/) |
| Primary signer | TezSign (Pi Zero 2W or Radxa Zero 3) |
| Backup signer | Second TezSign (strongly recommended) |
| UPS | Battery backup for node + ISP router |

> **💡 TIP:** A complete new-baker setup — dedicated mini PC + two TezSign devices — can be assembled for under $200. See [Hardware Requirements](/getting-started/hardware-requirements/) for specs.

---

## Next Steps

- **New baker with TezSign:** → [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/)
- **Using a Ledger:** → [Ledger vs TezSign](/getting-started/ledger-vs-tezsign/)
- **Check your hardware:** → [Hardware Requirements](/getting-started/hardware-requirements/)
- **Understand the basics first:** → [What is Baking?](/getting-started/what-is-baking/)

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
