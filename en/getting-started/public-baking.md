---
title: "Tezos Public Baking"
weight: 1
type: docs
summary: How to Setup a Public Baker on Tezos
---

## Public Baking

Public baking and private baking are the same thing from the perspective of the blockchain. The only difference is that a public baker has announced themselves to the Tezos community and is willing to accept delegations. A private baker is not willing to accept delegations from delegators. A private baker cannot stop these delegations from occuring but a private baker is not expected to pay out rewards to delegators.

### What is Delegation?

When you delegate your tez to a baker, your tokens never leave your wallet - you retain full ownership and control. The baker gains increased baking power from your delegated tez and shares the baking rewards with you. On Tezos, bakers are paid directly by the blockchain for themselves as well as for their delegators. Each baker determines their own fee structure and payment policies. We recommend using TezPay payment software to automate reward distribution to delegators.

### What is Staking?

With the [Adaptive Issuance](https://research-development.nomadic-labs.com/adaptive-issuance-paris.html#the-new-staker-role) protocol upgrade, bakers can now accept stakers in addition to delegators. Staking differs from delegation in several important ways:

* **Funds are locked** - Staked tez is frozen for up to 12 days (cannot be moved during this period)
* **Higher rewards** - Stakers earn approximately 3x the rewards of delegators due to the additional commitment and risk
* **Slashing risk** - Stakers are liable for losses if the baker double bakes or double attests (though this is rare)
* **Direct protocol payment** - Stakers are paid directly by the protocol without baker intervention, unlike delegators who rely on the baker to distribute rewards
* **Greater baking power** - Staked tez counts as 1.0 toward the baker's capacity, while delegated tez counts as ≈0.33

A public baker has to contact two entities within the Tezos ecosystem to be added to the list of public bakers within each of their ecosystems. The entities in question all have their own methods to determine your public baker details, such as your fee and payment policies, via self-reporting. You will be asked to self-report your details to each of the following entities:

* <https://tzstats.com> (Trilitech)
  * The best place to contact them is: <tzstats@trili.tech>
* <https://tzkt.io> / <https://baking-bad.org> (Baking Bad)
  * The best places to contact them are: <https://t.me/baking_bad_chat> and <https://discord.gg/aG8XKuwsQd>

Most wallets and services on Tezos pull their baker information from one of these sources, mostly from TzKT. If you are not listed on TzKT, you will not be listed on most wallets and services on Tezos.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
