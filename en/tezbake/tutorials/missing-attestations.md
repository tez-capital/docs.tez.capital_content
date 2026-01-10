---
title: "Missing Attestations in Baking"
weight: 13
type: docs
summary: Occasional missing endorsements are expected in a distributed protocol like Tezos.
---

## Overview

Sometimes, you’ll notice that an attestation (also known as an endorsement) didn’t make it into a block — even though your setup appears to be working fine. This is usually **not a problem**.

**Key Points:**

- Tezos is a distributed network; messages can arrive late or out of sync.
- Block producers are not obligated to include all attestations.
- If your attestations succeed *most of the time*, your setup is likely healthy.

---

## Why It Happens

Attestations have a tight timing window. If your signer’s message arrives just after the block producer includes attestations, it won’t make the cut.

> Think of it like catching a departing elevator — if you’re a second late, the doors close and you're left behind, even if you were just steps away.

---

## When to Worry

Occasional missed attestations are expected. But you should investigate if:

- You miss **frequently**
- You miss **multiple in a row**

Possible causes include:

- Your setup isn't powerful enough to produce attestations in time
- Your network connection is unstable (latency spikes or downtime)
- Your system clock is out of sync

---

## What to Check

Before assuming something is broken, check the following:

- **Was it a broader issue?**  
  Look up the block in [TzKT](https://tzkt.io/) and see how many attestations were included.  
  If the block was missing a significant portion of attestations (e.g., 1/3), this is more likely a network or block producer issue than a problem with your setup.
  Example: [https://tzkt.io/8830884/operations](https://tzkt.io/8830884/operations)

- **Is your clock synced?**  
  Run `timedatectl status` to check your system clock's synchronization status.

> **Note:** The command may vary depending on your Linux distribution. Here are some examples:
>
> - For systems using `timedatectl`:  
  `timedatectl status`
> - For systems using `chronyc`:  
  `chronyc tracking`
> - For systems using `ntpq`:  
  `ntpq -p`

- **Is your setup healthy?**  
  Use:  
  ``tezbake info``  
  ``tezbake node logs -f``  
  ``tezbake signer logs -f``

Look for signs of delay, network errors, or system load.

---

## Summary

Occasional missed attestations are a normal and expected part of Tezos baking. Unless it becomes a trend, there’s no reason to worry.

> Like traffic lights turning red just as you arrive — frustrating, but part of the flow.

---

## Related Guides

* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Check baker health
* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Fix common issues
* [TezWatch Setup](/tezwatch/tutorials/setup/) - Get alerted to missed attestations

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
