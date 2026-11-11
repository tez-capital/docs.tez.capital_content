---
title: "High-Watermark Recovery"
weight: 6
type: docs
summary: Understand missing HWM files and optionally set TezSign levels during recovery
---

## When to Use This

Use this to understand missing or stale high-watermark (HWM) state, or when you choose to set an explicit starting level during failover, SD-card restore, or migration.

**You do not need to preserve `level2.bin` or manually set HWM for every normal migration.** Restore the whole `tezsign` folder, keep the original signer offline, and use one active baker with these keys. TezSign recreates missing HWM state on unlock and advances it with subsequent signatures. A backup made before first use can legitimately lack this file.

HWM records a separate level and round for blocks, preattestations, and attestations. TezSign rejects a request whose level/round does not advance that signing kind's watermark. The file is persisted state, but it is not a key file or a requirement for restoring the existing key identity.

Recreated state starts without the previous signing history. Copy existing state as part of the whole-folder backup when available; do not delete it to fix a signing error. A spare's state becomes stale if the original signs after the backup was made. Manually advancing HWM is an optional extra precaution before switching to that spare.

> **🚨 CRITICAL:** Only one baker may use these signing keys at a time. HWM settings do not coordinate two devices and do not make concurrent operation safe. Confirm the original cannot sign before starting the replacement; waiting a fixed number of blocks is not a substitute.

## Set Levels for Each Signing Alias

Use this procedure only if you choose to set levels manually. It updates HWM state without creating keys or changing their identities.

### 1. Stop Signing and Isolate the Original

Stop the local services before connecting the restored device, and keep every other baker using the same keys stopped. Disconnect the original signer.

```bash
tezbake stop
tezbake info
```

Confirm that the baker and signer services are stopped. This also prevents an automatic-unlock service from making the spare available before you finish checking it. Connect only the restored device; keep services stopped while running the management commands below.

### 2. Unlock and Inspect the Keys

```bash
tezbake tezsign status
tezbake tezsign unlock
tezbake tezsign status --full
```

Confirm the expected public addresses and `UNLOCKED` state. **Unlock is required before `set-level`.** A locked-key status report does not expose its persisted HWM, so zero counters while locked are not proof of lost state.

Record the block, preattestation, and attestation levels for every signing alias. The common device aliases are `consensus` and `companion`; these may differ from local Octez aliases such as `baker`.

### 3. Choose a Fresh Target Level

Read the current level from a trusted, synchronized RPC for the baker's network. For mainnet, this read-only example uses a public RPC because `tezbake stop` also stops the local node:

```bash
curl -fsS --max-time 15 https://rpc.tzkt.io/mainnet/chains/main/blocks/head/header
```

Check the response's network and timestamp as well as its `level`. For a testnet, use that testnet's RPC instead. Choose a target above the fresh chain level and all existing HWM levels for the keys being prepared; a margin such as `+10` is a common recovery choice. Use a fresh value at the time of the switch, not a number copied from a support conversation.

### 4. Set and Verify Each Alias

Replace `<target-level>` with the integer you chose, and replace the aliases if your device uses different names:

```bash
tezbake tezsign advanced set-level consensus <target-level>
tezbake tezsign advanced set-level companion <target-level>
tezbake tezsign status --full
```

For each alias, verify `UNLOCKED` and that all three HWM levels equal the target, with rounds reset to `0`. Current firmware requires a target strictly greater than each existing level. If a command fails, inspect the error and current state before proceeding; do not delete HWM files or try to lower the counters.

### 5. Resume or Store the Spare

For a spare-card test, disconnect and store it offline. For an actual failover, with the original still unable to sign:

```bash
tezbake start
tezbake info --signer
tezbake info
tezbake info --dal
```

Setting a target ahead of the chain can temporarily reject signing requests until the chain advances past that target. Check the signer/baker logs and verify resumed attestations; do not lower HWM to remove this expected delay.

Once the chain passes that level, [check Baker Signal on Tezos.Systems](/getting-started/baker-signal/#check-attestation-and-dal) for new attestations from the recovered mainnet baker.

## Failover Example

If a fresh chain check reports level `8000000` and all recorded counters are lower, `8000010` illustrates a target with a ten-level margin. This is an example only. Recalculate for the current chain and the actual device counters before running the commands above.

## Related Guides

- [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - Restore the TezSign data folder
- [Failover Procedure](/tezbake/tutorials/failover-procedure/) - Safe baker failover
- [Slashing Explained](/getting-started/slashing-explained/) - Why duplicate signing is dangerous

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
