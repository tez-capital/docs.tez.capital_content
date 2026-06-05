---
title: "Alias Cleanup After Activation"
weight: 5
type: docs
summary: Clean up temporary TezSign consensus aliases after tz4 keys activate on-chain
---

## When to Use This

Use this after your tz4 TezSign consensus key has activated on-chain and you want to transition from `baker`/`consensus`/`companion` to `baker`/`companion`, with the active TezSign consensus key loaded through the default `baker` alias.

Do not run this during the 3-cycle activation window. Keep `consensus` loaded until the tz4 consensus key is active on-chain.

## Re-Import the Consensus Key as `baker`

```bash
tezbake setup-tezsign --import-key=consensus --key-alias=baker --force
```

## Keep Only the DAL Companion as an Additional Key

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
```

## Remove the Stale Local `consensus` Alias

Remove the stale consensus alias from the node wallet, if present:

```bash
tezbake node client forget address consensus --force
```

Remove the stale consensus alias from the signer daemon wallet, if present:

```bash
tezbake signer signer forget address consensus --force
```

## Apply and Verify

```bash
tezbake upgrade
tezbake node show configuration.additional_key_aliases
tezbake node client list known addresses
tezbake signer signer list known addresses
tezbake info
tezbake info --dal
```

Expected final state:

- `configuration.additional_key_aliases` is exactly `["companion"]`.
- Node known addresses include `baker` and `companion`.
- Signer daemon known addresses include `baker` and `companion`.
- No local Octez alias named `consensus` remains.
- The on-chain consensus key is unchanged; only local alias wiring changed.

The TezSign device/internal key may still be named `consensus`. This cleanup only removes stale local Octez aliases from the node and signer daemon wallets.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
