---
title: "Setup Peak GUI"
weight: 3
type: docs
summary: How to Setup Using TezPay with the TezPeak GUI
---

## Preparation

For this tutorial, you'll need to have already followed the following tutorials:

* [Generate payout soft wallet](/tezpay/tutorials/how-to-setup/#preparation-step-3---payout-wallet-optional)
* [Create TezPay configuration file](https://docs.tez.capital/tezpay/tutorials/how-to-setup/#setup-step-1a-configuration-file-simple)
  * [Default configuration](https://docs.tez.capital/tezpay/configuration/examples/default/)
  * [Starter/Minimal configuration](https://docs.tez.capital/tezpay/configuration/examples/starter/)
  * [All configuration options](https://docs.tez.capital/tezpay/configuration/examples/sample/)
* [Download TezBake](/tezbake/tutorials/how-to-bake/#download-and-copy-tezbake) _(note: Installing node/baking services not necessary for this tutorial)_
* [Install ami-tezpay service](https://github.com/tez-capital/ami-tezpay)

Following the guides above will ensure you have the necessary tools and configurations to proceed with this tutorial.

---

## Installation

TezPeak GUI supports using TezBake and TezPay simultaneously or by themselves. To run them at the same time simply combine both configurations in the same file as shown here: [https://github.com/tez-capital/tezpeak](https://github.com/tez-capital/tezpeak)

> Scroll down to the bottom of the page for the full configuration example.

### Download and install TezPeak via TezBake

```bash
tezbake setup --peak
```

> You don't need to install the TezBake node or baker services to use TezPay.

### Setup TezPeak configuration

```bash
cd /bake-buddy/peak/ && touch config.hjson
```

Open the `config.hjson` file with your favorite text editor.

#### Sample TezPeak configuration

Here's an example of a minimal TezPeak `config.hjson` file with just TezPay configured:

```yaml
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

##### Full TezPeak configuration examples

Here's the TezPeak configuration with all available TezPay options:

```yaml
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
# path to tezpay ami package, either absolute or relative to parent directory peak
            # `pay` is the correct value to use when both tezpay and tezpeak are installed as modules within tezbake instance.
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
 
 # List of reference nodes to connect to
 # The reference nodes are used to get the rights and blocks if the baker's node is not available
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
     # reports error if node not available, use for baker's node
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

## Start TezPeak and connect to it

```bash
tezbake start --peak
```

If you're connecting to the TezPeak GUI from a different computer, you'll need to open a web browser and navigate to `http://<your-baker-ip>:8733`.

If you're connecting from the same computer, you can use `http://127.0.0.1:8733` or `http://localhost:8733`.

## TezPeak example screenshot

![TezPeak example screenshot](/tezbake/tutorial/tezpeakexample.png)

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
