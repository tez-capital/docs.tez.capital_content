---
title: "Baker Signal on Tezos.Systems"
weight: 9
type: docs
summary: Find a maintenance gap between scheduled baking rights and check your baker’s consensus attestations and DAL participation on mainnet.
---

[Baker Signal on Tezos.Systems](https://tezos.systems/) shows when your mainnet baker is next scheduled to produce a block and how well it is attesting to blocks and Data Availability Layer (DAL) slots. You only need the baker’s public address or a Tezos domain that points to it.

## Open your baker’s signal

Append `/signal` to the baker’s Tezos.Systems URL. Use the address registered as the baker (the delegate address), even if a separate consensus key signs for it. Both examples below open the same public baker, Baking Benjamins.

- **By domain.** In `https://tezos.systems/baker.tez/signal`, replace `baker.tez` with your baker’s domain. [Open bakingbenjamins.tez](https://tezos.systems/bakingbenjamins.tez/signal).
- **By address.** In `https://tezos.systems/tz1address/signal`, replace `tz1address` with the full baker address. [Open Baking Benjamins by address](https://tezos.systems/tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur/signal).

You can also open **My Tezos** from the homepage, enter the public address or domain in **Overview**, choose **Track address**, then select **Baker Signal**.

## Plan maintenance

1. In **Outage**, choose a duration that covers the work, restart and time for your node to catch up. The options are 5, 15, 30 and 60 minutes.
2. Read **Maintenance gap** for the earliest fit. It gives a time range in your browser’s time zone and the block level to be back before. The **Next round 0** cards show when your baker is first in line to produce a block.
3. Recheck the live page just before stopping services. Expand **Schedule coverage & timing** to see when the rights were checked or verify the schedule on TzKT. If you see **No fitting gap**, the selected outage does not fit within the published rights checked. If the schedule is waiting for fresh data, wait for it to update.

[![Baker Signal with Outage set to 15 minutes and a maintenance gap between levels 15,774,122 and 15,774,419.](/getting-started/baker-signal/maintenance-gap.png)](/getting-started/baker-signal/maintenance-gap.png)

On November 11, 2026, Baking Benjamins had a round-0 right at about 9:27 PM UTC. For a 15-minute restart, the planner suggested **9:30–9:45 PM UTC**, after level **15,774,122** and before **15,774,419**. Starting immediately would have overlapped the first right. Select the screenshot to read the schedule at full size.

The planner checks up to 100 published round-0 rights and leaves a two-minute buffer before the next right and after a preceding scheduled right. Block times can shift. Consensus attestations and DAL duties continue during a gap, and later-round baking is still possible.

## Check attestation and DAL

After maintenance, look for a recent block level **after your restart**. The three cards answer different questions.

| Card | What to look for |
| --- | --- |
| **Baker working?** | Check the last observed baking and attestation activity for signs that the baker is signing again. |
| **Attestation** | Read the consensus participation percentage alongside the recent attestation results and latest level. A cycle percentage can round to 100.0% even with a miss. |
| **DAL** | Compare attested slots with attestable slots for the current cycle. **N/A** means no slots have been assigned yet. **No data** means the participation reading is unavailable. |

[![Baker working shows Working, Attestation shows the last 10 attestations succeeded, and DAL shows 58 of 58 slots attested.](/getting-started/baker-signal/attestation-and-dal.png)](/getting-started/baker-signal/attestation-and-dal.png)

In this snapshot, the last **10 consensus attestations** succeeded through level **15,774,091**. The separate **58/58** count means the baker attested all 58 eligible DAL slots so far that cycle. After a DAL restart, check again when more slots become eligible to see whether the attested count rises.

If consensus misses repeat, compare the affected levels with your [baker and signer logs](/tezbake/tutorials/monitoring-logs-and-status/). If eligible DAL slots keep accumulating without new attestations, check the [DAL node and attester profile](/tezbake/tutorials/baking-with-dal/).

---

Any questions/comments/concerns? Please contact the Tez Capital team on [Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
