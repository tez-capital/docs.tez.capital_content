---
title: "Baking with DAL"
weight: 8
type: docs
summary: TezBake Baking Tutorial for DAL
---

Follow along on Youtube!
{{< youtube 0EEgEHFl40Y >}}

## Introduction

The Tezos Data Availability Layer (DAL) enhances network scalability by efficiently handling large volumes of data off-chain, providing essential support for rollups and other layer-2 solutions. This guide explains how to set up DAL locally alongside your TezBake baker.

For more details on DAL, see [Tezos DAL Overview](https://tezos.gitlab.io/shell/dal_overview.html).

## DAL Rewards and Penalties

Running a DAL node directly impacts your baking income:

**Reward Impact:**
- DAL attestations account for approximately **10% of total block rewards** (2,275 out of 22,757 reward weight units)
- Successfully attesting to DAL shards earns you this portion of rewards
- Missing DAL attestations means losing ~10% of your potential baking income

**How It Works:**
1. When data is published to the DAL, it's split into "shards" distributed across bakers
2. Your baker must download and verify shards assigned to you within the attestation lag period
3. Your baker then declares whether it successfully obtained the shards (via the attestation operation)
4. If enough bakers attest to availability, the data blob is marked as available

**Attestation Lag:**
The protocol defines an attestation lag period during which bakers must download and verify their assigned shards. Your DAL node must be online and synchronized to meet this deadline. The lag gives bakers time to fetch data before they must attest.

**No Slashing for DAL Failures:**
Unlike double baking/attestation, failing to run DAL does not result in slashing of your stake. You simply miss out on the DAL portion of rewards.

> **⚠️ WARNING: IP Address Exposure Risk**
>
> The security implications for bakers in the DAL network are highlighted in the [official Octez documentation](https://octez.tezos.com/docs/shell/dal_overview.html).
>
> **Risk:**
> Since a baker's bandwidth in the DAL is proportional to their stake, it may be relatively straightforward to identify the IP address of a DAL node—especially for bakers with substantial stakes.
>
> **Mitigation:**
> The core team advises running your DAL node on a different IP address than your L1 node. This separation helps prevent unintentional exposure of your node's identity.
>
> **Solution for Separate Machines:**
> If you wish to run TezBake with DAL on a separate machine, consider using [TezBake Prism Tunneling](https://docs.tez.capital/tezbake/tutorials/baking-with-prism/), which is designed for setups across multiple hosts.

## New TezBake Setup with DAL

You can set up TezBake with DAL integration from scratch. Follow these steps:

1. **Run setup with DAL integration:**

```bash
tezbake setup --with-dal
```

2. Proceed with your usual setup steps (ledger integration, baker registration, etc.).

3. Continue to the [After DAL Setup](#after-dal-setup) section.

---

## Existing TezBake Setup

If you already have TezBake running without DAL, follow these steps:

1. **Install DAL:**

```bash
tezbake setup --dal
```

## After DAL Setup

1. **Inject attester profiles:**

This step does not require interaction with your Ledger:

```bash
tezbake update-dal-profiles --auto
```

If the above command fails, specify your **baker key** (not consensus key):

```bash
tezbake update-dal-profiles <your-baker-key>
```

2. **Restart TezBake to apply changes:**

```bash
tezbake stop && tezbake start
```

## Remote DAL Setup

Follow the Prism setup steps here: [Baking with Prism](/tezbake/tutorials/baking-with-prism/)

## Quick Troubleshooting

If you encounter issues or require immediate help, execute these commands to revert changes and reinstall:

```bash
tezbake remove --dal
tezbake setup --node  # choose 'yes' to merge config when prompted
tezbake stop && tezbake start
```

## Verify DAL Operation

Ensure your DAL process is correctly running by checking logs:

```bash
tezbake node log dal -f
```

Your logs should indicate the DAL is receiving block levels. Also, verify the TezBake baker logs are error-free regarding DAL integration:

```bash
tezbake node log baker -f
```

For additional DAL checks, refer to the [Nomadic Labs DAL Tutorial](https://tezos.gitlab.io/shell/dal_overview.html).

---

## Related Guides

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Complete setup guide
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Check DAL and baker logs
* [Updating](/tezbake/tutorials/updating/) - Keep TezBake up to date

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
