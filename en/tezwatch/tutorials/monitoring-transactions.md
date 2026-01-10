---
title: "Monitoring Transactions"
weight: 2
type: docs
summary: TezWatch Balances & Transactions Tutorial
---

## How to monitor Tezos balances and transactions with TezWatch

Using TezWatch you can keep an eye on transactions to and from baker wallets or baker payment wallets with flexibility.

You can monitor some of the following aspects:

- Transfers in/out in amounts greater than X but less than Y
- Notify when balance falls below X

> If you want to remain anonymous, you can create a new Discord account and join our Discord server using that account. Then you can DM the TezWatch bot from that account and what with it in private.

To get all "sources" and "events" use the `/events` command

| ![Slash Events](/tezwatch/tutorial/tezwatchSlashEvents.png) |
|-|

Here are the descriptions of all the key variables you will need to configure to monitor balances and transactions:

| Variable    | Available Options                          | Description                                                                                                                                                   |
|-------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `source`    | `wallet` | The general area of interest. `staking` is related to activities that validate on the chain and `chain` is related to general chain progression               |
| `event`     | `transfer`, `balance-updated` | These are the specific staking events that you're interested in being notified for. Each event has its own unique subscription tier. All `missed*` ones are TIER 0 or FREE |
| `conditions` for transfers | `tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv:in`, `tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv:in>1`, `tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv:50>in>1` | Be nofified for all incoming transactions, all incoming transactions over 1 or all transactions smaller than 1 but larger than 50 |
| `conditions` for balance-updated | `tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv:50>balance` | Be nofidied for all incoming transactions, all incoming transactions over X or all transactions smaller than X but larger than Y |

> Please note that some TezWatch features are considered premium and will in the future require a subscription. The freemium features indicated as TIER 0 will always be available for free to the Tezos ecosystem.

### Transfers Examples

Here's an example of subscribing to all incoming transactions to the wallet tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv

| ![Incoming transfer](/tezwatch/tutorial/tezwatchIncoming.png) |
|-|

Here's an example of subscribing to incoming transactions to the wallet tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv of over 1 tez

| ![Incoming transfer](/tezwatch/tutorial/tezwatchIncomingMore1.png) |
|-|

Here's an example of subscribing to incoming transactions to the wallet tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv of over 1 tez but less than 50 tez

| ![Incoming transfer](/tezwatch/tutorial/tezwatchIncomingMore1Less50.png) |
|-|

### Balances Example

Here's an example of subscribing to the wallet tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv's balance being below 50 tez.

| ![Incoming transfer](/tezwatch/tutorial/tezwatchBalance50.png) |
|-|

---

## Related Guides

* [TezWatch Setup](/tezwatch/tutorials/setup/) - Initial bot configuration
* [TezPay Notifications](/tezpay/tutorials/notifications/) - Payout alerts
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - CLI monitoring

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
