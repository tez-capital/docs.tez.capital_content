---
title: "Baking with DAL"
weight: 9
type: docs
summary: Run, verify, and recover the TezBake Data Availability Layer node.
---

## DAL Status Checks

Start with TezBake's DAL status and version overview:

```bash
tezbake info --dal
tezbake version --all
```

You want the DAL node to be operational, the baker to have DAL profiles, and the Octez binaries to match the current TezBake package/protocol line.

If this is a new setup or you just restored DAL, register the baker's DAL attester profile:

```bash
tezbake update-dal-profiles --auto
tezbake info --dal
```

> **ℹ️ INFO:** Use your baker address for DAL profiles. In the recommended Ledger-manager plus TezSign-consensus setup, that is usually the manager address (`tz1`, `tz2`, or `tz3`), not the TezSign consensus key or companion key.

## Remote DAL Recovery After Node Rebootstrap

Use this when your L1 node was rebootstrapped and a remote DAL node stays dead, auto-restarting, or never returns to operational status.

Run the wrapper checks first:

```bash
tezbake info --dal
tezbake version --all
```

If remote DAL is still dead or repeatedly auto-restarting, stop repeating `tezbake upgrade --dal` and inspect the remote unit journal:

```bash
ssh <remote-user>@<remote-dal-host>
sudo journalctl -u bb-default-dal-xtz-dal.service -n 160 --no-pager -l
```

This recovery path is for errors like:

```text
Did not find service: GET http://127.0.0.1:8732/.../context/dal/skip_list_cells_of_level
```

If `tezbake version --all` shows the node and DAL versions both match the current package/protocol line, reset only the DAL store:

```bash
sudo systemctl stop bb-default-dal-xtz-dal.service
sudo systemctl show bb-default-dal-xtz-dal.service -p ExecStart
sudo mv /bake-buddy/dal/data /bake-buddy/dal/data.bak.$(date +%Y%m%d%H%M%S)
exit
tezbake upgrade --dal
tezbake update-dal-profiles --auto
tezbake info --dal
```

The `ExecStart` output tells you which endpoint the DAL node is actually using. Keep it with your incident notes.

> **⚠️ WARNING:** Do not keep resetting the DAL store if `/bake-buddy/dal/data` is recreated and the same error returns. At that point, verify what RPC is actually listening at the DAL node's `--endpoint`, usually `127.0.0.1:8732`, before making more destructive changes:
>
> ```bash
> curl http://127.0.0.1:8732/version
> curl http://127.0.0.1:8732/chains/main/blocks/head/header
> ```

After DAL is operational again, check your baker externally at:

```text
https://tezos.systems/YOUR_TEZOS_DOMAIN_OR_ADDRESS
```

## Companion Key Check

If you use a tz4/BLS consensus key, confirm the DAL companion key is loaded by the baker:

```bash
tezbake node show configuration.additional_key_aliases
tezbake info --dal
```

For the recommended TezSign setup where the active consensus key is imported as `baker`, only `companion` needs to be listed as an additional alias:

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
tezbake upgrade
```

If your TezSign consensus key is imported as `consensus` instead of `baker`, include both aliases:

```bash
tezbake node modify --set configuration.additional_key_aliases '["consensus","companion"]'
tezbake upgrade
```

## Related Guides

- [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Standard mainnet setup with DAL
- [Baking on Testnets](/tezbake/tutorials/baking-on-testnets/) - Testnet setup with DAL
- [Baking with Prism](/tezbake/tutorials/baking-with-prism/) - Run node, DAL, and signer across separate hosts
- [Baking Without DAL](/tezbake/tutorials/baking-without-dal/) - Exception path when you intentionally disable DAL
- [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Debug DAL and baker issues

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
