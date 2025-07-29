---
title: "Baking with Consensus Key"
weight: 1
type: docs
summary: TezBake Baking Tutorial for using a Consensus Key
---

Follow along on Youtube!
{{< youtube 5m_GKFRIflk >}}

## Preparation

For this tutorial, you'll need to have already followed one of the following tutorials:
* [How to Bake](/tezbake/tutorials/baking-on-mainnet)
* [How to Bake on Ghostnet](/tezbake/tutorials/baking-on-ghostnet)

A Tezos consensus key is a cryptographic key specifically used for signing blocks and consensus operations (endorsing blocks) in the Tezos blockchain. Introduced to improve security and operational flexibility, it allows bakers (validators) to delegate block-signing responsibilities to a different key than the one associated with their primary baking account.

This separation of roles is useful for reducing the exposure of the primary baker key (which holds funds and has broader permissions) by isolating consensus-related tasks to a different key. If compromised, only the consensus operations are affected, not the funds held by the baker's main account.

> If an attacker gains control of the consensus key, they can sign blocks and endorse operations. They can maliciously double-bake or double-attest on your behalf, slashing your funds. They can also transfer all baker funds that are not locked/staked in the security deposit by using the drain operation. To eliminate the risk of fund draining by the consensus key, it is recommended to lock/stake all baking funds in the security deposit. It's further recommended to rotate the consensus key before stopping the baking operations and unstaking the security deposit.

---

## Consensus key setup

### Import the consensus key

Plug in your consensus key Ledger device and open the Tezos Baking app.

Run the following command to list the available Ledgers:

   ```
   tezbake list-ledgers
   ```

Note the 4 word ID of the Ledger you want to use for the consensus key.

Run the following command to import the consensus key:

   ```
   tezbake setup-ledger --platform --import-key="P-256/0h/0h" --authorize --ledger-id "apple-banana-coconut-date" --hwm 1 --key-alias=consensus
   ```

> Replace the `--ledger-id` value with the 4 word ID of the Ledger you want to use for the consensus key.

> We are using the P-256 (tz3) curve for the consensus key because it's the fastest on Ledger hardware and most portable option with both on premise and cloud hardware security modules (HSMs). The consensus key is only used for signing blocks and endorsements, so it doesn't need to be the same curve as the baker key. In fact, many bakers move from a tz1 key to a tz3 key for the consensus key to improve performance.

This will import your consensus key Ledger device and authorize it for baking. Leave the baking app running on the Ledger device.

### Modify the baking configuration

Open the TezBake node configuration file:

   ```
   nano /bake-buddy/node/app.json
   ``` 

Inside the `"configuration"` object, add the following key-value pair:

   ```
   "additional_key_aliases": [ "consensus" ]
   ```

Here an example of a file with the consensus key alias added:

   ```json
    {
            "configuration": {
                    "BAKER_STARTUP_ARGS": [
                            "--dal-node",
                            "http://127.0.0.1:10732/"
                    ],
                    "CONFIG_FILE": {
                            "p2p": {
                                    "bootstrap-peers": [
                                            "boot.tzbeta.net"
                                    ],
                                    "limits": {
                                            "connection-timeout": 10,
                                            "expected-connections": 40,
                                            "max-connections": 50,
                                            "max_known_peer_ids": [
                                                    320,
                                                    240
                                            ],
                                            "max_known_points": [
                                                    320,
                                                    240
                                            ],
                                            "min-connections": 20
                                    },
                                    "listen-addr": "[::]:9732"
                            },
                            "shell": {
                                    "chain_validator": {
                                            "synchronisation_threshold": 6
                                    }
                            }
                    },
                    "NODE_TYPE": "baker",
                    "VOTE_FILE": {
                            "adaptive_issuance_vote": "on",
                            "liquidity_baking_toggle_vote": "on"
                    },
                    "additional_key_aliases": [
                            "consensus"
                    ]
            },
            "id": "bb-default-node",
            "type": {
                    "id": "xtz.node",
                    "version": "latest"
            },
            "user": "bb"
    }
   ```

Re-run the TezBake setup and merge your configuration when asked:

   ```
   tezbake stop
   tezbake setup
   tezbake start
   ```

### Register the consensus key

Get your consensus key public key hash:

   ```
   cat /bake-buddy/node/data/.tezos-client/public_keys
   ```

The public key is the one in the `key` field.

> An example of a public key for a tz3 address is `p2pk66fWs9UZ6T4nVTfHfV9PtuJje4xYBh2RVo4517a8VTj6Cny7ZXY`

To register the consensus key, run the following command:

   ```
   tezbake signer client set consensus key for baker to consensus
   ```

You can also set your consensus key on TezGov via https://gov.tez.capital.

The new consensus key will become effective after 3 cycles. For example if you set your consensus key in cycle 1000, it will be effective in cycle 1003.

Once the consensus Ledger becomes effective, you can unplug your original Ledger device as it's no longer needed for baking.

### Confirm the consensus key is working

Once the consensus key becomes effective, you will see a change in the baking logs by showing the consensus key operating on blocks and endorsements on behalf of the baker key.

   ```
   Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449: injected attestation op5RtCGypnrri9FyYHy91haPWB6CpouxNABcgM7BSUmr81p27G4 for
   Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   consensus (tz3P9WvzULMuss5iDk4tjNQYWkwSrLAjUuh7)
   Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   on behalf of tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur for level 7403648, round
   Dec 18 05:14:26 bb baker[2428547]: Dec 18 05:14:26.449:   0
   ```

To view your baker logs, run the following command:

   ```
   tezbake node log baker -f
   ```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)