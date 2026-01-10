---
title: "> Setup"
weight: 1
type: docs
summary: Configure TezWatch Discord bot for baker monitoring notifications
---

## Preparation

Setting up TezWatch currently requires you to have a <https://Discord.com> account. If you don't have one, please create one before proceeding.

You will also need to join our [Discord](https://discord.gg/cVGMA4MaNM). This is where you will configure TezWatch. You can also ask questions and get support from the Tez Capital team and other Tezos bakers, stakers and delegators. After you join, make sure to self-select the "TezBake" role in order to get access to the [#tezwatch](https://discord.gg/94dnM2AcRw) channel.

The idea here is to have the Discord mobile app on your phone and/or the Discord desktop app on your computer. You will need to be logged in to your Discord account on the device you want to receive notifications on. TezWatch will use Discord to tag you in a DM (Direct Message) based on your configuration. For example, you may not care if you miss a single attestation but you care if you miss 10 attestation in a row or a block.

> **💡 TIP: Privacy Option:**
>
> If you want to remain anonymous, you can create a new Discord account and join our Discord server using that account. Then you can DM the TezWatch bot from that account and chat with it in private.

---

## Setup and configuration of baker performance monitoring

The whole setup and configuration process currently takes very little time as the features are limited. We are working on adding more features and more platforms. If you have any suggestions, please let us know on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital).

The setup is done using slash commands, which means just type the `/` character in the chat in the #tezwatch channel and you will see a list of available commands for the TezWatch bot.

| ![Navigate to #tezwatch channel and discover all slash commands](/tezwatch/tutorial/tezwatchSlashCommands.png) |
|-|

To get all "sources" and "events" use the `/events` command

| ![Slash Events](/tezwatch/tutorial/tezwatchSlashEvents.png) |
|-|

Here are the descriptions of all the key variables you will need to configure to monitor baker performance:

| Variable    | Available Options                          | Description                                                                                                                                                   |
|-------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `source`    | `baking` or `chain`  | The general area of interest. `baking` is related to activities that validate on the chain and `chain` is related to general chain progression               |
| `event`     | `attested-block`, `baked-block`, `missed-block`, `missed-attestation`, `new-block` | These are the specific staking events that you're interested in being notified for. Each event has its own unique subscription tier. All `missed*` ones are TIER 0 or FREE |
| `conditions`| `tz1UGkfyrT9yBt6U5PV7Qeui3pt3a8jffoWv,tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur` | This is a comma-separated list of Tezos addresses that you want to be notified for                                                                            |

> **ℹ️ INFO - Premium Features:**
>
> Some TezWatch features are considered premium and will in the future require a subscription. The freemium features indicated as TIER 0 will always be available for free to the Tezos ecosystem.

### Example

Here's an example command to subscribe to notifications for a missed attestation or missed block for a specific baker:

| ![Example TezWatch slash command](/tezwatch/tutorial/tezwatchSlashCommandExample.png) |
|-|

---

## Related Guides

* [Monitoring Transactions](/tezwatch/tutorials/monitoring-transactions/) - Track balances and transfers
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - CLI-based monitoring
* [Missing Attestations](/tezbake/tutorials/missing-attestations/) - Understanding missed attestations

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
