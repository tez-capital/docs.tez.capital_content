---
title: "Troubleshooting"
weight: 8
type: docs
summary: Common TezPay issues and solutions
---

## Common Errors and Solutions

### "failed to apply batch"

**Cause:** Usually RPC connectivity issues during payment broadcast.

**Solution:**
1. Check your RPC configuration
2. Add multiple RPCs to `rpc_pool` for redundancy (see [RPC Configuration](#rpc-configuration))
3. Retry the payment - may be transient

---

### RPC Timeout Errors

**Error:** `failed to fetch head metadata - Get "https://...": context deadline exceeded`

**Cause:** The configured RPC is not responding in time.

**Solution:**
1. Add multiple RPCs to your `config.hjson` (see below)
2. Check if your configured RPC is responsive
3. Consider using local RPC if available

---

### Extension Loading Error: "stream closed"

**Error:** `failed to load extension: stream closed`

**Cause:** Using the entire sample config with incompatible options.

**Solution:** Start with minimal config, only add options you need:

```hjson
{
  baker_address: "tz1YourBakerAddress"
  payout_fee: 0.05
  min_payout: 1
}
```

---

### Payment Not Recorded After Crash

If TezPay crashes mid-payout, check the reports directory. TezPay tracks each batch - completed batches are recorded and won't be duplicated.

**To verify:**

```bash
ls /bake-buddy/pay/reports/<cycle>/
```

Each successfully broadcast batch is recorded. Failed batches can be retried safely.

---

## RPC Configuration

RPC timeouts are the #1 cause of payment failures. Configure multiple RPC endpoints for reliability:

```hjson
network: {
    rpc_pool: [
        "https://rpc.tzkt.io/mainnet"
        "https://rpc.tzbeta.net/"
        "https://eu.rpc.tez.capital"
        "https://us.rpc.tez.capital"
        "https://prod.tcinfra.net/rpc/mainnet"
    ]
    tzkt_url: "https://api.tzkt.io"
}
```

TezPay automatically tries the next RPC if one fails. This is **strongly recommended** for production use.

### Check Your Current RPC Configuration

```bash
cat /bake-buddy/pay/config.hjson | grep -A 10 rpc
```

If no `rpc_pool` is configured, TezPay uses `https://eu.rpc.tez.capital/` by default.

---

## Viewing Logs

To diagnose issues, check the TezPay logs:

```bash
# View recent logs
tezbake pay log

# Follow logs in real-time
tezbake pay log -f
```

---

## After Protocol Upgrades

TezPay automatically pauses continual payouts during protocol upgrades. See [Protocol Upgrade Handling](/tezpay/tutorials/tezbake-integration/#protocol-upgrade-handling) for restart instructions.

---

## Catching Up Missed Payments

If payments were missed, use the `--include-previous-cycles` flag:

```bash
# Check last 5 cycles for missed payments
tezbake pay pay --include-previous-cycles 5
```

See [Catching Up Missed Payments](/tezpay/tutorials/tezbake-integration/#catching-up-missed-payments) for details.

---

## Related Guides

* [TezBake Pay Module Setup](/tezpay/tutorials/tezbake-integration/) - Full integration guide
* [Paying Delegators](/tezpay/tutorials/paying-delegators/) - Manual payout guide
* [Configuration Examples](/tezpay/configuration/examples/) - Sample configs

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
