---
title: "Baking with Prism Tunneling"
weight: 12
type: docs
summary: Secure, low-latency tunneling between TezBake components using Prism.
---

## Command Cheatsheet

| Task                           | Command / Path                                                             |
| ------------------------------|----------------------------------------------------------------------------|
| Install Node on Remote        | `tezbake setup --node --node-remote <user>@<ip>:<port> ...`               |
| Install DAL on Remote         | `tezbake setup --dal --dal-remote <user>@<ip>:<port> ...`                 |
| Remove Local Node/DAL         | `tezbake remove --node --all`, `tezbake remove --dal --all`               |
| Generate CA                   | `tezbake <app> prism generate-ca --output=/bake-buddy/<app>/prism/keys/ca`|
| Generate Key                  | `tezbake <app> prism generate-key --ca=... --name=... --output=...`       |
| View Key Info                 | `tezbake <app> prism key-info --path=<key>.prism`                         |
| Activate Configuration        | `tezbake upgrade`                                                         |
| Start All                     | `tezbake start`                                                           |

> **⚠️ WARNING - Command Execution:**
>
> * All `tezbake` commands should be executed on the controller machine (usually the machine hosting the signer)
> * Configuration edits (`app.json`) should be performed on their respective machines (Node, DAL, or Signer)

---

## Overview

Prism is Tez Capital's in-house tunneling system for TezBake. Built on QUIC, Prism provides low-latency, resilient connections with seamless client IP migration.

**Key Benefits:**

- Lightweight and built for baking performance
- Supports node ↔ signer ↔ DAL topologies
- Easy to manage with `tezbake` CLI

This guide assumes basic familiarity with Linux, networking, and remote SSH access.

## Table of Contents

