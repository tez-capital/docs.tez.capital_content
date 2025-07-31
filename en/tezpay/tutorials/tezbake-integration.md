---
title: "> TezBake Pay Module Setup"
weight: 1
type: docs
summary: Integrating TezPay with TezBake
---

## Command Cheatsheet

| Task                           | Command                               |
| ------------------------------ | ------------------------------------- |
| Install TezPay Module          | `tezbake setup --pay`                 |
| Verify Installation            | `tezbake apps`                        |
| Generate Payouts               | `tezbake pay generate-payouts`        |
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

---

## Installation

Install the TezPay module:

`tezbake setup --pay`

Verify the installation:

`tezbake apps`

Ensure the output includes:
``` 
pay    │ true
```

---

## Migration for Existing TezPay Users

Migrating from standalone TezPay is straightforward:

- Copy your existing configuration (`config.hjson`), your private key (`payout_wallet_private.key`) or remote signer (`remote_signer.hjson`) files and `reports` directory into:

``` 
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

```
edsk...yourprivatekeyhere...
```

> **Important:**  
> **Use a dedicated wallet with minimal funds for payouts only, never your main baker wallet.**

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

> **Note:** This applies only when continual payouts are enabled. Refer to the section above for instructions on enabling continual payouts.

- **Start TezPay:**
  - direct: `tezbake pay start`
  - combined: `tezbake start --pay`

- **Stop TezPay:**
  - direct: `tezbake pay stop`
  - combined: `tezbake stop --pay`

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

## Support & Questions

Need help or have questions?

- [Discord](https://discord.gg/cVGMA4MaNM)  
- [Telegram](https://t.me/tezcapital)
