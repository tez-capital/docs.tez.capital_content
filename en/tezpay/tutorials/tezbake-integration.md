---
title: "> TezBake Pay Module Setup"
weight: 2
type: docs
summary: Integrating TezPay with TezBake
---

## Command Cheatsheet

| Task                           | Command                               |
| ------------------------------ | ------------------------------------- |
| Install TezPay Module          | `tezbake setup --pay`                 |
| Upgrade TezPay Only            | `tezbake upgrade --pay`               |
| Verify Installation            | `tezbake apps`                        |
| Generate Payouts               | `tezbake pay generate-payouts`        |
| Pay Delegators                 | `tezbake pay pay`                     |
| Pay with Previous Cycles       | `tezbake pay pay --include-previous-cycles N` |
| View TezPay Logs               | `tezbake pay log` or `tezbake pay log -f` |
| Check Continual Payout Status  | `tezbake pay continual status`      |
| Enable Continual Payouts       | `tezbake pay continual enable`      |
| Disable Continual Payouts      | `tezbake pay continual disable`     |
| Start TezPay                   | `tezbake pay start` or `tezbake start --pay` |
| Stop TezPay                    | `tezbake pay stop` or `tezbake stop --pay` |
| TezPay Information             | `tezbake info --pay`                  |
| Remove TezPay Module           | `tezbake remove --pay`                |

---

## Overview

TezPay is integrated directly into TezBake as the `pay` module, simplifying your payout process.

**New Users:**
Please follow the steps below to create your configuration and wallet or remote signer files. Refer to the [TezPay Configuration Documentation](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/) for detailed setup instructions.

## Table of Contents

1. [Installation](#installation)
2. [Migration for Existing TezPay Users](#migration-for-existing-tezpay-users)
3. [Setup Guide for New Users](#setup-guide-for-new-users)
4. [Managing Payouts](#managing-payouts)
5. [Automatic (Continual) Payouts](#automatic-continual-payouts)
6. [Starting & Stopping TezPay](#starting--stopping-tezpay-continual-service)
7. [Removing the Pay Module](#removing-the-pay-module)

---

## Installation

Install the TezPay module:

`tezbake setup --pay`

Verify the installation:

`tezbake apps`

Ensure the output includes:

```bash
pay    │ true
```

---

## Migration for Existing TezPay Users

Migrating from standalone TezPay is straightforward:

- Copy your existing configuration (`config.hjson`), your private key (`payout_wallet_private.key`) or remote signer (`remote_signer.hjson`) files and `reports` directory into:

```bash
/bake-buddy/pay
```

## Setup Guide for New Users

New users should follow these steps for proper setup:

### Step 1: Creating the Configuration File (`config.hjson`)

Inside `/bake-buddy/pay`, create a new file called exactly `config.hjson`.

Start with the [Starter Configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.starter.hjson) and enhance it from the [Sample Configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.sample.hjson) if needed.

Minimal starter example:

```yaml
{
  baker_address: "tz1-your-baker-wallet-address",
  payout_fee: 0.05, // 5% fee
  delegator_pays_xfer_fee: true,
  min_payout: 1 // minimum payout 1 XTZ
}
```

### Step 2: Setting Up the Wallet or Remote Signer File

You have two options:

- **Private key file**, or
- **Remote signer file**.

**Option A: Private Key (`payout_wallet_private.key`):**

Create file named exactly `payout_wallet_private.key` inside `/bake-buddy/pay`.

Insert your **unencrypted** private key:

```bash
edsk...yourprivatekeyhere...
```

> **🚨 CRITICAL:**
>
> **Use a dedicated wallet with minimal funds for payouts only. NEVER use your main baker wallet.**

**Option B: Remote Signer (`remote_signer.hjson`):**

Create file named exactly `remote_signer.hjson` inside `/bake-buddy/pay`.

See [Remote Signer Sample](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/remote_signer.sample.hjson).

---

## Managing Payouts

Manually generate payouts - display only - no payout:

`tezbake pay generate-payouts`

Pay:

`tezbake pay pay`

Reports are stored in:

```bash
/bake-buddy/pay/reports
```

---

## Automatic (Continual) Payouts

Continual payouts are initially disabled.

- **Check current status:**  
  `tezbake pay continual status`

- **Enable continual payouts:**  
  `tezbake pay continual enable`

- **Disable continual payouts:**  
  `tezbake pay continual disable`

---

## Starting & Stopping TezPay (continual service)

> **ℹ️ INFO:** This applies only when continual payouts are enabled. Refer to the section above for instructions on enabling continual payouts.

- **Start TezPay:**
  - direct: `tezbake pay start`
  - combined: `tezbake start --pay`

- **Stop TezPay:**
  - direct: `tezbake pay stop`
  - combined: `tezbake stop --pay`

---

## Protocol Upgrade Handling

TezPay automatically pauses continual payouts when a Tezos protocol upgrade occurs. This is a safety feature to ensure payout accuracy under the new protocol.

### What Happens During Protocol Upgrades

1. TezPay detects the protocol change
2. Continual mode stops processing payments
3. The service remains running but inactive

### Restart Procedure After Protocol Upgrade

1. **Update TezPay to the latest version:**

   ```bash
   tezbake upgrade --pay
   ```

2. **Run a dry-run to verify payouts:**

   ```bash
   tezbake pay generate-payouts
   ```

3. **Check for any errors in the output**

4. **If satisfied, restart the service:**

   ```bash
   tezbake start --pay
   ```

> **Note:** The service may already be running but not processing. The start command ensures it resumes processing.

---

## Catching Up Missed Payments

If payments were missed (due to downtime, protocol upgrades, or errors), use the `--include-previous-cycles` flag:

```bash
# Check last 5 cycles for missed payments (dry run first)
tezbake pay generate-payouts --include-previous-cycles 5

# Actually pay missed cycles
tezbake pay pay --include-previous-cycles 5
```

This scans previous cycles for any payouts that weren't completed and includes them in the current batch.

---

## TezPay Information (continual service)

View detailed information:

`tezbake info --pay`

---

## Removing the Pay Module

To remove TezPay integration:

`tezbake remove --pay`

This does not delete configuration or reports.

---

## References and Advanced Configuration

Examples of configuration files:

- **Default:** [config.default.hjson](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.default.hjson)  
- **Starter:** [config.starter.hjson](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.starter.hjson)  
- **Advanced Sample:** [config.sample.hjson](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.sample.hjson)

Detailed configuration guide: [TezPay Configuration Documentation](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/)

---

## Related Guides

**TezPay:**

* [TezPay Setup](/tezpay/tutorials/setup/) - Initial TezPay setup
* [Paying Delegators](/tezpay/tutorials/paying-delegators/) - Manual payout guide
* [Notifications](/tezpay/tutorials/notifications/) - Set up payout alerts
* [Configuration Examples](/tezpay/configuration/examples/) - Sample configs

**TezBake:**

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Baker setup guide

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
