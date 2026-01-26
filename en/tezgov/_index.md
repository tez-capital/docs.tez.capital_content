---
title: "TezGov"
weight: 5
type: docs
summary: Tezos governance portal for voting on proposals and managing baker settings
---

<style>
	.grid {
		display: grid;
		grid-template-columns: repeat(4, auto);
		grid-column-gap: 4px
	}

	.grid a {
		color: black;
		text-align: left;
	}

	.grid img {
		max-width: 100px;
		min-width: 40px;
		width: 20vw
	}
	.grid .link {
		transition: 0.2s
	}

	.grid .link:hover {
		transform: scale(1.1)
	}
</style>

<div class="grid" align="center" >
  <a href="tutorials/" >
	<div class="link" style="display: inline-block">
		<img src="/govbuddy.png" alt="GovBuddy"/>
		<div><p><i>Hi 👋 I'm your gov buddy!</i></p></div>
	</div>
  </a>
</div>

## What is TezGov?

[TezGov](https://gov.tez.capital) is your all-in-one portal for Tezos baker management and governance participation.

### Features

| Feature | Description |
|---------|-------------|
| **Protocol Voting** | Vote on Tezos protocol upgrades during Exploration and Promotion periods |
| **Proposal Submission** | Submit new protocol proposals during Proposal periods |
| **Staking Parameters** | Set your staking limit (0-5x) and fee (edge) for accepting stakers |
| **Stake Management** | Stake and unstake funds from your baker |
| **Consensus Keys** | Set and rotate consensus and companion keys |
| **Baker Reactivation** | Reactivate your baker if it becomes inactive |

### Connection Methods

- **Ledger Direct** (Recommended) - See transaction details on your Ledger screen
- **Remote Signer** - Connect to a signer on your local network
- **Beacon Wallet** - Use Temple, Kukai, or other Beacon-enabled wallets

> **💡 TIP**: Use the Tezos **Wallet** app on your Ledger, not the Baking app.