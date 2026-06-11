---
title: "Baking Without DAL"
weight: 10
type: docs
summary: Exception-path guide for bakers who intentionally run TezBake without a local DAL node
aliases:
  - /tezbake/tutorials/baking-with-dal/
---

## Default Setup Includes DAL

DAL is part of the standard TezBake baker setup. The mainnet and testnet setup guides install the node, baker, signer, and DAL together with `tezbake setup --with-dal`.

Use this guide only when you intentionally need to run without a local DAL node. If you want DAL on another machine, another IP address, or another network segment, use [Baking with Prism](/tezbake/tutorials/baking-with-prism/) instead.

## What You Give Up

Running without DAL is not a recommended steady-state setup for an active baker.

- Your baker will miss the DAL portion of baking rewards, currently about 10%.
- Your baker may log warnings about attestations without DAL content.
- Your setup will not help provide data availability for rollups and other DAL users.
- This is not a slashing condition by itself, but it is still lost income and reduced network participation.

## When This Makes Sense

Use this only as an exception path:

- Temporary recovery while you are fixing a DAL host, disk, bandwidth, or network issue
- A test or lab setup where DAL participation is intentionally out of scope
- A constrained environment where you accept the reward loss
- A migration step before moving DAL to a separate host with Prism

If your reason is privacy or IP address exposure, do not remove DAL first. Prefer [Baking with Prism](/tezbake/tutorials/baking-with-prism/) so you can run DAL elsewhere while keeping DAL rewards.

## New Setup Without DAL

For normal production setup, stop here and follow [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) or [Baking on Testnets](/tezbake/tutorials/baking-on-testnets/).

If you intentionally want a new setup without DAL, omit `--with-dal`:

```bash
tezbake setup
```

For a testnet, keep the network configuration but omit `--with-dal`:

```bash
tezbake setup --node-configuration=https://configs.tez.capital/bakingnet.json
tezbake setup --node-configuration=https://configs.tez.capital/tallinnnet.json
```

Continue with the normal key import, baker registration, and staking steps from the relevant setup guide.

## Remove DAL From an Existing Setup

Stop TezBake before changing components:

```bash
tezbake stop
```

Remove the DAL component:

```bash
tezbake remove --dal
```

Re-apply the node configuration without DAL:

```bash
tezbake setup --node
tezbake upgrade
tezbake start
```

If prompted to merge configuration, choose `yes` unless you have a specific reason to discard your existing node settings.

## Verify the No-DAL State

Check TezBake status:

```bash
tezbake info
```

Watch baker logs for DAL-related warnings so you understand the reward impact:

```bash
tezbake node log baker -f
```

If you disabled DAL temporarily, keep a note of why and when you plan to restore it.

## Restore Local DAL Later

To return to the standard local DAL setup:

```bash
tezbake setup --dal
tezbake update-dal-profiles --auto
tezbake upgrade
tezbake start
```

If you use a tz4/BLS consensus key, confirm the DAL companion key is loaded by the baker:

```bash
tezbake node show configuration.additional_key_aliases
tezbake info --dal
```

For the common TezSign setup where the active consensus key is imported as `baker`, only `companion` needs to be listed as an additional alias:

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
tezbake upgrade
```

If your TezSign consensus key is imported as `consensus` instead of `baker`, include both aliases:

```bash
tezbake node modify --set configuration.additional_key_aliases '["consensus","companion"]'
tezbake upgrade
```

## Run DAL Elsewhere

If your goal is to avoid exposing the same IP address for your L1 node and DAL node, or you want the DAL node on a different host, do not use the no-DAL path.

Use [Baking with Prism](/tezbake/tutorials/baking-with-prism/) to connect TezBake components across hosts while keeping DAL enabled.

## Related Guides

- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Standard setup with DAL
- [Baking on Testnets](/tezbake/tutorials/baking-on-testnets/) - Testnet setup with DAL
- [Baking with Prism](/tezbake/tutorials/baking-with-prism/) - Run node, DAL, and signer across separate hosts
- [Key Aliases](/tezbake/tutorials/key-aliases/) - Confirm companion key alias configuration
- [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Debug DAL and baker issues

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
