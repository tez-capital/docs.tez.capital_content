---
title: "Testnet Starter Configs"
weight: 1
type: docs
summary: Ready TezPay starter configuration files for Bakingnet and Ushuaianet
---

Use these examples as `config.hjson` for TezPay testnet payouts. Replace the baker address first, then adjust the payout fee, minimum payout amount, and delegator minimum balance for your own policy.

The examples use `payouts.wallet_mode: local-private-key`, which expects a matching `payout_wallet_private.key` file in the same TezPay folder. If you use a remote signer, change `payouts.wallet_mode` to `remote-signer` and provide `remote_signer.hjson`.

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

## Related Configs

For mainnet or advanced options, use the upstream TezPay examples:

* [Starter configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.starter.hjson)
* [Advanced sample configuration](https://github.com/tez-capital/tezpay/blob/main/docs/configuration/config.sample.hjson)
