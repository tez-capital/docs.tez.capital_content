---
title: "Tezos Public Baking"
weight: 2
type: docs
summary: How to Setup a Public Baker on Tezos
---

## Public Baking

Public baking and private baking are the same thing from the perspective of the blockchain. The only difference is that a public baker has announced themselves to the Tezos community and is willing to accept delegations. A private baker is not willing to accept delegations from delegators. A private baker cannot stop these delegations from occuring but a private baker is not expected to pay out rewards to delegators.

### What is Delegation?

When you delegate your tez to a baker, your tokens never leave your wallet - you retain full ownership and control. The baker gains increased baking power from your delegated tez and shares the baking rewards with you. On Tezos, bakers are paid directly by the blockchain for themselves as well as for their delegators. Each baker determines their own fee structure and payment policies. We recommend using TezPay payment software to automate reward distribution to delegators.

### What is Staking?

With the [Adaptive Issuance](https://research-development.nomadic-labs.com/adaptive-issuance-paris.html#the-new-staker-role) protocol upgrade, bakers can now accept stakers in addition to delegators. Staking differs from delegation in several important ways:

* **Funds are locked** - Staked tez is frozen and cannot be moved. Unstaking takes up to 4 days (3 cycles plus the remainder of the current cycle)
* **Higher rewards** - Stakers earn approximately 3x the rewards of delegators due to the additional commitment and risk
* **Slashing risk** - Stakers share in slashing penalties proportionally if the baker double bakes or double attests. See [Slashing Explained](/getting-started/slashing-explained/) for details
* **Direct protocol payment** - Stakers are paid directly by the protocol without baker intervention, unlike delegators who rely on the baker to distribute rewards
* **Greater baking power** - Staked tez counts as 1.0 toward the baker's capacity, while delegated tez counts as ≈0.33

### Configuring Staker Acceptance

Bakers control whether and how much external stake they accept via two parameters:

**`limit_of_staking_over_baking`** (default: 0, max: 9)
- Defines how much external stake you accept relative to your own stake
- Default of 0 means you reject all external stakers
- Setting to 5 means you accept up to 5x your own stake from stakers
- Example: If you stake 10,000 XTZ with limit=5, you can accept up to 50,000 XTZ from stakers

**`edge_of_baking_over_staking`** (default: 1, range: 0-1)
- Controls how staking rewards are split between you and your stakers
- Value of 1 (100%): You keep all rewards from your own stake (stakers get their proportional share)
- Value of 0: You share all rewards proportionally with stakers
- Most public bakers use the default of 1

Configure these via [TezGov](https://gov.tez.capital) or CLI. Changes take effect after 5 cycles (~5 days).

A public baker has to contact two entities within the Tezos ecosystem to be added to the list of public bakers within each of their ecosystems. The entities in question all have their own methods to determine your public baker details, such as your fee and payment policies, via self-reporting. You will be asked to self-report your details to each of the following entities:

* <https://tzstats.com> (Trilitech)
  * The best place to contact them is: <tzstats@trili.tech>
* <https://tzkt.io> / <https://baking-bad.org> (Baking Bad)
  * The best places to contact them are: <https://t.me/baking_bad_chat> and <https://discord.gg/aG8XKuwsQd>

Most wallets and services on Tezos pull their baker information from one of these sources, mostly from TzKT. If you are not listed on TzKT, you will not be listed on most wallets and services on Tezos.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
