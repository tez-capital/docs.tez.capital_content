---
title: "Migrating from Kiln"
weight: 15
type: docs
summary: Step-by-step guide for Kiln users migrating to TezBake, covering Ledger setup, node bootstrap, and post-migration verification
---

# Migrating from Kiln to TezBake

Kiln is being sunset. If you're currently baking with Kiln, **TezBake** by [Tez Capital](https://tez.capital) is the recommended migration path. It's a modern, actively maintained baking tool that manages your entire stack — node, baker, signer, DAL — under one CLI.

This guide walks you through the migration step by step.

> **TezBake runs on Linux and macOS** (Ubuntu 22.04+, Debian 12+, or macOS with Apple Silicon / Intel). Linux is recommended for production bakers, but macOS is fully supported.

---

## Prerequisites

- **Linux or macOS machine** — Ubuntu 22.04+, Debian 12+, or macOS (dedicated hardware recommended for production)
- **Ledger** — Nano S Plus or Nano X (the same one you used with Kiln)
- **Your baker address** — the `tz1`/`tz2`/`tz3` address you're currently baking with
- **Root/sudo access** on the machine

### Hardware Minimums

| Component | Minimum |
|-----------|---------|
| CPU | 3 cores (arm64 or x86-64) |
| RAM | 8GB + 8GB swap (or 16GB RAM) |
| Storage | 100GB SSD |
| Network | Reliable broadband, low latency |

---

## Pre-Migration Checklist

Before touching anything, collect this information from your running Kiln setup:

