---
title: "Tezos Public Baking"
weight: 2
type: docs
summary: Complete guide to running a public baker on Tezos
---

## Public Baking

Public baking and private baking are the same thing from the perspective of the blockchain. The only difference is that a public baker has announced themselves to the Tezos community and is willing to accept delegations. A private baker is not willing to accept delegations from delegators. A private baker cannot stop these delegations from occurring but a private baker is not expected to pay out rewards to delegators.

---

## Understanding Delegation

### What is Delegation?

When you delegate your tez to a baker, your tokens never leave your wallet - you retain full ownership and control. The baker gains increased baking power from your delegated tez and shares the baking rewards with you. On Tezos, bakers are paid directly by the blockchain for themselves as well as for their delegators. Each baker determines their own fee structure and payment policies.

### Why Delegators Choose Public Bakers

Delegators want:
- **Passive income** without running infrastructure
- **Liquidity** - funds never locked, can spend/transfer anytime
- **Zero slashing risk** - delegators cannot be slashed
- **Reliable payouts** - consistent, predictable reward distribution

### Delegation Capacity

The protocol enforces a **9x delegation limit** relative to your frozen stake:

| Your Frozen Stake | Maximum Delegations |
|-------------------|---------------------|
| 10,000 XTZ | 90,000 XTZ |
| 50,000 XTZ | 450,000 XTZ |
| 100,000 XTZ | 900,000 XTZ |

If you exceed this limit (overdelegation), excess delegators earn reduced or no rewards.

---

## Understanding Staking

With the [Adaptive Issuance](https://research-development.nomadic-labs.com/adaptive-issuance-paris.html#the-new-staker-role) protocol upgrade, bakers can now accept stakers in addition to delegators. Staking differs from delegation in several important ways:

| Aspect | Delegators | Stakers |
|--------|------------|---------|
| Funds locked | No | Yes (frozen) |
| Baking power | 0.33 per tez | 1.0 per tez |
| Rewards | ~1x (base rate) | ~3x delegator rate |
| Slashing risk | None | Yes (proportional) |
| Payment method | Baker distributes | Protocol pays directly |
| Unstaking time | Instant | ~3 days |

See [Slashing Explained](/getting-started/slashing-explained/) for details on slashing risks.

### Configuring Staker Acceptance

Bakers control whether and how much external stake they accept via two parameters:

**`limit_of_staking_over_baking`** (default: 0, max: 9)
- Defines how much external stake you accept relative to your own frozen stake
- Default of 0 means you reject all external stakers
- Setting to 5 means you accept up to 5x your own frozen stake from stakers
- Example: If you have 10,000 XTZ frozen stake with limit=5, you can accept up to 50,000 XTZ from stakers

> **💡 Understanding Capacity Limits**
>
> Bakers have two independent capacity limits:
> * **Staking limit**: Up to 9x your own frozen stake (controlled by `limit_of_staking_over_baking`)
> * **Delegation limit**: Up to 9x your own frozen stake (protocol-enforced, always active)
>
> These limits are **independent** - accepting stakers does not reduce your delegation capacity. A baker with 10,000 XTZ frozen stake could theoretically accept up to 90,000 XTZ in stakers AND 90,000 XTZ in delegations (if both limits are maxed).

**`edge_of_baking_over_staking`** (default: 1, range: 0-1)
- Your fee on staker rewards (similar to a delegation fee)
- Value of 1 (100%): You keep all staker rewards as fee (stakers receive nothing)
- Value of 0.1 (10%): You take 10% fee, stakers keep 90% of their proportional rewards
- Value of 0: No fee, stakers receive their full proportional rewards
- Public bakers typically set this lower (e.g., 0.05-0.15) to attract stakers

Configure these via [TezGov](https://gov.tez.capital) or CLI. Changes take effect after 5 cycles (~5 days).

---

## Reward Distribution

### Who Pays Whom

| Recipient | Who Pays | Notes |
|-----------|----------|-------|
| Baker (own stake) | Protocol | Direct payment each cycle |
| Stakers | Protocol | Automatic, no baker action needed |
| Delegators | **Baker** | You must distribute using payment software |

**Key Point:** Staker rewards are handled automatically by the protocol. Delegator rewards require you to actively distribute them.

---

## Automating Delegator Payments

[TezPay](/tezpay/tutorials/setup/) makes delegator reward distribution fully automatic. You can:

- **Run completely hands-off** - TezPay pays delegators automatically after each cycle
- **Integrate with TezBake** - One command adds the pay module to your existing baker setup
- **Pay on your schedule** - Every cycle, weekly, or any interval you choose

### Available Configuration Options

TezPay gives you full control over your payout policies:

| Option | What It Does |
|--------|--------------|
| **Custom fees per delegator** | Give friends 0% fee, loyal delegators a discount, or set any rate per address |
| **Payment timing** | Control min/max delay after cycle end to spread out transactions |
| **Payout mode** | Pay based on ideal (expected) or actual (what you earned) rewards |
| **Minimum payout threshold** | Skip tiny payouts below a threshold to save on transaction fees |
| **Minimum delegator balance** | Filter out dust delegations |
| **Delegator filtering** | Whitelist or blacklist specific addresses |
| **Income splitting** | Split baker rewards between multiple wallets |
| **Payout redirection** | Send a delegator's rewards to a different address |
| **Batched payments** | Aggregate multiple cycles into single payouts |
| **Notifications** | Get alerts via Discord, Telegram, Twitter, or email |

See [TezPay Setup](/tezpay/tutorials/setup/) for complete configuration details.

---

## Registering as a Public Baker

To appear in wallets and explorer listings, register with:

* <https://tzkt.io> / <https://baking-bad.org> (Baking Bad)
  * Contact: <https://t.me/baking_bad_chat> or <https://discord.gg/aG8XKuwsQd>

**You'll need to provide:**
- Baker address
- Fee percentage
- Payment frequency
- Minimum delegation
- Contact information

Most wallets and services on Tezos pull their baker information from TzKT. If you are not listed on TzKT, you will not be listed on most wallets and services.

---

## Recommended Settings for New Public Bakers

| Setting | Recommended Value | Notes |
|---------|-------------------|-------|
| Fee | 5-10% | Competitive but sustainable |
| Minimum payout | 0.5-1 XTZ | Balance between tx costs and fairness |
| Minimum balance | 10-100 XTZ | Filter dust delegations |
| Payment frequency | Every cycle or weekly | Delegators prefer frequent payouts |
| Baker pays tx fees | Yes | More attractive to delegators |

---

## Related Guides

* [TezPay Setup](/tezpay/tutorials/setup/) - Installation and configuration
* [TezBake Integration](/tezpay/tutorials/tezbake-integration/) - Automate with your baker
* [Notifications](/tezpay/tutorials/notifications/) - Set up payout alerts
* [Slashing Explained](/getting-started/slashing-explained/) - Understand the risks
* [Best Practices](/getting-started/best-practices/) - Operational guidelines

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
