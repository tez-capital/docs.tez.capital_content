---
title: "Hardware Requirements"
weight: 3
type: docs
summary: "Minimum and recommended hardware specifications for running a Tezos baker"
---

## Hardware Requirements for Tezos Baking

Running a Tezos baker requires a dedicated machine. This page lists minimum and recommended specifications for the **baking node** (the computer running TezBake and the Tezos node), plus requirements for the **signing device**.

---

## Baking Node

### Minimum Specifications

These are the lowest specs on which a Tezos baker can run. Performance may be tight, especially during bootstrapping and protocol upgrades.

| Component | Minimum |
|-----------|---------|
| CPU | 3 cores (x86-64 or ARM64) |
| RAM | 8 GB |
| Storage | 256 GB SSD |
| Network | 50 Mbps symmetric, wired Ethernet |
| OS | Ubuntu 22.04 LTS or Debian 12 (recommended) |

### Recommended Specifications

For comfortable operation, headroom during syncing, and the ability to run monitoring tools alongside the baker:

| Component | Recommended |
|-----------|-------------|
| CPU | 8 cores |
| RAM | 16 GB |
| Storage | 512 GB NVMe SSD |
| Network | 100 Mbps+ symmetric, wired Ethernet |
| OS | Ubuntu 22.04 LTS or Debian 12 |

> **💡 TIP:** A mini PC (Intel N100, AMD Ryzen 5, or similar) with 16 GB RAM and a 512 GB NVMe SSD is an excellent and affordable baking node. These are widely available for $150–250.

---

## Storage: SSD is Mandatory

> **⚠️ WARNING: HDDs will not work.**
>
> A traditional spinning hard drive (HDD) is **not suitable** for running a Tezos node. The node performs random read/write operations at a rate that will cause an HDD to fall behind and eventually become unable to keep up with the chain. You **must** use an SSD (or NVMe).

### Disk Space Growth

The Tezos full node currently requires approximately **100 GB** of disk space, and this grows over time as the chain advances.

| Mode | Approximate Size | Notes |
|------|-----------------|-------|
| Full node (rolling) | ~100 GB+ | The standard baking mode; grows slowly |
| Archive node | 1+ TB | Full history; not needed for baking |

> **ℹ️ INFO:** TezBake runs a **rolling** node by default — this is the recommended mode for bakers. It retains enough history to participate in consensus without storing the entire chain history. 256 GB provides comfortable headroom; 512 GB is future-proof.

---

## Network Requirements

| Requirement | Value |
|-------------|-------|
| Connection type | Wired Ethernet (never Wi-Fi) |
| Minimum speed | 50 Mbps down / 50 Mbps up |
| Recommended speed | 100 Mbps+ symmetric |
| IP address | Static (not DHCP) |
| Ports needed | 9732 (Tezos P2P), 8732 (RPC, local only) |

> **⚠️ WARNING: Do not use Wi-Fi.**
>
> Wi-Fi introduces latency and occasional packet loss that can cause missed attestations. Always connect your baking node via a wired Ethernet cable.

> **⚠️ WARNING: Use a static IP.**
>
> DHCP can fail to renew leases, causing connectivity drops. Configure a static IP address for your baking node.

---

## Signing Device (TezSign)

TezSign is the recommended signing solution. It runs on very modest hardware — the private key operations it performs are lightweight.

| Device | CPU | RAM | Storage | Cost |
|--------|-----|-----|---------|------|
| Raspberry Pi Zero 2W | Quad-core ARM Cortex-A53 @ 1 GHz | 512 MB | microSD (8 GB min) | ~$15–20 |
| Radxa Zero 3 | Quad-core ARM Cortex-A55 @ 1.8 GHz | 2–8 GB | eMMC / microSD | ~$25–40 |

Both devices are more than sufficient for signing Tezos operations. The Pi Zero 2W is the most common choice due to its low cost and wide availability.

> **💡 TIP:** Buy two TezSign-compatible boards — one for primary baking, one as a backup. Two Pi Zero 2W boards cost less than $40 total, less than half the price of a single Ledger.

**→ See also:** [Choose Your Setup](/getting-started/choose-your-setup/) | [Baking with TezSign](/tezsign/tutorials/)

---

## Power & Reliability

| Item | Recommendation |
|------|---------------|
| UPS (Uninterruptible Power Supply) | Strongly recommended — attach baking node + ISP router |
| Dedicated machine | Yes — do not share with daily-use workloads |
| Monitoring | Configure [TezWatch](/tezwatch/) alerts via Discord or Telegram |

> **ℹ️ INFO:** Tezos does **not** slash bakers for downtime caused by hardware failure or power outage. You will miss rewards during downtime, but you will not lose stake. However, bakers who fall below 67% attestation participation for 2 consecutive cycles risk deactivation. Rewards are proportional to attestations made — there is no cliff penalty.

---

## Complete Recommended Setup Bill of Materials

| Item | Example | Approx. Cost |
|------|---------|-------------|
| Mini PC (baking node) | Intel N100 mini PC, 16 GB RAM, 512 GB NVMe | $150–250 |
| Primary TezSign | Raspberry Pi Zero 2W + microSD + USB cable | $20–30 |
| Backup TezSign | Second Pi Zero 2W | $20–30 |
| UPS | APC Back-UPS 600VA or similar | $60–100 |
| **Total** | | **~$250–410** |

---

## Related Pages

- [Choose Your Setup](/getting-started/choose-your-setup/) — Pick the right signing option
- [Best Practices](/getting-started/best-practices/) — Reliability and safety guidelines
- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) — Full setup walkthrough

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
