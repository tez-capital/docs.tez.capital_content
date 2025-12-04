---
title: "> TRD to TezPay Migration"
weight: 2
type: docs
summary: Migrating from TzKT Reward Distributor to TezPay
---

## Command Cheatsheet

| Task                               | Command Example                                      |
| ---------------------------------- | ---------------------------------------------------- |
| Import TRD config                  | `./tezpay import-configuration trd ./trd.yaml`       |
| Test configuration (no payout)     | `./tezpay generate-payouts`                          |
| Manual payout                      | `./tezpay pay` or `./tezpay pay -c <cycle>`          |
| Start continual payouts            | `./tezpay continual`                                 |

---

## Overview

If you're switching from TRD (Tezos Reward Distributor) to TezPay, here's what you need to know:

- **TezPay is folder-scoped.** Each TezPay instance lives in its own directory and serves one baker address.
- The folder holds the binary, configuration (`config.hjson`), and reports.
- TezPay has an import tool that can convert a standard `trd.yaml` into a working `config.hjson`.

> **Note:**  
> This is just a quick start guide. For more comprehensive and detailed information about TezPay, you may want to check other guides and the command reference.

---

## Migration Steps

### Step 1: Create a TezPay Instance Directory

Choose a directory to store your TezPay instance.

> **Tip:**  
> Use one folder per baker address, e.g., `/tezos/payouts/my-baker`.

This directory will contain:

- `tezpay` binary  
- `config.hjson`  
- (optional) `reports/` directory with payout history

---

### Step 2: Download TezPay Binary

Download the latest binary for your platform from the [Releases](https://github.com/tez-capital/tezpay/releases).

Rename it to `tezpay` and make it executable (on linux):

```bash
chmod +x ./tezpay
```

For Windows, the binary will be named `tezpay.exe`. To make it executable, no additional steps are required on Windows.

> **Note:**  
> Replacing the binary with the latest version is how you upgrade TezPay.

---

### Step 3: Choose Configuration Method

You have **two options**:

#### Option A: Migrate from TRD

1. Copy your `trd.yaml` into the instance folder.
2. Run:

```bash
./tezpay import-configuration trd ./trd.yaml
```

This creates `config.hjson` based on your TRD settings.

> **Note:**  
> Most standard TRD configs work out of the box. Edge cases might need adjustments.

#### Option B: Start Fresh

1. Create a file `config.hjson` in the instance folder.
2. Use this starter template:  
   [Starter Config](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.starter.hjson)
3. For advanced options, refer to:  
   [Sample Config](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.sample.hjson)

---

### Step 4: Test the Configuration

Run a dry run of the payouts:

```bash
./tezpay generate-payouts
```

> **This does not execute payouts.**  
> It only shows what TezPay would distribute.

---

### Step 5: Execute Payouts

#### Manual Payout

- Default (last completed cycle):

```bash
./tezpay pay
```

- Specific cycle:

```bash
./tezpay pay -c <cycle number>
```

#### Automatic (Continual) Payouts

To run payouts continuously:

```bash
./tezpay continual
```

> **Folder Location Tip:**  
> If you run TezPay as a service, place your payout folder **outside of `/home`** for reliable service access.  
> Example: `/tezos/payouts/my-baker`

---

## Need Help?

Reach out anytime:

- [Discord](https://discord.gg/cVGMA4MaNM)
- [Telegram](https://t.me/tezcapital)

---
