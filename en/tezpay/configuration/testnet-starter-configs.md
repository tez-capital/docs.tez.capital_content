---
title: "Testnet Starter Configs"
weight: 1
type: docs
summary: Ready TezPay starter configuration files for Bakingnet and Ushuaianet
---

Use these examples as `config.hjson` for TezPay testnet payouts. Replace the baker address first, then adjust the payout fee, minimum payout amount, and delegator minimum balance for your own policy.

The examples use `payouts.wallet_mode: local-private-key`, which expects a matching `payout_wallet_private.key` file in the same TezPay folder. If you use a remote signer, change `payouts.wallet_mode` to `remote-signer` and provide `remote_signer.hjson`.

> **⚠️ WARNING: Keep Testnet Keys Separate**
>
> Do not reuse mainnet payout keys, signer files, TezSign cards, Ledger devices, or funded baker wallets for testnet payout testing. Use separate testnet-only key material and a payout wallet that only holds test tez.

## Choose a Testnet

| Network | Best for | Trade-off |
|---------|----------|-----------|
| **Bakingnet** | Long-running, production-style payout testing | Slower feedback than short-cycle testnets |
| **Ushuaianet** | Fast setup, registration, activation, and payout tests | Short cycles are less representative of a steady production baker |

> **💡 TIP:** Use Ushuaianet when you need a quick end-to-end payout rehearsal. Use Bakingnet when you want a longer-running testnet setup that behaves more like an operational baker.

## Before You Use a Config

- [ ] Replace `baker` with your testnet baker address.
- [ ] Confirm `payouts.wallet_mode` matches the wallet file you plan to use.
- [ ] Put `payout_wallet_private.key` or `remote_signer.hjson` in the same TezPay folder as `config.hjson`.
- [ ] Fund the payout wallet with test tez before trying a testnet payout.
- [ ] Run a dry-run first: `./tezpay generate-payouts` for standalone TezPay, or `tezbake pay generate-payouts` for TezBake integration.

> **ℹ️ INFO:** The `rpc_pool`, `tzkt_url`, and `explorer` values below are network-specific. Copy the whole block for the network you are testing rather than mixing endpoints between testnets.

## Bakingnet

Bakingnet is the recommended long-running baker testnet for most production-style testing. Current network details are listed on [Teztnets](https://teztnets.com/bakingnet-about).

```hjson
{
  tezpay_config_version: 0

  baker: "tz1-your-bakingnet-baker-address"

  payouts: {
    wallet_mode: local-private-key
    fee: 0.05
    baker_pays_transaction_fee: true
    minimum_payout_amount: 0.01
  }

  delegators: {
    requirements: {
      minimum_balance: 0.5
    }
  }

  network: {
    rpc_pool: [
      "https://rpc.bakingnet.teztnets.com"
    ]
    tzkt_url: "https://api.bakingnet.tzkt.io/"
    explorer: "https://bakingnet.tzkt.io/"
  }

  overdelegation: {
    protect: true
  }
}
```

## Ushuaianet

Ushuaianet has short cycles and is useful for rapid setup, registration, activation, and payout testing. Current network details are listed on [Teztnets](https://teztnets.com/ushuaianet-about).

> **💡 TIP:** Ushuaianet is the quickest path for checking that TezBake registration, TezPay configuration, and payout execution all line up before you repeat the process on a slower network.

```hjson
{
  tezpay_config_version: 0

  baker: "tz1-your-ushuaianet-baker-address"

  payouts: {
    wallet_mode: local-private-key
    fee: 0.05
    baker_pays_transaction_fee: true
    minimum_payout_amount: 0.01
  }

  delegators: {
    requirements: {
      minimum_balance: 0.5
    }
  }

  network: {
    rpc_pool: [
      "https://rpc.ushuaianet.teztnets.com"
    ]
    tzkt_url: "https://api.ushuaianet.tzkt.io/"
    explorer: "https://ushuaianet.tzkt.io/"
  }

  overdelegation: {
    protect: true
  }
}
```

## After Saving `config.hjson`

Run a dry-run before sending any payments. Use the command that matches how TezPay is installed.

Standalone TezPay:

```bash
./tezpay generate-payouts
```

TezBake integration:

```bash
tezbake pay generate-payouts
```

If the dry-run output looks correct, send a small testnet payout before relying on continual mode.

Standalone TezPay:

```bash
./tezpay pay
```

TezBake integration:

```bash
tezbake pay pay
```

## Related Configs

For mainnet or advanced options, use the upstream TezPay examples:

* [Starter configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.starter.hjson)
* [Advanced sample configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.sample.hjson)