1. [Supported Topologies](#supported-topologies)
2. [Step 1: Choose Your Prism Endpoint](#step-1-choose-your-prism-endpoint)
3. [Step 2: Setup Remote Applications](#step-2-setup-remote-applications)
4. [Step 3: Edit Application Configurations](#step-3-edit-application-configurations)
5. [Step 4: Activate Configurations](#step-4-activate-configurations)
6. [Step 5: Generate and Distribute Prism Keys](#step-5-generate-and-distribute-prism-keys)
7. [Final Step: Start Everything](#final-step-start-everything)
8. [Troubleshooting](#troubleshooting)

---

## Supported Topologies

We currently support four Prism tunnel layouts:

- **Node on remote**  
  → Node acts as the public Prism endpoint.
- **DAL on remote**  
  → DAL acts as the public Prism endpoint.
- **Node and DAL on separate remotes**  
  → Node is the Prism endpoint.
- **Node and DAL on same remote**  
  → Same as node on remote; node is the endpoint.

---

## Step 1: Choose Your Prism Endpoint

In every configuration, you need one "public" Prism endpoint — an app that listens for incoming encrypted Prism traffic.

| Layout                        | Public Prism Endpoint |
|------------------------------|------------------------|
| Node on Remote               | Node                   |
| DAL on Remote                | DAL                    |
| Node & DAL on Remote         | Node                   |
| Node & DAL on Separate Hosts | Node                   |

---

## Step 2: Setup Remote Applications

Use the `tezbake setup` command with `--remote` flags to install apps directly to remote machines.

Example for DAL:

```bash
tezbake setup --dal \
  --dal-remote user@192.168.1.50:22 \
  --dal-remote-auth key:/path/to/ssh/key \
  --dal-remote-elevate sudo
```

Example for Node:

```bash
tezbake setup --node \
  --node-remote user@192.168.1.60:22 \
  --node-remote-auth key:/path/to/ssh/key
  --node-remote-elevate sudo
```

> **💡 TIP:** You can combine both in a single command.
>
> **ℹ️ INFO:** If you've previously installed node or dal locally, remove it first:

```bash
tezbake remove --node --all
tezbake remove --dal --all
```

> **ℹ️ INFO:** During remote setup, TezBake injects its own SSH keys into the remote machine for secure automation (setup, upgrade, info, etc.).

---

## Step 3: Edit Application Configurations

### If Node is Public Prism Endpoint

**Node's `app.json`:**

```yaml
{
  "configuration": {
    # ...
    "PRISM": {
      "listen": "0.0.0.0:20080",
      "dal": true,
      "signer": true
    }
    # ...
  }
  # ...
}
```

**DAL's `app.json`:**

```yaml
{
  "configuration": {
    # ...
    "PRISM": {
      "remote": "<node-ip>:20080",
      "node": true
    }
    # ...
  }
  # ...
}
```

**Signer's `app.json`:**

```yaml
{
  "configuration": {
    # ...
    "PRISM": {
      "remote": "<node-ip>:20080",
      "node": true
    }
    # ...
  }
  # ...
}
```

---

### If DAL is Public Prism Endpoint

**DAL's `app.json`:**

```yaml
{
  "configuration": {
    # ...
    "PRISM": {
      "listen": "0.0.0.0:20080",
      "node": true
    }
    # ...
  }
  # ...
}
```

**Node's `app.json`:**

```yaml
{
  "configuration": {
    # ...
    "PRISM": {
      "dal_remote": "<dal-ip>:20080",
      "dal": true
    }
    # ...
  }
  # ...
}
```

> **ℹ️ INFO:** Signer does not require a Prism section in this layout.

---

## Step 4: Activate Configurations

Run:

```bash
tezbake upgrade
```

This will apply your updated `app.json` configurations and prepare the runtime.

Make sure UDP port `20080` (or your configured Prism port) is open on the public endpoint.

---

## Step 5: Generate and Distribute Prism Keys

To ensure encrypted and authenticated communication between components, generate a Prism CA and keys on a secure machine — usually the controller/signer host.

> **ℹ️ INFO: Key Generation:**
>
> * Only applications with a `PRISM` configuration in their `app.json` file can generate keys
> * You must generate ALL `.prism` keys from the same `<app>` to ensure compatibility and proper authentication across components
> * Using different `<app>` values for key generation can lead to connection failures

```bash
mkdir -p /bake-buddy/<app>/prism/keys/
```

Generate the Certificate Authority:

```bash
tezbake <app> prism generate-ca \
  --output=/bake-buddy/<app>/prism/keys/ca
```

Then generate identity keys:

```bash
tezbake <app> prism generate-key \
  --ca=/bake-buddy/<app>/prism/keys/ca \
  --name=tezos-node \
  --output=/bake-buddy/<app>/prism/keys/node

tezbake <app> prism generate-key \
  --ca=/bake-buddy/<app>/prism/keys/ca \
  --name=tezos-dal \
  --output=/bake-buddy/<app>/prism/keys/dal

tezbake <app> prism generate-key \
  --ca=/bake-buddy/<app>/prism/keys/ca \
  --name=tezos-signer \
  --output=/bake-buddy/<app>/prism/keys/signer
```

> **You can validate the keys with:**

```bash
tezbake <app> prism key-info --path=<key>.prism
```

You should see output similar to the following:

```bash
Common Name: tezos-<app>
DNS Names: [tezos-<app>]
Extended Key Usage:
  - Server Authentication
  - Client Authentication
```

---

### Key Distribution

Manually copy the generated `.prism` keys to the correct app directories:

| Key               | Destination                                         |
|------------------|-----------------------------------------------------|
| `signer.prism`    | `/bake-buddy/signer/prism/keys/signer.prism`       |
| `node.prism`      | `/bake-buddy/node/prism/keys/node.prism`         |
| `dal.prism`       | `/bake-buddy/dal/prism/keys/dal.prism`          |

> **🚨 CRITICAL:**
>
> * Do NOT copy or expose the CA file
> * If the CA is compromised, regenerate ALL keys with a new CA immediately

After distributing the keys to their respective locations, you can verify each application's key information using the following commands:

For the Node key:

```bash
tezbake node prism key-info --path=/bake-buddy/node/prism/keys/node.prism
```

For the DAL key:

```bash
tezbake dal prism key-info --path=/bake-buddy/node/prism/keys/dal.prism
```

If the Node is the public Prism endpoint, use this command for the Signer key:

```bash
tezbake signer prism key-info --path=/bake-buddy/node/prism/keys/signer.prism
```

---

## Final Step: Start Everything

Once all keys and configs are in place, run:

```bash
tezbake start
```

TezBake will initialize all services and Prism tunnels. You should now have a secure, low-latency connection across your baking infrastructure.

---

## Troubleshooting

- Use `tezbake info` and `tezbake <app> log -f prism` to diagnose issues. These commands provide valuable insights into the system's state.
- If Prism cannot establish a connection, verify that the firewall is not blocking the required port. Ensure that the UDP port (`20080` in this guide) is open on the `public endpoint` — the machine configured with the `listen` directive under the `PRISM` section in `app.json`.
- If you see the log message `failed to verify certificate`, it indicates that the certificates are not from the same CA. Use the `key-info` commands mentioned in the **Key Distribution** section to verify the keys and ensure they share the same `CA` field.
- If everything appears to be working but baking is still not happening, review the certificate's CN field. Ensure it matches the respective `tezos-<app>`. For example, for the signer, the CN field should display `tezos-signer`.

---

## Related Guides

* [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) - Hardware signer setup
* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Standard single-machine setup
* [Troubleshooting](/tezbake/tutorials/troubleshooting/) - Fix common issues

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
