---
title: "Baking with Docker"
weight: 1
type: docs
summary: TezBake Baking Tutorial for Docker Users
---

## Preparation

For this tutorial, you'll need to have already have installed Docker as shown here: <https://docs.docker.com/engine/install>

---

## Pull and Execute the TezBake Docker Image

### Pull the TezBake Docker Image

   ```bash
   sudo docker pull ghcr.io/tez-capital/tezbake:latest-alpine
   ```

### Setup TezBake Container

   ```bash
   sudo docker run --name tezbake-container --privileged -d ghcr.io/tez-capital/tezbake:latest-alpine
   ```

> After this step, you will need to wait a while for the container to start up and bootstrap the Tezos node.

You can monitor the progress of the Tezos node bootstrapping by running the following command:

   ```bash
   sudo docker logs -f tezbake-container
   ```

Once the Tezos node has finished bootstrapping, you can connect into the container and either import your Ledger wallet or create a new soft wallet.

### Connect to TezBake Container

   ```bash
   sudo docker exec -it tezbake-container /bin/sh
   ```

### Import Ledger key or soft key and register as baker

Now that your node is in full sync, you can proceed with the most important part: (1) your baker parameters import into your baker node and (2) submit your baker registration on the blockchain.

You have the option to use the secure Ledger hardware wallet or simply use a local, unencrypted software key (a.k.a. soft key). The secure Ledger hardware wallet is the recommended option for mainnet baking.

You will have to first fund your baker address with enough tez (6000 minimum) to cover the bond requirement. You can do this by sending tez from your main account or exchange to the baker address.

#### (Option 1 - RECOMMENDED) Import Ledger key to TezBake signer

   ```bash
   tezbake setup-ledger --platform --import-key --authorize --hwm 1

   # If you have a custom derivation path, you can specify it as shown: (`--import-key="ed25519/0h/0h"`; change ed to bip as needed for your individual needs; the default is ed25519/0h/0h which works just fine)
   # `--hwm 1` works great if you're setting up for the first time. If you're setting up a device that's been used to bake before, you want to change this (`1`) to the current block height on the blockchain for your safety.
   # If you're importing for the second time after already trying again but failing, you can use `--force` to force the import.
   ```

> Once imported, you can see your baker address by running `tezbake info`
> The ledger will ask you twice to confirm this operation. Make sure the baker you see on the ledger screen matches the one you want to use. If you don't have this information yet, don't worry. To get the address of the ledger that's used by default simply go to <https://kukai.app> and login with ledger, accepting the default derivation path.
> BLS (i.e. bip) signatures are designed to offer greater flexibility and scalability for certain applications compared to the default ED25519 algorithm.
> Putting the baker on a non-default derivation path provides an additional layer of security for your baker at the cost of extra complexity for you. Make sure your setup is clearly documented for your own records.
> If your device was used to bake before it might have a "high watermark" aka HWM. If you try to use this device on a testnet, it will not work because the block height on test networks usually starts with 1 while mainnet is up to over a couple of million blocks at the time of writing.
If you used to bake on mainnet with the same ledger as you're trying to use now but it's been a while, it's highly recommended to change the 1 above to the current block on the network that will be used for the device going forward.
> The watermark is simply a record of the lack block number your ledger helped to bake or attest. If you're setting up a brand new device that's not been used for baking before, there is no need to alter the default command above.
> Always make sure you're not accidentally going to double bake by using your production ledger and/or setup to bake on a testnet. It's really easy to make this mistake and the only thing preventing it are your personal standard operating procedures, the documentation you keep, and the care you take when setting up your baker.
> To double bake or attest due to baker setup error means having 2 different bakers with the same key on the same network. This is a serious offense and can lead to loss of bond and other penalties. Always double-check your setup and make sure you're not accidentally double baking or attesting.

#### (Option 2 - INSECURE) Import Soft key to TezBake signer

First, generate the baker key for TezBake signer:

   ```bash
   tezbake signer - gen keys baker
   ```

Make sure to backup your key in a secure location. You can get the key by running the following command:

   ```bash
   cat /bake-buddy/signer/data/.tezos-signer/secret_keys
   ```

Then, import the baker public key hash to TezBake node:

Get the tz1-tz3 address which is the hashed public key of the baker key:

   ```bash
   cat /bake-buddy/signer/data/.tezos-signer/public_key_hashs
   ```

Import the hashed key to the TezBake node:

   ```bash
   tezbake node client import secret key baker http://127.0.0.1:20090/tz1bcSYEMKBoMnsACXzixn5bmzcdYjagqjZF
   ```

> Change the tz1bcSYEMKBoMnsACXzixn5bmzcdYjagqjZF to the hashed key you got from the previous command.

Finally, change the permissions of the newly generated keys to be readable by the ascend user and group which runs the TezBake node:

   ```bash
   chown -R ascend:ascend /bake-buddy/
   ```

#### Register Ledger key as baker on the Tezos blockchain

For this step your node level must be synced with the latest block on the blockchain explorer. You must also temporarily open your Ledger Tezos Wallet app to register your key as a baker (__note__: as well as when voting). For all other baker operations, you must use the Tezos Baking app.

   ```bash
   tezbake register-key
   ```

> Registering is not necessary if this is already an active baker ledger which is being setup on some kind of failover machine or in a situation where it has not been over 2 weeks of baking inactivity.
> Registering applies to new bakers and to inactive bakers. If you're setting up a new baker, you must register it. If you're setting up a baker that's been inactive for over 2 weeks, you must register it. If you're setting up a baker that's been inactive for less than 2 weeks, you don't need to register it. The best way to find out if you need to register your baker again is to look into your baking rights schedule and see if they stopped coming in. If they did, you need to register your baker again.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
