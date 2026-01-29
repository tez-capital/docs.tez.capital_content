---
title: "Troubleshooting"
weight: 6
type: docs
summary: Common TezBake issues and solutions for installation errors and runtime problems
---

## Troubleshooting

Troubleshooting TezBake mostly comes in during the installation phase, when an error is encountered during the setup process.

### Installation errors

If you encounter an installation issue, run the setup command again with the `--log-level=trace` option, for example:

```bash
tezbake setup --log-level=trace
```

You can add `--log-level=trace` regardless of the setup type that's being attempted, whether it's on Ghostnet or some other testnet.

### Blockchain errors

Sometimes, you will notice that `tezbake info` never seems to show that you are able to fully synchronize your node. Upon looking at it you may find that your node is stuck on a certain block. This is usually due to a problem with the Tezos node itself. The Tezos node is a separate process from the TezBake process. If you are having issues with the Tezos node, you can try to restart it with the following command:

```bash
tezbake restart
```

You may see an error like the one below, which indicates your node needs to be bootstrapped with a fresh copy of the Tezos blockchain.

```bash
baker@baker-VirtualBox:~$ tezbake node log node -f
-- Logs begin at Wed 2022-05-18 14:16:28 CEST. --
mai 20 08:51:31 baker-VirtualBox systemd[1]: Stopped bb-default-node node service.
mai 20 08:51:31 baker-VirtualBox systemd[1]: Started bb-default-node node service.
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.414 - node.config.validation: the node configuration has been successfully validated.
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.415 - node.main: read identity file (peer_id = idqueYR61yjX8QfsiLru4FEZFSWi7m)
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.415 - node.main: starting the Tezos node v13.0 (cb9f439e) (chain = TEZOS_MAINNET)
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.415 - node.main: disabled local peer discovery
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.415 - node: shell-node initialization: bootstrapping
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.678 - node: shell-node initialization: p2p_maintain_started
mai 20 08:51:32 baker-VirtualBox node[5868]: May 20 08:51:32.678 - external_block_validator: initialized
mai 20 08:51:33 baker-VirtualBox node[5868]: May 20 08:51:33.599 - external_block_validator: block validator process started with pid 5874
mai 20 08:51:43 baker-VirtualBox node[5868]: May 20 08:51:43.267 - node.store: the store is in an inconsistent state:
mai 20 08:51:43 baker-VirtualBox node[5868]: May 20 08:51:43.267 - node.store:   Error:
mai 20 08:51:43 baker-VirtualBox node[5868]: May 20 08:51:43.267 - node.store:     The block 'current_head' is unexpectedly missing from the store.
mai 20 08:51:43 baker-VirtualBox node[5868]: May 20 08:51:43.267 - node.store:
mai 20 08:51:43 baker-VirtualBox node[5868]: May 20 08:51:43.267 - node.store: attempting to restore the store's consistency...
```

---

## Common Fixes

### Clear Cache

If you encounter plugin or installation issues, clear the AMI cache:

```bash
ami --erase-cache && sudo ami --erase-cache
```

### Corrupted Node Storage

If your node won't sync or shows storage errors, you may need to clear the storage and rebootstrap:

```bash
# Stop the node first
tezbake stop

# Remove corrupted storage files
rm -rf /bake-buddy/node/data/.tezos-node/daily_logs \
       /bake-buddy/node/data/.tezos-node/context \
       /bake-buddy/node/data/.tezos-node/lock \
       /bake-buddy/node/data/.tezos-node/store

# Rebootstrap with a rolling snapshot
tezbake bootstrap-node https://snapshots.tzinit.org/mainnet/rolling --no-check
```

### Full Disk

Check disk usage and identify large directories:

```bash
# Check overall disk space
df -hT

# Check TezBake directory sizes
du -sh /bake-buddy/node/data/.*
```

A rolling node should typically be under 100GB. If significantly larger, clear the storage and rebootstrap with a rolling snapshot as shown above.

