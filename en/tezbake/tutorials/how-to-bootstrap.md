---
title: "How to Bootstrap"
weight: 1
type: docs
summary: TezBake Bootstrapping Tutorial
---

## TezBake Bootstrapping
To bootstrap your TezBake node means to download someone else's snapshot of the blockchain and import it into your node. This is much faster than synchronizing the blockchain from scratch. There are two ways to bootstrap your node: using a tarball or using a snapshot. The tarball method is faster, but the snapshot method is more reliable and robust.

**Please be aware that bootstrapping your node could result in your nonce revelation to fail and for you to forfit your endorsement rights for the cycle.** This is because by default we're using rolling snapshots which don't export full blocks for past cycles by default. If your node baked a special kind of block during the lack cycle, it may be asked to provide its nonce to the network. To do so, it needs to have the `.tezos-client` data intact from the last cycle and it needs to have the full blocks for the last cycle.

To remedy this situation and to never risk forfitting your endorsement rights for a whole cycle, you can simply always use a snapshot that's at least 4 days (from today) old, when bootstrapping a node. This way you'll always have at least 1 cycle+ of full blocks in your node's database from the very beginning. The downside of this method is that it will slow down your bootstrap process by the amount of time it takes to download the full blocks for the past 1 cycle+, which can be up to 2 hours on some slow home networks.

### Bootstrap using a tarball (fast)

   ```
   bb-cli bootstrap-node --tarball
   ```

The `--tarball` method uses the latest snapshot from https://xtz-shots.io/mainnet/ and downloads it to your node. This is the fastest method, but it's not as secure as the snapshot method below.

### Bootstrap using a snapshot (slow, but more rebust and secure)

   ```
   bb-cli bootstrap-node <url> <block_hash>
   # example:
   bb-cli bootstrap-node https://mainnet-v15.xtz-shots.io/mainnet-3185135.rolling BL8Vq12HX6MJWkB6RLgQAYRKpKZ5fyMoLpWzAoQ6mh55gkKHiQU
   ```
   
You can get snapshots in the following places:
* https://xtz-shots.io/mainnet/
* https://snapshot-api.tezos.marigold.dev/mainnet

Verify the hash or checksum provided by the snapshot provider to ensure the snapshot is valid. You can find the correct hashes for the snapshot block on the Tezos blockchain explorers such as:
https://tzkt.io/
https://tzstats.com/

Simply search for the block number in the search field and verify the hash of the block matches the hash provided by the snapshot provider.

---

Any questions/comments/concerns please contact the Tez.Capital team on
[Discord](https://discord.gg/vykxNSnvQY) or [Telegram](https://t.me/bakebuddy) 