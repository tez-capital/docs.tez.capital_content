---
title: "Failover Procedure"
weight: 9
type: docs
summary: How to safely switch to your backup baker when your primary fails
---

## Safe Failover Procedure

When your primary baking setup fails, you need to switch to your backup quickly but safely. Rushing this process risks double baking, which results in slashing. This guide walks you through a safe failover.

> **🚨 CRITICAL: Never Run Two Bakers Simultaneously**
>
> Double baking occurs when two signers with the same key sign blocks at the same level. This results in slashing of your stake. Always ensure your primary is completely stopped before activating your backup.

## Before You Need Failover

Prepare these items in advance:

- [ ] Backup computer with TezBake installed (but not started)
- [ ] Backup TezSign device or Ledger (not authorized for baking)
- [ ] For TezSign: cloned SD card or restored `data/tezsign` folder ready for the backup device
- [ ] Document your baker's public key hash (tz1/tz2/tz3/tz4 address)
- [ ] Know your current block level (check [TzKT](https://tzkt.io))

## Failover Steps

### Step 1: Confirm Primary is Down

Before doing anything, verify your primary baker is truly unreachable:

```bash
# Try to connect to primary
ssh your-primary-baker

# If you can connect, check status
tezbake info
```

If the primary responds, troubleshoot it rather than failing over.

### Step 2: Stop Primary (If Accessible)

If you can still access your primary:

```bash
# On primary machine
tezbake stop

# Verify it's stopped
tezbake info
# Should show services as stopped
```

### Step 3: Disconnect Primary Signer

> **🚨 CRITICAL:** Physically ensure the primary cannot sign before activating the backup.

- **TezSign**: Unplug the USB cable from the primary computer
- **Ledger**: Unplug from primary and close the Baking app

If you cannot access the primary physically, use a verified remote shutdown or other isolation that prevents it from signing. An unreachable host may still be baking. Waiting a fixed number of blocks does not establish that it has stopped.

### Step 4: Note Current Block Level

Check the current block level on a block explorer:

```bash
# Or use your backup node if synced
tezbake info
```

Record the network and a fresh level if you plan to set HWM. For TezSign, this is an optional extra precaution; missing `level2.bin` alone does not make a restored key unusable.

### Step 5: Setup Backup Signer

Keep the backup computer's baking and signer services stopped while preparing the signer:

```bash
tezbake stop
tezbake info
```

Confirm they are stopped, then connect only the backup signer.

**For TezSign:**

If the backup SD card is newly flashed, restore the `tezsign` folder from the `data` partition backup before using it. See [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/).

```bash
tezbake tezsign status
tezbake tezsign unlock
tezbake tezsign status --full
```

Verify the expected public addresses and unlocked keys. Do not initialize a new master store or generate new keys to restore an existing signer. If you choose to set HWM manually, complete [High-Watermark Recovery](/tezsign/tutorials/high-watermark-recovery/) before Step 6; unlock is required first, and the target must exceed the existing counters. Use the device aliases, which may differ from local Octez aliases.

**For Ledger:**
```bash
# Import with HWM set to current block level + 10
tezbake setup-ledger --platform --import-key --authorize --hwm <current_level+10>
```

> **ℹ️ INFO: HWM Safety Margin:** Advancing HWM adds a guard against older signing requests and may delay signing until the chain advances. It does not prevent an active primary from signing new levels too. The primary must remain unable to sign.

### Step 6: Start Backup Baker

```bash
tezbake start

# Verify it's running
tezbake info

# Watch the logs
tezbake node log baker -f
```

### Step 7: Verify Baking Resumes

Monitor your baker for the next few blocks:

```bash
tezbake node log baker -f
```

You should see attestation and baking activity. Once the backup is running, [check Baker Signal on Tezos.Systems](/getting-started/baker-signal/#check-attestation-and-dal) for attestations at levels after the switch and for DAL participation as slots become eligible.

## After Failover

### Investigate Primary Failure

Once stable on backup, investigate what happened:

- Hardware failure?
- Network issues?
- Software crash?
- Power outage?

### Repair Primary

Fix the primary issue but **do not start baking** on it. Keep it as your new backup:

1. Stop all baking services: `tezbake stop`
2. Do not authorize the signer
3. Keep it ready for the next failover

### Update HWM on Repaired Primary

Keep the repaired signer offline. At the next actual switch, stop and isolate the then-active signer first. Use a fresh chain level and inspect the restored device's current counters if you choose the optional [TezSign HWM procedure](/tezsign/tutorials/high-watermark-recovery/). For Ledger, use the current-level HWM when authorizing it at failover time.

## Emergency: Cannot Access Primary

If your primary is completely unreachable (fire, theft, hardware death):

1. Establish that the primary cannot sign, through power-off, disconnection, or another verified isolation method. Network unreachability and silence on an explorer are insufficient by themselves.
2. If that cannot be established, contact the team to plan recovery rather than enabling a second copy of the same keys.
3. Once isolated, check a fresh chain level and continue with steps 4-7. Setting HWM or waiting a few minutes cannot make two active copies safe.

## Common Mistakes to Avoid

| Mistake | Consequence |
|---------|-------------|
| Starting backup before stopping primary | Double baking → Slashing |
| Treating HWM as protection against a still-active primary | Both copies can sign new levels |
| Pre-authorizing backup signer | Risk of accidental double baking |
| Rushing the process | Errors lead to slashing |

## Failover Checklist

- [ ] Primary verified unable to sign
- [ ] Primary signer physically disconnected
- [ ] Current block level noted
- [ ] Backup signer connected
- [ ] If manually setting TezSign HWM, unlock and level verification completed
- [ ] Backup baker started
- [ ] Attestations/blocks appearing in logs
- [ ] Block explorer confirms activity

---

## Related Guides

* [Best Practices](/getting-started/best-practices/) - Backup hardware recommendations
* [Slashing Explained](/getting-started/slashing-explained/) - Why double baking is dangerous
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Monitor your baker
* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - TezSign KB hub
* [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/) - Restore TezSign data to a fresh card

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
