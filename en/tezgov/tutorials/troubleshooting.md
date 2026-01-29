---
title: "Troubleshooting"
weight: 4
type: docs
summary: Common TezGov issues and solutions for Ledger, voting, and staking problems
---

## TezGov Troubleshooting

TezGov (https://gov.tez.capital) is used for protocol voting, staking management, and baker configuration. Most issues involve Ledger connectivity or understanding the two different voting systems.

---

## Ledger Connection Issues

### "Transaction Not Trusted" Warning

**Cause:** Voting from your baking machine while the baker is running. The new Ledger Wallet app can't share the device with the running baker process.

**Solution:**
```bash
tezbake stop
# Perform voting via TezGov
tezbake start
```

**Alternative:** Use a secondary Ledger with the same seed on a different machine.

### "Failed to Open Device"

1. Ensure **Wallet** app is open (not Baking app)
2. Reconnect USB cable
3. Try different USB port
4. Restart browser
5. Try incognito/private window

### Browser Requirements

- **Use Chromium-based browsers** (Chrome, Brave, Edge)
- Firefox may have WebUSB issues with Ledger
- Clear cache with `Ctrl+Shift+R` (or `Cmd+Shift+R` on Mac) if UI is stale

### Expert Mode Required

Some operations require Expert Mode in Ledger settings:
- Settings → Expert mode → Enable

---

## Two Types of Voting (Important!)

Tezos has **two completely different voting mechanisms**. Don't confuse them!

### 1. Protocol Amendment Voting (via TezGov)

- **What:** Voting on protocol upgrades (e.g., Paris, Quebec)
- **When:** During Exploration and Promotion periods
- **How:** Cast YAY/NAY/PASS ballot on TezGov
- **URL:** https://gov.tez.capital

### 2. Per-Block Voting (via vote-file.json)

- **What:** Liquidity Baking toggle + Adaptive Issuance toggle
- **When:** Every block you bake
- **How:** Edit config file on your baking node
- **Location:** `/bake-buddy/node/data/vote-file.json`

```json
{
  "liquidity_baking_toggle_vote": "on",
  "adaptive_issuance_vote": "on"
}
```

**What is Adaptive Issuance?**

Adaptive Issuance is a Tezos protocol feature that dynamically adjusts block rewards based on the global staking ratio. When more tez is staked, rewards decrease; when less is staked, rewards increase. This creates an equilibrium that balances security with inflation.

- `"on"` = Support adaptive issuance
- `"off"` = Oppose adaptive issuance
- `"pass"` = Abstain

After editing, restart: `tezbake stop && tezbake start`

**⚠️ TezGov does NOT handle per-block votes - those are configured on your node.**

---

## Protocol Voting Issues

### Vote Not Counting

**Common mistake:** Clicking "Signal" instead of the actual vote button.

- **Signal** (bottom of page): Shows preference to delegators only, NOT an on-chain vote
- **UPVOTE/YAY/NAY/PASS** (top): Actual on-chain vote

**Solution:** Click the actual vote button at the top of the proposal.

### Voting Periods

| Period | Can Vote? | Duration |
|--------|-----------|----------|
| Proposal | UPVOTE only | ~14 days |
| Exploration | YAY/NAY/PASS | ~14 days |
| Cooldown | No voting | ~14 days |
| Promotion | YAY/NAY/PASS | ~14 days |
| Adoption | No voting | ~14 days |

### Check Current Voting Period

```bash
tezbake node client show voting period
```

---

## Staking Issues

### Setting Staking Parameters

**Via TezGov:**
1. Login at https://gov.tez.capital
2. Navigate to Baker Management
3. Set your parameters:
   - `limit_of_staking_over_baking`: 0-9x your stake
   - `edge_of_baking_over_staking`: 0.0-1.0 (your fee, e.g., 0.1 = 10%)

**Via CLI:**
```bash
tezbake signer client set delegate parameters for <tz_address> \
  --limit-of-staking-over-baking 5 \
  --edge-of-baking-over-staking 0.10
```

### Parameters Not Taking Effect

- Changes take **2-3 cycles (~3-5 days)** to activate
- External stakes are treated as delegation until parameters are set
- Default: Stakers NOT accepted until you explicitly set parameters

### Decimal Unstaking Issues

If unstaking decimal amounts fails:
- **Workaround:** Try whole numbers instead

### Pending Balance Quirks

- TezGov may not display pending balance correctly
- Staking a small amount may pull from pending balance unexpectedly

---

## Key Management

### Setting Consensus/Companion Keys

1. Generate POP on your baker:
   ```bash
   tezbake signer client create bls proof for baker
   tezbake signer client create bls proof for companion
   ```

2. Go to https://gov.tez.capital → Baker Management

3. Set consensus key: Paste tz4 address + POP

4. Set companion key: Paste tz4 address + POP

### Key Activation Timing

- **Activation delay:** 2-3 cycles (~3-5 days)
- Register in cycle N → Live in cycle N+3
- Check status: https://tzkt.io/YOUR_TZ1/secondary-keys

### Common Key Mistakes

- ❌ Setting keys in wrong order (consensus vs companion)
- ❌ Running same consensus key on two machines simultaneously
- ✅ Always verify on TzKT before and after changes

---

## Baker Reactivation

If your baker becomes inactive:

1. Go to https://gov.tez.capital
2. Click "TezGov" in top left
3. Find "Reactivate" button
4. Confirm transaction on Ledger

**Note:** You can also reactivate by simply resuming baking - if you attest >67% of assigned work, the baker automatically reactivates.

---

## Browser Issues

### UI Not Loading or Stale Data

1. Hard refresh: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)
2. Try incognito mode
3. Check browser console (`F12`) for errors
4. Try a different Chromium-based browser

### CORS or Network Errors

- Check if you're behind a corporate firewall
- Try from a different network
- Disable browser extensions temporarily

---

## Alternative Interfaces

If TezGov isn't working, you can use CLI commands directly:

```bash
# Check voting period
tezbake node client show voting period

# Submit proposal (Proposal period only)
tezbake node client submit proposals for <delegate> <proposal_hash>

# Cast ballot (Exploration/Promotion only)
tezbake node client submit ballot for <delegate> <proposal_hash> <yea|nay|pass>
```

For staking operations, you can also use https://stake.tezos.com as an alternative.

---

## MiCA / EU Considerations

- MiCA regulations cover **custodial** services only
- Decentralized non-custodial staking is NOT covered
- **Staking-only** mode (no payouts) is legally simpler for EU bakers
- You cannot disable delegations (protocol limitation)

---

## Related Guides

* [Voting on Proposals](/tezgov/tutorials/voting-on-proposals/) - Complete voting guide
* [Managing Baker Settings](/tezgov/tutorials/managing-baker-settings/) - Staking parameters
* [Injecting Proposals](/tezgov/tutorials/injecting-proposals/) - Submit new proposals

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
