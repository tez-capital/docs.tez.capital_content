---
title: "Per-Block Votes"
weight: 6
type: docs
summary: "How to configure your baker's Liquidity Baking per-block vote with supported TezBake commands"
---

This page documents the *currently supported* TezBake workflow for per-block voting.

At this time, use this guide for the *Liquidity Baking toggle vote* only.

Per-block votes are separate from [protocol upgrade voting via TezGov](https://gov.tez.capital).

## Prerequisites

- TezBake installed and running
- Access to the baker host shell
- `sudo` access (if your deployment requires it)

## Configure the Liquidity Baking Vote

Set your persisted vote value:

```bash
tezbake node modify configuration.VOTE_FILE.liquidity_baking_toggle_vote pass
```

Accepted values are:

- `on`
- `off`
- `pass`

## Verify the Current Value

```bash
tezbake node show configuration.VOTE_FILE.liquidity_baking_toggle_vote
```

## Apply Node Config (Only If Needed)

If you need to apply node configuration changes immediately, run:

```bash
tezbake upgrade --node
```

If your vote file is already correct, a restart/apply is not required just to continue baking with that vote.

## Important Notes

- Do *not* use TezGov for per-block LB vote configuration.
- This guide intentionally avoids unsupported/undocumented vote-file editing workflows.
- If TezBake adds first-class support for additional per-block vote fields, this page will be expanded.

## What's Next

- Continue with [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/)
- Review [TezGov voting workflow](/tezgov/tutorials/cast-votes/) for protocol amendment votes
