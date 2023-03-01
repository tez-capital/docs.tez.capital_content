---
title: "How to Vote"
weight: 1
type: docs
summary: TezGov Proposal Voting Tutorial
---

## Tezos proposal voting

The Tezos proposal process consists of 5 stages, each of which lasts for 5 cycles (~14 days). Briefly, the proposal process is as follows:
1. *Proposal.* If no proposal gets more than 5% of the state to vote for it, the proposal process repeats indefinitely. As soon as a proposal gets 5% of the state to vote for it, the proposal process moves to the next stage. In cases where there are multiple 5%+ proposals on the table, the proposal with the most votes wins.
2. *Exploration* Proposal needs to hit quorum (around 50% of all stake voting in some way) and it needs to further hit an acceptance level of 80%+. If it fails to meet these thresholds, the process resets back to the *Proposal* stage.
3. *Cooldown* The proposal is tested on a dedicated test network to make sure it migrates and runs. Dapp developers make sure this proposal doesn't break their dapps.
4. *Promotion* Proposal needs to hit quorum (around 50% of all stake voting in some way) and it needs to further hit an acceptance level of 80%+. If it fails to meet these thresholds, the process resets back to the *Proposal* stage.
5. *Adoption* The proposal is officially accepted and goes into effect at the end of the *Adoption* stage. The Ghostnet test network is upgraded as a final test before Mainnet adoption. Dapp developers make sure this proposal doesn't break their dapps and make any necessary changes to their dapps.

We recommend you read more about the Tezos Voting process here: https://tezos.gitlab.io/active/voting.html

## Proposal voting with TezGov

TezGov allows you to participate in Tezos proposal voting with your Ledger hardware wallet. 

* We recommend using a secondary Ledger device with the same seed, to vote with while your primary device is baking. 
* You can also briefly unplug your primary Ledger baking device and use it on a different computer just for voting purposes (use the wallet app to vote), then immediately plugging it back into the primary device, opening the baking app and resuming baking only.
* If your baking computer itself has a graphical interface, you can briefly switch to the wallet app on your Ledger device and vote, then switch back to the baking app and resume baking only.

**In order to vote using your Ledger device, you must have the Tezos wallet app installed and open on your Ledger device. If you have the baking app open, you will not be able to vote**

---

## TezGov Web Portal

[TezGov Web Portal: https://gov.tez.capital](https://gov.tez.capital "TezGov Web Portal")

Log into the TezGov portal by using the most secure method available to you. 

* **We highly recommend using the direct Ledger login at the top. This method allows you to see the specific details of your vote on your Ledger screen.**
* Use the Remote (signer) method if you have a signer somewhere on your LAN or local computer. This is an option for advanced users. 
* Use the Beacon method if you are using a Beacon enabled wallet like Temple or Kukai. In this case you will not see the exact details of your vote on your Ledger screen and you will instead be asked to sign an arbitrary blob message. This method also works fine but it is inherently less secure than the direct Ledger method

![<TezGov login home screen>](/tezgov/tutorial/tezgovHome.png) 

Once logged in you'll see the TezGov home screen. You can see the current cycle, the current proposal, the current voting period, and the current voting period progress. You can also see the current proposal status and the current proposal details.

### Exploration and Promotion Periods
During the exploration and promotion periods you will see the period name `Exploration` or `Promotion` in the middle of the page. You can vote for the current proposal by clicking the `NAY`, `PASS` or `YAY` buttons.

![<TezGov login home screen>](/tezgov/tutorial/tezgovPromotion.png)

 You will be asked to confirm the vote on your Ledger device. 

![<TezGov login home screen>](/tezgov/tutorial/tezgovPromotionConfirm.png)

Once confirmed, the page will refresh within 30 seconds.

![<TezGov login home screen>](/tezgov/tutorial/tezgovPromotionConfirm2.png)

After finished, you'll be able to click on the confirmation link for your vote.

![<TezGov login home screen>](/tezgov/tutorial/tezgovPromotionConfirm3.png)

### Cooldown Period
During the cooldown period you will period name `Cooldown` in the middle of the page and you will not be able to vote until the next period. The time remaining is also reflected on the page.

![<TezGov Cooldown period](/tezgov/tutorial/tezgovCooldown.png) 


---

Any questions/comments/concerns please contact the Tez.Capital team on
[Discord](https://discord.gg/vykxNSnvQY) or [Telegram](https://t.me/bakebuddy) 