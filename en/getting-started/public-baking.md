---
title: "Tezos Public Baking"
weight: 1
type: docs
summary: How to Setup a Public Baker on Tezos
---

## Pubic Baking
Public baking and private baking are the same thing from the perspective of the blockchain. The only difference is that a public baker has announced themselves to the Tezos community and is willing to accept delegations. A private baker is not willing to accept delegations from delegators. A private baker cannot stop these delegations from occuring but a private baker is not expected to pay out rewards to delegators.

On Tezos, bakers (a.k.a. delegates) are paid directly by the blockchain for themselves as well as for their delegators. It's up to each and every baker to determine their own fee structure and payment policies. It's also up to each baker as to which payment software they will use to distribute rewards to delegators. There are several different payment software options available to bakers. Some bakers use their own custom software, some bakers use a third party payment software, and some bakers use a combination of both. We recommend using our TezPay payment software. TezPay allows you to fully automate the distribution of rewards to delegators.

With the recent introduction of [Adaptive Issuance](https://research-development.nomadic-labs.com/adaptive-issuance-paris.html#the-new-staker-role) on the Tezos protocol, bakers are now able to accept stakers. Stakers are different from delegators in that:
* Staker tez is frozen for up to 12 days.
* Stakers are liable for any losses incurred by the baker due to double baking or double endorsing. While rare, this is a risk that stakers take on when they stake their tez with a baker.
* Stakers are paid directly by the protocol without any need for the baker to pay them, unlike with delegators

A public baker has to contact two entities within the Tezos ecosystem to be added to the list of public bakers within each of their ecosystems. The entities in question all have their own methods to determine your public baker details, such as your fee and payment policies, via self-reporting. You will be asked to self-report your details to each of the following entities:
* https://tzstats.com (BlockWatch)
  * The best place to contact them is: tzstats@blockwatch.cc
* https://tzkt.io / https://baking-bad.org (Baking Bad)
  * The best place to contact them is: https://t.me/baking_bad_chat 

Most wallets and services on Tezos pull their baker information from one of these sources.

You also have the option of contacting the folks at Ledger directly in order to be listed as a baker on their Ledger Live wallet. You can contact them via the following link: https://support.ledger.com/hc/en-us/requests/new


---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 