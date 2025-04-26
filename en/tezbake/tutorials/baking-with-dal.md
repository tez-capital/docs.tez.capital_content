---
title: "Baking with DAL"
weight: 1
type: docs
summary: TezBake Baking Tutorial for DAL
---

## Introduction

The Tezos Data Availability Layer (DAL) enhances network scalability by efficiently handling large volumes of data off-chain, providing essential support for rollups and other layer-2 solutions. This guide explains how to set up DAL locally alongside your TezBake baker.

For more details on DAL, see [Tezos DAL Overview](https://tezos.gitlab.io/shell/dal_overview.html).

> **Note:** This feature is experimental and not extensively tested yet. Remote/distributed setups will be available before the Rio activation.

---

## If You Previously Installed DAL Manually

If you previously set up DAL manually using the older method, first remove it by running:

```bash
sudo systemctl stop octez-dal-node
export DAL_USER="$(grep -i '^User=' /etc/systemd/system/octez-dal-node.service | cut -d= -f2)"
sudo rm -r "/home/$DAL_USER/.zcash-params"
sudo rm -r "/home/$DAL_USER/.tezos-dal-node"
sudo rm /usr/sbin/octez-dal-node
sudo rm /etc/systemd/system/octez-dal-node.service
sudo systemctl daemon-reload
```

Additionally, remove any DAL configurations manually added to your `app.json`, particularly the `BAKER_STARTUP_ARGS` referencing DAL.

---

## New TezBake Setup with DAL

Follow these steps if you're setting up a new TezBake baker with DAL:

1. **Install latest TezBake prerelease:**

```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh --prerelease
```

2. **Run setup with DAL integration:**

```bash
tezbake setup --with-dal
```

3. Proceed with your usual setup steps (ledger integration, baker registration, etc.).

4. Continue to the [After Setup](#after-setup) section.

---

## Existing TezBake Setup

If you already have TezBake running without DAL or if you've previously added DAL manually, follow these steps:

1. **Ensure no manual DAL references exist in your `app.json`** (remove if present):

- Specifically, remove any `BAKER_STARTUP_ARGS` referencing DAL.

2. **Install latest TezBake prerelease:**

```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh --prerelease
```

3. **Upgrade TezBake components:**

```bash
tezbake upgrade
```

> **Important (rare case):** If your node runs on a non-default RPC address, ensure your `RPC_ADDR` includes the full address (e.g., `http://`).

4. **Install DAL:**

```bash
tezbake setup --dal
```

5. Continue to [After Setup](#after-setup).

---

## After Setup

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

---

## Quick Troubleshooting

If you encounter issues or require immediate help, execute these commands to revert changes and reinstall:

```bash
tezbake remove --dal
tezbake setup --node  # choose 'yes' to merge config when prompted
tezbake stop && tezbake start
```

---

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

Any further questions or support requests? Contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital).
