---
title: "Baking with Consensus Key"
weight: 11
type: docs
summary: TezBake Baking Tutorial for using a Consensus Key
---

> **⚠️ WARNING: tz4/BLS Signing Requirements**
>
> tz4/BLS signing migration now requires both a Consensus Key AND a Companion Key.
>
> **Key Points:**
>
> * Bakers must set their new consensus and companion keys together when first changing to tz4 signing
> * Later, you can rotate just your consensus key if desired
> * The companion key is **mandatory** for tz4 BLS signing (handles DAL-related duties)
> * When you see "companion," think "DAL"
> * **Consensus and companion always go together**
>
> **In Practice:**
>
> When activating a Consensus Key, you must also have a Companion Key activated. Both keys are used together for baking simultaneously.
>
> **ℹ️ INFO - Video Outdated:**
> The steps below have been updated, but the video does not include the "Companion Key" step which is now mandatory. Follow the written instructions to set both your consensus key and companion key.
>
> **ℹ️ INFO - tz1-3 Users:**
> If you are baking with a tz1-3 key, you do NOT need a companion key. All instructions below assume tz4 consensus + companion. If using an old tz1-3 key, simply omit the companion key steps.

Follow along on Youtube!
{{< youtube 5m_GKFRIflk >}}

## Table of Contents

1. [Preparation](#preparation)
2. [BLS/tz4 Consensus and Companion key setup](#blstz4-consensus-and-companion-key-setup)
3. [PRE-BLS (DEPRECATED) Ledger Consensus key setup](#pre-bls-deprecated-ledger-consensus-key-setup)

---

## Preparation

For this tutorial, you'll need to have already followed one of the following tutorials:

* [How to Bake](/tezbake/tutorials/baking-on-mainnet)
* [How to Bake on Testnets](/tezbake/tutorials/baking-on-testnets)

A Tezos consensus key is a cryptographic key specifically used for signing blocks and consensus operations (attesting blocks) in the Tezos blockchain. Introduced to improve security and operational flexibility, it allows bakers (validators) to delegate block-signing responsibilities to a different key than the one associated with their primary baking account.

A Tezos companion key was not necessary to use in the tz1-tz3 address era. With the introduction of tz4 or BLS consensus signing (aggregating signatures), it is now necessary to use a separate companion key, in addition to the separate consensus key to validate blocks in the tz4 era. Both the consensus key and the companion key bake together at the same time. The consensus key works with the consensus and the companion key works with the DAL.

This separation of roles is useful for reducing the exposure of the primary baker key (which holds funds and has broader permissions) by isolating consensus-related tasks to a different key. If compromised, only the consensus operations are affected, not the funds held by the baker's main account.

> **🚨 CRITICAL: Key Compromise Risks**
>
> **If Consensus Key is Compromised:**
>
> * Attacker can sign blocks and endorse operations
> * Attacker can maliciously double-bake or double-attest on your behalf, **slashing your funds**. See [Slashing Explained](/getting-started/slashing-explained/)
> * Attacker can transfer all baker funds NOT locked/staked in the security deposit using the drain operation
>
> **Protection Measures:**
>
> * Lock/stake ALL baking funds in the security deposit to eliminate drain risk
> * Rotate the consensus key before stopping baking operations and unstaking the security deposit
>
> **If Companion Key is Compromised:**
>
> * Attacker can cost you 10% of your baking income due to DAL penalties

---

## BLS/tz4 Consensus and Companion key setup

Simply follow the TezSign instructions to generate, verify and activate your TezSign keys.

[Baking with TezSign](/tezbake/tutorials/baking-with-tezsign)

## PRE-BLS (DEPRECATED) Ledger Consensus key setup

### Import the consensus key from a Ledger

Plug in your consensus key Ledger device and open the Tezos Baking app.

Run the following command to list the available Ledgers:

```bash
tezbake list-ledgers
```

Note the 4 word ID of the Ledger you want to use for the consensus key.

Run the following command to import the consensus key:

```bash
tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --ledger-id "apple-banana-coconut-date" --hwm 1 --key-alias=consensus
```

> **ℹ️ Configuration Notes:**
>
> * Replace the `--ledger-id` value with the 4-word ID of the Ledger you want to use for the consensus key
> * We use the P-256 (tz3) curve for the consensus key because it's the fastest on Ledger hardware and the most portable option for both on-premise and cloud hardware security modules (HSMs)
> * The consensus key is only used for signing blocks and attestations, so it doesn't need to be the same curve as the baker key
> * Many bakers move from a tz1 key to a tz3 key for the consensus key to improve performance

This will import your consensus key Ledger device and authorize it for baking. Leave the baking app running on the Ledger device.

### Modify the baking configuration

Add the consensus key alias so the baker knows to inject it alongside your default baker key:

```bash
tezbake node modify --set configuration.additional_key_aliases '["consensus"]'
```

You can verify it was set:

```bash
tezbake node show configuration.additional_key_aliases
```

> **📖 See [Key Aliases](/tezbake/tutorials/key-aliases/) for the full reference** on managing key aliases, including the difference between `additional_key_aliases` and `key_aliases`.

Re-run the TezBake upgrade and merge your configuration when asked:

```bash
tezbake stop
tezbake upgrade
tezbake start
```

### Register the consensus key

Get your consensus key public key hash:

```bash
cat /bake-buddy/node/data/.tezos-client/public_keys
```

The public key is the one in the `key` field.

> **ℹ️ Example:** A public key for a tz3 address looks like: `p2pk66fWs9UZ6T4nVTfHfV9PtuJje4xYBh2RVo4517a8VTj6Cny7ZXY`

To register the consensus key, run the following command:

```bash
tezbake signer client set consensus key for baker to consensus
```

You can also set your consensus key on TezGov via <https://gov.tez.capital>.

The new consensus key will become effective after 3 cycles (~3 days). For example if you set your consensus key in cycle 1000, it will be effective in cycle 1003.

Once the consensus Ledger becomes effective, you can unplug your original Ledger device as it's no longer needed for baking.

### Confirm the consensus key is working

Once the consensus key becomes effective, you will see a change in the baking logs by showing the consensus key operating on blocks and attestations on behalf of the baker key.

```bash
Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449: injected attestation op5RtCGypnrri9FyYHy91haPWB6CpouxNABcgM7BSUmr81p27G4 for
Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   consensus (tz3P9WvzULMuss5iDk4tjNQYWkwSrLAjUuh7)
Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   on behalf of tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur for level 7403648, round
Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   0
```

To view your baker logs, run the following command:

```bash
tezbake node log baker -f
```

---

## Related Guides

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Standard setup guide
* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - Hardware signer setup
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Check baker health

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
