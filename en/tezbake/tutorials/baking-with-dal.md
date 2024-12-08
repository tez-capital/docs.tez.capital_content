---
title: "Baking with DAL"
weight: 1
type: docs
summary: TezBake Baking Tutorial for DAL
---

## Preparation

For this tutorial, you'll need to have already followed one of the following tutorials:
* [How to Bake](/tezbake/tutorials/how-to-bake)
* [How to Bake on Ghostnet](/tezbake/tutorials/how-to-bake-ghostnet)

The Tezos Data Availability Layer (DAL) enhances the network's scalability by providing a decentralized and efficient way to manage large volumes of data off-chain while maintaining security guarantees. It allows rollups and other layer-2 solutions to offload data availability requirements, improving transaction throughput without congesting the main blockchain. This mechanism ensures data remains accessible and verifiable, enabling Tezos to support more complex and higher-volume applications.

Read more about the DAL [https://tezos.gitlab.io/shell/dal_overview.html](https://tezos.gitlab.io/shell/dal_overview.html).

> Setting up TezBake natively with the DAL is being worked on and will be available soon. For now, this guide explains how to run an all-in-one TezBake setup with the DAL process running separately from the TezBake processes, on the same machine or on a different machine within your network.

> Please be advised that running the DAL on the same machine as your baker may reveal your baker's IP address to the public. If you're concerned about this, you can run the DAL on a separate machine.

> If you're running your baker behind a NAT or have a restrictive firewall, you may need to open the necessary ports for the DAL to communicate with other nodes on the network. You will need to open and/or map port tcp/11732 to the machine running the DAL.

---

## DAL Installation

Before downloading the DAL binary, you need to slightly modify the TezBake configuration to include the DAL process.

### Edit app.json

   ```
   nano /bake-buddy/node/app.json
   ```

Add the following 3 lines to the `app.json` file, under the `configuration` section within the `BAKER:

   ```
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

> Note that the rest of the config above has been provided for reference. You should only add the 3 lines under `BAKER_STARTUP_ARGS`. This config in question is for a TezBake node running on Ghostnet.

### Run TezBake setup to modify the configuration

You have to run a TezBake setup operation on top of the existing setup to include the DAL process in your baker's service configuration.

   ```
   tezbake stop
   tezbake setup -a
   ```

When asked by the setup process, choose to merge your current configuration with the new one.

### Download and execute the DAL prerequisite script

   ```
   wget https://gitlab.com/tezos/tezos/-/raw/master/scripts/install_dal_trusted_setup.sh
   chmod +x install_dal_trusted_setup.sh
   ./install_dal_trusted_setup.sh
   ```

### Download the DAL binary

The DAL binaries can be found in the the Tezos Gitlab repository: [https://gitlab.com/tezos/tezos/-/releases](https://gitlab.com/tezos/tezos/-/releases).

Make sure to download the correct version of the DAL binary for your system, whether it's x86_64 or arm64.

For example:   

    ```
    wget https://gitlab.com/tezos/tezos/-/package_files/159438046/download -O octez-dal-node
    chmod +x octez-dal-node
    sudo cp octez-dal-node /usr/sbin/octez-dal-node
    ```

### Initialize the DAL configuration

   ```
   octez-dal-node config init --endpoint http://127.0.0.1:8732 --attester-profiles="tz1xxxxxxxxxxxxxxxxxxxxxx"
   ```
> Replace `tz1xxxxxxxxxxxxxxxxxxxxxx` with your baker's address.

> There are several other options you can set in the DAL configuration. You can find more information about them by running visiting the DAL documentation [https://tezos.gitlab.io/shell/dal_overview.html](https://tezos.gitlab.io/shell/dal_overview.html).

### Start the DAL process

   ```
   octez-dal-node run
   ```

### How to run the DAL process in the background with `screen`

To run the DAL process in the background, you can use the `screen` command.

   ```
   screen -S dal
   octez-dal-node run
   ```

To detach from the screen session, press `Ctrl + A` followed by `D`. To reattach to the screen session, run:

   ```
   screen -r dal
   ```
### How to run the DAL process as a service

To run the DAL process as a service, you can use `systemd`.

Create a new service file:

   ```
   sudo nano /etc/systemd/system/octez-dal-node.service
   ```
Add the following content to the file:

   ```
    [Unit]
    Description=Octez DAL Node Service
    After=network.target

    [Service]
    Type=simple
    ExecStart=/usr/sbin/octez-dal-node run
    Restart=on-failure
    User=octez
    Group=octez
    Environment=HOME=/home/octez

    [Install]
    WantedBy=multi-user.target
   ```

> Replace `octez` with the user you're running the DAL process as on the `User,` `Group` and `Environment` lines.

Reload system to read the new service file:

   ```
   sudo systemctl daemon-reload
   ```

Enable the service on boot:

   ```
   sudo systemctl enable octez-dal-node
   ```

Start the service:

   ```
   sudo systemctl start octez-dal-node
   ```

### Verify the DAL process is operational

Once running the DAL process should indicate that it's communicating with the baker process by displaying the level of the last block it received.

![DAL running](/tezbake/tutorial/tezbakedalrun.png)

If you've chosen to run the DAL as a systemd service, you can check the logs with:

   ```
   journalctl -u octez-dal-node -f
   ```

 After confirming that the DAL process is able to observe the baker's level, confirm that TezBake's baker process is properly configured and not complaining about the DAL process.

    ```
    cat /etc/systemd/system/bb-default-node-xtz-baker.service
    ```

Verify that `--dal-node` is in the configuration file on the `ExecStart` line.

Check that the TezBake baker process logs do not complain about the DAL process.

    ```
    tebake node log baker -f
    ```
The Nomadic Labs team has provided a [DAL tutorial](https://tezos.gitlab.io/shell/dal_overview.html) that explains how to further check the DAL process and its logs.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 