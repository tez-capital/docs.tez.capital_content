---
title: "Updating"
weight: 6
type: docs
summary: How to update TezPay
---

## Keeping TezPay Up-to-Date

Choose the update path that matches how TezPay is installed.

### TezBake Integrated TezPay

If TezPay was installed through TezBake with `tezbake setup --pay`, update the integrated pay module through TezBake:

```bash
tezbake upgrade --pay
```

### Standalone Linux CLI

For a standalone TezPay install, run the following command from within the folder where tezpay is located:

```bash
cd tezpay
wget -q https://pay.tez.capital/install -O /tmp/install.sh && sh /tmp/install.sh
```

---

## Related Guides

* [TezPay Setup](/tezpay/tutorials/setup/) - Initial configuration
* [TezBake Integration](/tezpay/tutorials/tezbake-integration/) - Use TezPay with TezBake
* [Paying Delegators](/tezpay/tutorials/paying-delegators/) - Run payouts

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
