---
title: "> How to Setup"
weight: 1
type: docs
summary: TezBox Setup Tutorial
---

## Preparation

To use TezBox, you need to have an OCI compatible container runtime installed on your machine (e.g. Docker, Podman, etc.)

To get started with Docker, you can follow the instructions on the [official Docker website](https://docs.docker.com/get-docker/).

To get started with Podman, you can follow the instructions on the [official Podman website](https://podman.io/getting-started/installation).

## Setup TezBox to test with Mainnet

To run the TezBox container with the Paris protocol

   ```
   docker run -it -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

> You can customize the port the container listens on by changing the `:8732` furthest to the right to a different port number, for example `0.0.0.0:8732:12732` will present the container on port 12732.

You can also run the process in the background by adding the `-d` flag

   ```
   docker run -d -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

To view all available protocols available in the TezBox container, you can run the following command

   ```
   docker run -it --entrypoint tezbox ghcr.io/tez-capital/tezbox:tezos-v20.3 list-protocols
   ```

## Setup TezBox to test Release Candidate (RC) protocols

### Qena42

To run the TezBox container with the Qena42 protocol

   ```
   docker run -it -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 qenabox
   ```

> You can customize the port the container listens on by changing the `:8732` furthest to the right to a different port number, for example `0.0.0.0:8732:12732` will present the container on port 12732.

You can also run the process in the background by adding the `-d` flag

   ```
   docker run -d -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 qenabox
   ```

To view all available protocols available in the TezBox container, you can run the following command

   ```
   docker run -d -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v21.0-rc4 list-protocols
   ```

## TezBox Configuration Options

### General Information

* The `HOME` directory is set to `/home/tezos.`
* Running `octez-client` and other applications in the container (excluding services) will use this `HOME` directory.
* All services use `TEZBOX_HOME` as the `HOME` directory, with a default path of `/tezbox/context/data.`
* Keys imported during bootstrap are accessible from both HOME directories.

### CI/CD Pipelines

TezBox is commonly used in CI pipelines. If you can estimate the expected duration of a specific test and want to prevent CI from getting stuck, you can use the `--timeout=<duration>` option to limit how long the instance runs. 
Supported units: `s` (seconds), `m` (minutes), `h` (hours). In case of a timeout, the container exits with an exit code of `2`.

> The timeout specifies how long the sandbox runs, excluding the bootstrap duration.

### Logs

Output from each service are stored in `/ascend/logs` within the container. To access the logs without entering the container, mount this directory from outside the container.

To mount the `/ascend/logs` directory from inside the container to your host system, use Docker's `-v` option to bind mount the directory when running the container. 

Here’s the command format:

   ```
   docker run -it -v /path/on/host:/ascend/logs --entrypoint tezbox ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```
For example:

   ```
   docker run -it -v /home/tezos/logs:/ascend/logs --entrypoint tezbox ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

### Overrides and configuration through mounted volumes

You can override any configuration file by mounting your own file to the `/tezbox/overrides` directory. The file will be merged with the default configuration. If you want to replace the whole configuration or file without merging, you can mount it to the `/tezbox/configuration` directory. The configuration is merged with the overrides and the result is stored in the `/tezbox/context` directory during the initialization of the container. 

> Array values are always replaced, not concatenated.

For example if you want to adjust block times, you can create `sandbox-override-parameters.hjson` file with the following content:

   ```
   minimal_block_delay: "1" // minimal block delay in seconds, has to be quoted
   ```

Run the container with the following command:

   ```
   # docker run -it -v <path-to-your-file>:/tezbox/overrides/protocols/<case sensitive protocol id>/sandbox-parameters.hjson ... ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   
   docker run -it -v $(pwd)/sandbox-override-parameters.hjson:/tezbox/overrides/protocols/Proxford/sandbox-parameters.hjson ... ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

You can determine path based on folder structure in https://github.com/tez-capital/tezbox/tree/main/configuration

Optionally you can mount entire overrides/configuration directory to `/tezbox/overrides` or `/tezbox/configuration` to replace the whole configuration.

   ```
   docker run -it -v <path-to-your-configuration-overrides>:/tezbox/overrides ... ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

> Do not edit or mount configuration files in the `/tezbox/context` directory. They are generated automatically and should not be modified manually.

### Accounts

By default tezbox comes with these accounts:

   ```
    {
        alice: {
            pkh: tz1VSUr8wwNhLAzempoch5d6hLRiTh8Cjcjb
            pk: edpkvGfYw3LyB1UcCahKQk4rF2tvbMUk8GFiTuMjL75uGXrpvKXhjn
            sk: unencrypted:edsk3QoqBuvdamxouPhin7swCvkQNgq4jP5KZPbwWNnwdZpSpJiEbq
            balance: 2000000
        }
        bob: {
            pkh: tz1aSkwEot3L2kmUvcoxzjMomb9mvBNuzFK6
            pk: edpkurPsQ8eUApnLUJ9ZPDvu98E8VNj4KtJa1aZr16Cr5ow5VHKnz4
            sk: unencrypted:edsk3RFfvaFaxbHx8BMtEW1rKQcPtDML3LXjNqMNLCzC3wLC1bWbAt
            balance: 2000000
        }
    }
   ```

You can add modify as needed. Just mount your own file to `/tezbox/overrides/accounts.hjson` for override or `/tezbox/configuration/accounts.hjson` for full replacement.

### Services

You can adjust service behavior by mounting your own configuration to `/tezbox/overrides/services/...` for override or `/tezbox/configuration/services/...` for full replacement.

If you want to disable a service, you can create override with `autostart: false`. 

For example to disable baker service you would crease `baker.hjson` file:

   ```
   autostart: false
   ```

Mount it into overrides directory:

   ```
   docker run -it -v $(pwd)/baker.hjson:/tezbox/overrides/services/baker.hjson ... ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

### Chain Context

Chain and protocol is automatically initialized only once during the first run. The chain and all runtime data are stored in `/tezbox/context/data` directory. If you want to persist your sandbox state just run it with mounted volume to `/tezbox/context/data` directory.

   ```
   docker run -it -v $(pwd)/sandbox-data:/tezbox -p 0.0.0.0:8732:8732 ghcr.io/tez-capital/tezbox:tezos-v20.3 parisbox
   ```

> To reset the state you can remove the `/tezbox/context/data/tezbox-initialized` file. After its removal all chain and client data will be removed and the chain will be reinitialized on the next run.

### Flextesa Compatibility

To maintain some level of compatibility with Flextesa, the `alice` and `bob` accounts are the same. The RPC port is exposed on ports 8732 and 20000. In addition, TezBox uses similar protocol aliases like `oxfordbox` and `parisbox`.

Unlike Flextesa, TezBox won't expose configuration through command line arguments. Instead you can edit the configuration directly in the configuration files or use overrides.

## Building TezBox

# To build TezBox follow these steps:

1. clone the repository
   `git clone https://github.com/tez-capital/tezbox && cd tezbox`
2. edit the Dockerfile, configuration and sources if needed
3. build lua sources (you can get eli from https://github.com/alis-is/eli/releases)
   `eli build/build.lua`
4. build the image
   `docker build --build-arg="PROTOCOLS=PsParisC" --build-arg="IMAGE_TAG=octez-v20.3" -t tezbox . -f containers/tezos/Containerfile --no-cache`

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 