---
title: "> How to Bake with Peak GUI"
weight: 1
type: docs
summary: TezBake Baking Tutorial for GUI Users
---

## Preparation

For this tutorial, you'll need to have already followed one of the following tutorials:
* [How to Bake](/tezbake/tutorials/how-to-bake)
* [How to Bake on Ghostnet](/tezbake/tutorials/how-to-bake-ghostnet)

The TezPeak GUI is a graphical user interface for TezBake, which is a command-line tool for baking and endorsing Tezos blocks. It's a great way to get started with baking and endorsing without having to use the command line on a day-to-day basis.

---

## Installation

### Download and install TezPeak via TezBake

   ```
   tezbake setup --peak
   ```

### Setup Peak Configuration

    ```
    cd /bake-buddy/peak/ && touch config.hjson
    ```

Open the `config.hjson` file with your favorite text editor. 

Here's an example of a `config.hjson` file:

    ```hjson
    {
	    id: BakingBenjamins
	    listen: "0.0.0.0:8733"
	    bakers: [
	    	tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur
	    ]
    }
    ```
This configuration file names the baker "BakingBenjamins", listens to anyone trying to connect to the TezPeak GUI web interface on port 8733, and specifies the baker's address.

> Change `id:` to have your baker's name and `bakers:` to have your baker's address. 

You can also make TezPeak GUI only available on the local computer if you have an all-in-one setup (i.e. the baker and the GUI are on the same computer which has a graphical user interface):

    ```hjson
    {
	    id: BakingBenjamins
	    listen: "127.0.0.1:8733"
	    bakers: [
	    	tz1S5WxdZR5f9NzsPXhr7L9L1vrEb5spZFur
	    ]
    }
    ```

Here's the TezPeak minimal configuration, showing you can also have multiple bakers:

    ```hjson
    {
        listen: "0.0.0.0:8733"
        bakers: [
            tz1P6WKJu2rcbxKiKRZHKQKmKrpC9TfW1AwM
            tz1hZvgjekGo7DmQjWh7XnY5eLQD8wNYPczE
        ]
    }
    ```

Here's the TezPeak configuration with all available options:

    ```hjson
    {
        # Id to show in the header
        id: ""
        # Address to listen on
        listen: 127.0.0.1:8733
        # List of bakers to monitor
        bakers: [
        ]
        # Baker's node to connect to
        node: http://localhost:8732
        # List of reference nodes to connect to
        # The reference nodes are used to get the rights and blocks if the baker's node is not available
        reference_nodes: {
            "Tezos Foundation": {
                address: https://rpc.tzbeta.net/
                is_rights_provider: true
                is_block_provider: false
            },
            "tzkt": {
                address: https://rpc.tzkt.io/mainnet/
                is_rights_provider: false
                is_block_provider: true
            }
        }
        # The mode tezpeak should operate in
        # auto - if bound to localhost, it will operate in private mode if not, it will operate in public mode
        # public - assumes public environment, only readonly operations are allowed
        # private - assumes private environment, all operations are allowed
        mode: auto
        # The number of blocks to look for the rights
        # 50 means 25 blocks in the past and 25 blocks in the future
        block_window: 50
    }
    ```

    ---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 