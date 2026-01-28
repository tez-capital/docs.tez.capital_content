---
title: "Managing Baker Settings"
weight: 3
type: docs
summary: How to configure staking parameters, consensus keys, and manage your baker via TezGov
---

## Baker Management with TezGov

TezGov provides a web interface for managing all aspects of your baker beyond voting, including:

- Setting staking parameters (limits and fees)
- Staking and unstaking funds
- Setting consensus and companion keys
- Reactivating an inactive baker

---

## Connecting to TezGov

[TezGov Web Portal: https://gov.tez.capital](https://gov.tez.capital "TezGov Web Portal")

**Connection Methods:**

* **Ledger Direct (Recommended)**: Connect your Ledger directly via USB. You'll see transaction details on the Ledger screen before signing.
* **Remote Signer**: For advanced users with a signer on their LAN.
* **Beacon Wallet**: Use Temple, Kukai, or other Beacon-enabled wallets. Note: You'll sign a blob message rather than seeing specific details.

> **💡 TIP**: Use the Tezos **Wallet** app on your Ledger, not the Baking app. The Baking app cannot sign these operations.

---

## Setting Delegate Parameters

Delegate parameters control how your baker accepts stakers and what fee you charge.

### Parameters Explained

| Parameter | Description | Range |
|-----------|-------------|-------|
| **Limit of staking over baking** | Maximum external stake you'll accept, relative to your own stake | 0 to 5; by default new bakers do not accept external stake |
| **Edge of baking over staking** | Your fee (portion of staking rewards you keep) | 0.0 to 1.0 |

### Understanding the Limits

- **Staking limit of 5** means you can accept external stakers up to 5× your own frozen stake
- **Edge of 0.10** means you keep 10% of staking rewards as your fee
- **Delegation limit** is always 9× your frozen stake (not configurable)

### Example Configurations

| Baker Goal | Staking Limit | Edge | Result |
|------------|---------------|------|--------|
| Accept stakers, 10% fee | 5 | 0.10 | Max stakers, standard fee |
| Accept stakers, no fee | 5 | 0.00 | Max stakers, free for stakers |
| Delegation only (no stakers) | 0 | N/A | All external funds treated as delegation |
| Staking only (no delegator payouts) | 5 | 0.10 | Accept stakers, no TezPay needed |
| Private baker | 0 | 1.00 | No stakers, keep all rewards |

### Staking Only Mode

**Staking only** is an option for public bakers who want to accept stakers but **not** pay delegators. This simplifies operations since:

- **Stakers** receive rewards automatically from the protocol (minus your edge/fee)
- **Delegators** can still delegate to you, but you don't run TezPay or send manual payouts
- You register your baker as "staking only" on [TzKT](https://tzkt.io) so delegators know not to expect payments

**Benefits:**
- No need to run TezPay or manage payout infrastructure
- Reduced operational complexity
- Clearer expectations for delegators (they see "staking only" on TzKT)
- May have legal/tax advantages in some jurisdictions (no active payout service)

**How to set up:**
1. Set your staking parameters in TezGov (e.g., limit of 5, edge of 0.10)
2. Register your baker as "staking only" on TzKT's baker registry
3. Delegators will see this status and can choose to stake instead if they want rewards

> **💡 TIP**: Delegators who want rewards from a staking-only baker should **stake** their funds instead of delegating. Staked funds automatically receive protocol rewards.

### How to Set Parameters

1. Log into [TezGov](https://gov.tez.capital) with your baker's key
2. Navigate to the **Baker Settings** or **Delegate Parameters** section
3. Set your desired **Limit of staking over baking** (0-5)
4. Set your desired **Edge of baking over staking** (e.g., 0.10 for 10%)
5. Click **Update Parameters**
6. Confirm the transaction on your Ledger

### Important Notes

- **Default state**: By default, stakers are NOT accepted until you explicitly set parameters
- **Activation delay**: Parameter changes take effect after **5 cycles** (~5 days)
- **External stakes**: Until parameters are set, any external stake is treated as delegation

### CLI Alternative

You can also set parameters via the command line:

```bash
tezbake signer client set delegate parameters for <tz_address> \
  --limit-of-staking-over-baking 5 \
  --edge-of-baking-over-staking 0.10
```

---

## Staking and Unstaking

TezGov allows you to stake additional funds or unstake existing funds from your baker.

### Staking Funds

1. Log into TezGov
2. Navigate to the **Staking** section
3. Enter the amount to stake
4. Click **Stake**
5. Confirm on your Ledger

Staked funds become part of your frozen stake and increase your baking power.

### Unstaking Funds

Unstaking is a multi-step process due to the protocol's security requirements:

1. **Initiate unstake**: Request to unstake an amount
2. **Waiting period**: Funds are "frozen unstaked" for **4 cycles** (~5-6 days)
3. **Finalize**: After the waiting period, finalize to make funds spendable

**To unstake:**

1. Log into TezGov
2. Navigate to the **Staking** section
3. Enter the amount to unstake
4. Click **Unstake**
5. Confirm on your Ledger
6. Wait 4 cycles
7. Return to finalize the unstake

> **⚠️ NOTE**: If you have issues unstaking decimal amounts, try using whole numbers instead.

---

## Setting Consensus and Companion Keys

Consensus keys allow you to separate your baker's consensus signing from your main wallet key. This is particularly useful for:

- Enhanced security (main key stays cold)
- Using BLS keys (tz4) for consensus
- DAL attestation (requires companion key)

### Key Types

| Key Type | Purpose | Format |
|----------|---------|--------|
| **Consensus Key** | Signs blocks and attestations | tz1, tz2, tz3, or tz4 |
| **Companion Key** | Required for DAL when using tz4 consensus | tz4 only |

### Setting Keys via TezGov

1. Log into TezGov with your baker's manager key
2. Navigate to **Baker Management** or **Keys** section
3. **For Consensus Key**:
   - Paste your new consensus key address (tz1/tz2/tz3/tz4)
   - Paste the Proof of Possession (POP)
   - Confirm on Ledger
4. **For Companion Key** (if using tz4):
   - Paste your companion key address (tz4)
   - Paste the Proof of Possession (POP)
   - Confirm on Ledger

### Activation Timeline

- Key changes take effect after **3 cycles** (~3 days)
- Check activation status on TezGov or TzKT (Secondary Keys tab)
- Plan key rotations ahead of time

### Important Notes

- If using a **tz4 consensus key**, you **must** also set a companion key for DAL
- The companion key is specifically for DAL attestation duties
- Both keys should be set in the same session if using tz4

---

## Baker Reactivation

If your baker becomes inactive (e.g., due to missing too many attestations or being offline), you can reactivate it via TezGov.

### How to Reactivate

1. Log into [TezGov](https://gov.tez.capital)
2. Click **TezGov** in the top left corner
3. Look for the **Reactivate** button (only visible if your baker is inactive)
4. Click **Reactivate**
5. Confirm the transaction on your Ledger

### After Reactivation

- Your baker will become active again in the next cycle
- Ensure your baking infrastructure is running properly before reactivating
- Check `tezbake info` to verify your node is synced and baker is operational

---

## Troubleshooting

### "Transaction Not Trusted" Error

If you see this error while your baker is running:

1. Stop your baker: `tezbake stop`
2. Perform the operation in TezGov
3. Restart your baker: `tezbake start`

This occurs because the Ledger Wallet app cannot share the device with a running baker process.

### Connection Issues

- Ensure you're using the **Wallet** app, not the Baking app
- Try a different USB port or cable
- Use a Chromium-based browser (Chrome, Brave, Edge)
- Try hard refresh: `Ctrl + Shift + R` (Windows/Linux) or `Cmd + Shift + R` (Mac)

### Parameter Changes Not Taking Effect

- Remember: changes take **5 cycles** to activate
- Verify the transaction was confirmed on-chain (check TzKT)
- Check your current parameters: `tezbake info`

---

## Related Guides

* [Voting on Proposals](/tezgov/tutorials/voting-on-proposals/) - Participate in governance
* [Injecting Proposals](/tezgov/tutorials/injecting-proposals/) - Submit new proposals
* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Set up your baker

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