- [ ] **Baker address** (`tz1...`, `tz2...`, or `tz3...`)
- [ ] **Key type** (ed25519/secp256k1/p256) and how it's stored (Ledger, software wallet)
- [ ] **Current cycle** — check on [TzKT](https://tzkt.io) or [TzStats](https://tzstats.com) to confirm your baker is active
- [ ] **Pending nonce revelations** — if you have unrevealed nonces from the current cycle, wait for them to be revealed before migrating (or use a snapshot ≥5 days old during bootstrap)

> ⚠️ **Never run two bakers with the same key at the same time.** This causes double baking/attestation, which results in **slashing** (loss of funds). Always stop Kiln completely before starting TezBake.

---

## Step-by-Step Migration

### 1. Note Your Baker Info

From your Kiln interface or config, record:
- Your baker's `tz` address
- Whether you're using a Ledger or software key
- Your **Ledger derivation path** — this is critical. Kiln typically uses `ed25519/0h/0h` but yours may differ. Check your Kiln config or logs for the exact path. Using the wrong derivation path in TezBake will import a **different key** and your baker won't work.

> **💡 How to find your derivation path in Kiln:** Check your Kiln configuration file or look for the `--ledger` flag in your running baker process (`ps aux | grep octez-baker`). The derivation path appears after the Ledger URI, e.g. `ledger://.../<curve>/<path>`.

### 2. Stop Kiln

**Linux:**
```bash
# If running as a systemd service
sudo systemctl stop kiln

# Or if using the Kiln CLI
kiln stop
```

**macOS (if migrating to a separate Linux machine):**
```bash
launchctl stop tezos.kiln
```

Confirm Kiln is fully stopped — no `octez-node` or `octez-baker` processes should be running:
```bash
ps aux | grep -E 'octez-node|octez-baker' | grep -v grep
```

This should return nothing.

### 3. Install TezBake

On your Linux machine:

```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
```

Verify installation:
```bash
tezbake version
```

### 4. Setup TezBake

Run the setup with DAL support (recommended for all bakers):

```bash
sudo tezbake setup --with-dal
```

This installs and configures:
- `octez-node`
- `octez-baker`
- `octez-dal-node`
- `octez-signer` (if using remote signer)

### 5. Configure Your Ledger

1. Connect your Ledger to the Linux machine via USB
2. Open the **Tezos Baking** app on the Ledger
3. Import your Ledger key:

The command varies depending on your key type. Use the derivation path you recorded in Step 1:

**tz1 (ed25519) — most common with Kiln:**
```bash
sudo tezbake setup-ledger --platform --import-key="ed25519/0h/0h" --authorize --hwm 1
```

**tz3 (P-256 / NIST):**
```bash
sudo tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --hwm 1
```

**tz2 (secp256k1):**
```bash
sudo tezbake setup-ledger --platform --import-key="secp256k1/0h/0h" --authorize --hwm 1
```

> **💡** The `0h/0h` part is the default derivation path. If your Kiln setup used a custom path (e.g. `ed25519/1h/0h`), substitute it here. The derivation path **must** match exactly or you'll import a different key.

Confirm on the Ledger screen when prompted to authorize the key for baking.

> ⚠️ **Verify the imported key matches your baker.** After setup, check the address:
> ```bash
> sudo tezbake info --signer
> ```
> The `tz` address shown must match your baker address exactly. If it doesn't, re-run `setup-ledger` with the correct derivation path.

> **Note:** You'll need the **Tezos Wallet** app (not Baking) for one-time operations like registration. Switch back to the **Tezos Baking** app for ongoing baking.

> **Looking ahead:** Once you're stable on TezBake with your Ledger, we recommend migrating to a **TezSign** hardware signer with **BLS/tz4 keys**. TezSign is a purpose-built signing device (~$20–30 in hardware) that outperforms Ledger for baking and supports the tz4 key type required by modern protocol features like DAL companion keys. This is a separate migration you can plan after you're settled on TezBake — see the [TezSign documentation](https://github.com/tez-capital/tezsign) for details.

### 6. Bootstrap the Node

Rather than syncing from scratch, bootstrap from a snapshot:

```bash
sudo tezbake bootstrap-node https://snapshots.eu.tzinit.org/mainnet/rolling
```

Regional mirrors are available — replace `eu` with `us` or `asia` for faster downloads depending on your location.

> **Tip:** If you have unrevealed nonces, use a snapshot that's at least 5–6 days old to avoid nonce revelation issues.

Bootstrap takes anywhere from 10 minutes to an hour+ depending on your hardware and network speed.

### 7. Start Baking

```bash
sudo tezbake start
```

This starts the node, baker, DAL node, and signer as managed systemd services.

### 8. Register (If Needed)

If your baker has been **inactive for more than 3 days**, you may need to re-register:

```bash
sudo tezbake register-key
```

If your baker is already active and was only briefly offline during migration, registration is **not** required.

> **Ledger users:** Registration requires the **Tezos Wallet** app. Switch back to **Tezos Baking** app after registration.

---

## Post-Migration Verification

Run through each of these to confirm everything is working:

```bash
# Overall status
sudo tezbake info

# Signer status
sudo tezbake info --signer

# DAL status
sudo tezbake info --dal

# Node logs (watch for sync progress)
sudo tezbake node log -f

# Baker logs (watch for attestations/baking)
sudo tezbake node log baker -f
```

### Checklist

- [ ] `tezbake info` shows node as **synced**
- [ ] `tezbake info --signer` shows signer **connected** and key loaded
- [ ] `tezbake info --dal` shows DAL node **running**
- [ ] Baker is producing attestations (check logs or [TzKT](https://tzkt.io))
- [ ] No missed slots in the first few cycles after migration
- [ ] Your baker appears active on [tezos.systems](https://tezos.systems)

---

## Troubleshooting

### Node won't sync
```bash
# Check node logs for errors
sudo tezbake node log -f

# If stuck, try re-bootstrapping
sudo tezbake stop
sudo tezbake bootstrap-node https://snapshots.us.tzinit.org/mainnet/rolling
sudo tezbake start
```

### Ledger not connecting
- Ensure Ledger is unlocked and **Tezos Baking** app is open
- Check USB connection — try a different port or cable
- Make sure no other application (Ledger Live, etc.) is using the device
- Verify with `sudo tezbake info --signer`

### Baker not producing attestations
- Confirm the node is fully synced first (`tezbake info`)
- Check that your baker key is properly imported
- Verify on [TzKT](https://tzkt.io) that your baker has upcoming rights
- If recently migrated, rights may take 2+ cycles to appear

### "Already baking" or double-baking warnings
- **Immediately stop** one of the bakers
- Confirm Kiln is completely stopped on all machines
- Never run the same key on two bakers simultaneously

### DAL issues
```bash
# Check DAL profiles
sudo tezbake info --dal

# Update DAL profiles if needed
sudo tezbake update-dal-profiles --auto
sudo tezbake stop && sudo tezbake start
```

---

## File Locations

TezBake stores everything under `/bake-buddy/`:

| Component | Path |
|-----------|------|
| Node data | `/bake-buddy/node/` |
| Signer data | `/bake-buddy/signer/` |
| DAL data | `/bake-buddy/dal/` |

---

## Useful Commands Reference

| Command | Description |
|---------|-------------|
| `tezbake info` | Overall status |
| `tezbake start` | Start all services |
| `tezbake stop` | Stop all services |
| `tezbake upgrade` | Update all components |
| `tezbake node log -f` | Follow node logs |
| `tezbake node log baker -f` | Follow baker logs |
| `tezbake version --all` | Show all component versions |
| `tezbake register-key` | Register as baker |

---

## Further Resources

- **Tez Capital Docs:** [docs.tez.capital](https://docs.tez.capital)
- **TezPeak** (monitoring dashboard): Included with TezBake — access via browser after setup
- **TezPay** (automated reward distribution): [github.com/tez-capital/tezpay](https://github.com/tez-capital/tezpay)
- **TezGov** (governance & staking): [gov.tez.capital](https://gov.tez.capital)
- **Community Support:** [Tez Capital Discord](https://discord.gg/tezcapital)
- **Octez Documentation:** [octez.tezos.com](https://octez.tezos.com/docs/introduction/tezos.html)
