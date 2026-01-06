---
title: "Tezos Baking Best Practices"
weight: 1
type: docs
summary: Tezos Baking Best Practices
---

## Baking Best Practices

This is the description of the ideal TezBake hardware setup and best practice for someone who wants to perform an excellent job baking on-premise, in their home, office or data center (in person) at a reasonable cost.

You can get an idea of what kind of computer needs to be dedicated to Tezos baking by looking at the guide below
[https://www.fxhash.xyz/article/how-to-setup-your-own-tezos-bakery](https://www.fxhash.xyz/article/how-to-setup-your-own-tezos-bakery)

## Recommended Hardware Setup

* Two computers (see link above) with equal or equivalent hardware specs *(one optional)*
* Two Ledger Nano S Plus or Nano X hardware wallets *(one optional)*. The Ledger Nano S works as well but is no longer recommended because it takes up to an extra second (compared to the other ones) for it to sign a block or endorsement. This can cause you to miss a block or endorsement
* One UPS battery backup *(optional)*

### Two computers

You will need two computers and both computers will be running TezBake. One computer will be used for baking and the other computer will be used as a backup. A manual failover of the Ledger hardware will be necessary to start baking on the second computer if the first computer fails. The second computer is optional, but it is highly recommended. If you only have one computer, you will need to have a backup plan in place in case the computer fails.

### Two Ledger Nano S Plus or Nano X

While having two computers is somewhat optional, especially for a smaller baker, having two Ledger Nano S Plus or Nano X hardware wallets is very highly recommended. You will need to have two hardware wallets for the following reasons:

* In the event of Ledger hardware failure
* To transfer tez to your payment wallet or elsewhere, without interrupting baking
* To vote on Tezos governance proposals, without interrupting baking

> **🚨 CRITICAL WARNING: Prevent Double Baking**
>
> You must NEVER simultaneously use two hardware wallets to bake for the same baking address. This will result in a double baking or double endorsing offense and you will be slashed.
>
> **How to avoid this:**
>
> * Do not authorize the second hardware wallet to bake before it's needed
> * Do not import your baking wallet into your backup computer before it's needed
> * Before changing any Ledger connections, always ensure only one hardware wallet is baking
> * Do not install TezBake on second computer before it's needed (setup takes just a few minutes with fast internet)

If you only have one hardware wallet and it fails, you will be down until you get a second one and set it up to bake.

### One UPS battery backup

Having at least one UPS device which has your ISP connection and your baking computer connected to it is highly recommended. This will ensure that your baking computer will not be interrupted in the event of electrical failure up to the amount of time supported by your battery.  The bigger the battery, the more time your computer will keep baking when power fails.

If your baking computer and your ISP are in different rooms or on different electrical outlets, you need to ensure that each one is independently supplied with UPS power.

## Bad Practices

* Using Wi-Fi
* Using DHCP
* Using your daily use computer or laptop
* Not using TezBake Discord or Telegram monitoring bots

### Using Wi-Fi

> **⚠️ NOT RECOMMENDED**
>
> Wi-Fi is not as reliable as a wired Ethernet connection and can cause intermittent connectivity issues. Always use a wired connection for your baking computer.

### Using DHCP

> **⚠️ NOT RECOMMENDED**
>
> DHCP automatically assigns IP addresses to your computer. This can cause issues if DHCP fails to renew the lease automatically. Use a static IP address for your baking computer instead.

### Using your daily computer or laptop

> **⚠️ NOT RECOMMENDED**
>
> Using your daily-use computer for baking increases the risk of accidentally disrupting either your baker's internet connection or your Ledger's USB connection. Use a dedicated machine for baking.

### Not using TezBake Discord or Telegram monitoring bots

> **⚠️ NOT RECOMMENDED**
>
> The TezBake Discord and Telegram bots monitor your baker's status and alert you immediately if issues arise. You should configure these monitoring bots to receive instant notifications when your baker goes offline.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