### Update eli (Lua Interpreter)

If you encounter "illegal instruction" or other low-level errors, update eli:

```bash
# Check current version
eli -v

# Update to latest
wget -q https://raw.githubusercontent.com/alis-is/eli/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
```

eli version should be `0.36.3` or higher.

### Shell Path Issues After Update

If `tezbake` commands fail after an update, refresh your shell's path cache:

```bash
hash -r
```

This is needed because tezbake moved to `/usr/local/bin/tezbake`.

### TezSign USB Issues

If your TezSign device becomes unresponsive or you see timeout errors, try resetting the USB port before rebooting the machine:

```bash
tezbake tezsign advanced usb-port-reset
```

This often recovers the device without requiring a full system restart.

---

## Related Guides

**Setup & Operations:**

* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Main setup guide
* [Monitoring Logs and Status](/tezbake/tutorials/monitoring-logs-and-status/) - Monitor your baker
* [Missing Attestations](/tezbake/tutorials/missing-attestations/) - Debug attestation issues

**Getting Help:**

* [Best Practices](/getting-started/best-practices/) - Prevent common issues
* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - TezSign-specific setup

---

---

## DAL Troubleshooting

The Data Availability Layer (DAL) accounts for ~10% of baking rewards. If your DAL isn't working properly, you're missing out on income.

### Command Translation: Octez → TezBake

When reading official [Octez DAL documentation](https://octez.tezos.com/docs/shell/dal_run.html), translate commands:

| Octez Command | TezBake Equivalent |
|---------------|-------------------|
| `octez-client ...` | `tezbake node client ...` |
| `octez-node config ...` | `tezbake node config ...` |

### Quick DAL Status Check

```bash
tezbake info --dal
```

**Note:** "Status down" is normal immediately after start/restart. Give it time to sync.

### Step-by-Step DAL Verification

**1. Check connected peers:**
```bash
curl http://localhost:10732/p2p/points/info?connected
```
Expected: List of connected DAL nodes. Empty = connection problem.

**2. Check topic subscriptions:**
```bash
curl http://localhost:10732/p2p/gossipsub/topics
```
Expected: Topics like `{"slot_index":N,"pkh":"tz1..."}`.

**3. Check shard assignments:**
```bash
tezbake node client rpc get /chains/main/blocks/head/context/dal/shards?delegates=<YOUR_TZ1>
```
Expected: Your address with shard indexes.

**4. Verify on Explorus:**

Check [Explorus Consensus Ops](https://explorus.io/consensus_ops) for your baker:
- **2/2** = Attesting all slots ✅
- **0/2** = Not attesting (issue with DAL)
- **Empty** = No shards assigned or not connected

### "No Common Protocol" Errors

**Symptoms:** DAL logs show `cancelled` or `no common protocol`.

**Root Cause:** Usually the underlying L1 node, not the DAL itself.

**Solution:**
```bash
tezbake info  # Check full status
# If node has sync issues:
tezbake bootstrap-node https://snapshots.tzinit.org/mainnet/rolling --no-check
```

### DAL Connection Issues

If no peers are connecting:

1. **Upgrade TezBake:** `tezbake upgrade`

2. **Check bootstrap connection:**
   ```bash
   tezbake node client --endpoint http://127.0.0.1:10732 rpc get /p2p/gossipsub/connections | jq ".[].connection.bootstrap"
   ```
   At least one should show `true`.

3. **Restart:** `tezbake stop && tezbake start`

### Firewall/NAT

**DAL Ports:**
- **11732** — P2P (recommended to open)
- **10732** — RPC (local only)

```bash
# Allow DAL P2P if behind firewall
sudo ufw allow 11732/tcp comment "DAL P2P"
```

> DAL can work without a public IP, but connectivity improves with port forwarding.

### DAL and Ledger

Ensure **Tezos Baking app v2.5.0+** for DAL attestation support.

For complete DAL setup, see [Baking with DAL](/tezbake/tutorials/baking-with-dal/).

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
