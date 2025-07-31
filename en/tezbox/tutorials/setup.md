---
title: "> Setup"
weight: 1
type: docs
summary: TezBox Setup Tutorial
---

{{< youtube v-X90E4JFTY >}}

## Preparation

To use TezBox, you need to have an OCI compatible container runtime installed on your machine (e.g. Docker, Podman, etc.)

To get started with Docker, you can follow the instructions on the [official Docker website](https://docs.docker.com/get-docker/).
To get started with Podman, you can follow the instructions on the [official Podman website](https://podman.io/getting-started/installation).

### General Information

* The `HOME` directory is set to `/home/tezos.`
* Running `octez-client` and other applications in the container (excluding services) will use this `HOME` directory.
* All services use `TEZBOX_HOME` as the `HOME` directory, with a default path of `/tezbox/context/data.`
* Keys imported during bootstrap are accessible from both HOME directories.

## Setup TezBox to test with Mainnet

To run the TezBox container with the Paris protocol

   ```bash
   sudo docker run -it -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

> You can customize the port the container listens on by changing the `:8732` furthest to the right to a different port number, for example `0.0.0.0:8732:12732` will present the container on port 12732.

You can also run the process in the background by adding the `-d` flag

   ```bash
   sudo docker run -d -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

To verify that the container is running successfully, you can use `octez-client` to query the node

   ```bash
   octez-client -E http://127.0.0.1:8732 rpc get /chains/main/blocks/head/header
   ```

To view all available protocols available in the TezBox container, you can run the following command

   ```bash
   sudo docker run -it --entrypoint tezbox ghcr.io/tez-capital/tezbox:tezos-v20.3 list-protocols
   ```

## Setup TezBox to test Release Candidate (RC) protocols

### Qena42

To run the TezBox container with the Qena42 protocol

   ```bash
   sudo docker run -it -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 qenabox
   ```

> You can customize the port the container listens on by changing the `:8732` furthest to the right to a different port number, for example `0.0.0.0:8732:12732` will present the container on port 12732.

You can also run the process in the background by adding the `-d` flag

   ```bash
   sudo docker run -d -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 qenabox
   ```

To view all available protocols available in the TezBox container, you can run the following command

   ```bash
   sudo docker run -it --entrypoint tezbox ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 list-protocols
   ```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 