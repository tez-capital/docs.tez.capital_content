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

**Critical step** - physically ensure the primary cannot sign:

- **TezSign**: Unplug the USB cable from the primary computer
- **Ledger**: Unplug from primary and close the Baking app

If you cannot access the primary machine physically, wait at least 2-3 blocks (~12-18 seconds at 6-second block times) after the last known signing before proceeding.

### Step 4: Note Current Block Level

Check the current block level on a block explorer:

```bash
# Or use your backup node if synced
tezbake info
```

Record this level - you'll use it for the High Watermark (HWM).

### Step 5: Setup Backup Signer

Connect your backup signer to the backup computer:

**For TezSign:**

If the backup SD card is newly flashed, restore the `tezsign` folder from the `data` partition backup before using it. See [Back Up and Restore TezSign Data](/tezsign/tutorials/back-up-and-restore-data/).

```bash
# Initialize if not already done
tezbake setup-tezsign --init --platform

# Set HWM for each TezSign signing alias
tezbake tezsign advanced set-level consensus <current_level+10>
tezbake tezsign advanced set-level companion <current_level+10>

# The device should already have keys from initial setup
# Verify it's detected
tezbake info
```

Replace `consensus` and `companion` if your TezSign device uses different key aliases. These are TezSign device aliases, not necessarily the local Octez aliases loaded by the baker.

**For Ledger:**
```bash
# Import with HWM set to current block level + 10
tezbake setup-ledger --platform --import-key --authorize --hwm <current_level+10>
```

> **ℹ️ HWM Safety Margin**: Setting HWM slightly above current level ensures your backup won't sign any block the primary might have signed. For TezSign, set the level for every signing alias, commonly `consensus` and `companion`.

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

You should see attestation and baking activity. Check block explorers to confirm your baker is producing blocks and attestations.

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

When the repaired machine becomes your backup:

```bash
# Set TezSign HWM to current level when you eventually need to use it
tezbake tezsign advanced set-level consensus <current_level>
tezbake tezsign advanced set-level companion <current_level>

# For Ledger, import or re-authorize with the current-level HWM
# tezbake setup-ledger --platform --import-key --authorize --hwm <current_level>

# Don't pre-authorize - wait until needed
```

## Emergency: Cannot Access Primary

If your primary is completely unreachable (fire, theft, hardware death):

1. **Wait 2-3 minutes** - Ensure any queued signatures complete
2. Check block explorer - Note the last block your baker signed
3. Set backup HWM to that level + 10
4. Proceed with failover steps 5-7

## Common Mistakes to Avoid

| Mistake | Consequence |
|---------|-------------|
| Starting backup before stopping primary | Double baking → Slashing |
| Not updating HWM | Potentially signing at same level → Slashing |
| Pre-authorizing backup signer | Risk of accidental double baking |
| Rushing the process | Errors lead to slashing |

## Failover Checklist

- [ ] Primary confirmed down or stopped
- [ ] Primary signer physically disconnected
- [ ] Current block level noted
- [ ] Backup signer connected
- [ ] HWM set appropriately
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
