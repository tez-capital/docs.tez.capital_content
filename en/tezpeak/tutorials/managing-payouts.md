---
title: "Managing Payouts"
weight: 2
type: docs
summary: How to use TezPay with the TezPeak GUI
---

## Prerequisites

For this tutorial, you'll need to have already followed:

* [Generate payout soft wallet](/tezpay/tutorials/setup/#preparation-step-3---payout-wallet-optional)
* [Create TezPay configuration file](/tezpay/tutorials/setup/#setup-step-1a-configuration-file-simple)
  * [Default configuration](/tezpay/configuration/examples/default/)
  * [Starter/Minimal configuration](/tezpay/configuration/examples/starter/)
  * [All configuration options](/tezpay/configuration/examples/sample/)
* [Download TezBake](/tezbake/tutorials/baking-on-mainnet/#download-and-install-tezbake) _(note: Installing node/baking services not necessary for this tutorial)_
* [Install ami-tezpay service](https://github.com/tez-capital/ami-tezpay)

---

## Installation

TezPeak supports using TezBake and TezPay simultaneously or by themselves. To run them at the same time, combine both configurations in the same file as shown here: [https://github.com/tez-capital/tezpeak](https://github.com/tez-capital/tezpeak)

### Download and install TezPeak via TezBake

```bash
tezbake setup --peak
```

> You don't need to install the TezBake node or baker services to use TezPay.

### Setup TezPeak configuration

```bash
cd /bake-buddy/peak/ && touch config.hjson
```

---

## Configuration Examples

### Minimal TezPay configuration

```hjson
{
    listen: 0.0.0.0:8733
    app_root: /bake-buddy
    modules: {
        tezpay: {
            payout_wallet: tz1X7U9XxVz6NDxL4DSZhijME61PW45bYUJE
        }
    }
}
```

### Full TezPay configuration

```hjson
{
    # Id to show in the header
    id: ""
    # Address to listen on
    listen: 127.0.0.1:8733
    app_root: /bake-buddy
    modules: {
        tezpay: {
            # can be null to disable tezpay package monitoring
            applications: {
                # path to tezpay ami package
                # ⚠️ IMPORTANT: Use "pay" NOT "tezpay" when using TezBake integration!
                tezpay: pay
            }
            payout_wallet: tz1X7U9XxVz6NDxL4DSZhijME61PW45bYUJE
            payout_wallet_preferences: {
                balance_warning_threshold: 100
                balance_error_threshold: 50
            }
            # forces all operations to be dry run
            force_dry_run: true
        }
    }
    # List of reference nodes
    nodes: {
        "Tezos Foundation": {
            address: https://rpc.tzbeta.net/
            is_rights_provider: true
            is_block_provider: false
        }
        tzkt: {
            address: https://rpc.tzkt.io/mainnet/
            is_rights_provider: false
            is_block_provider: true
            is_essential: false
        }
    }
    # The mode tezpeak should operate in
    # auto - if bound to localhost, it will operate in private mode if not, it will operate in public mode
    # public - assumes public environment, only readonly operations are allowed
    # private - assumes private environment, all operations are allowed
    mode: auto
}
```

---

## Start TezPeak and connect to it

```bash
tezbake start --peak
```

If you're connecting from a different computer, navigate to `http://<your-baker-ip>:8733`.

If you're connecting from the same computer, use `http://127.0.0.1:8733` or `http://localhost:8733`.

## TezPeak example screenshot

![TezPeak example screenshot](/tezbake/tutorial/tezpeakexample.png)

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
