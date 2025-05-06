---
title: "> TezBake Prism Tunneling Setup"
weight: 2
type: docs
summary: Secure, low-latency tunneling between TezBake components using Prism.
---

## Command Cheatsheet

| Task                           | Command / Path                                                             |
| ------------------------------|----------------------------------------------------------------------------|
| Install Node on Remote        | `tezbake setup --node --node-remote <user>@<ip>:<port> ...`               |
| Install DAL on Remote         | `tezbake setup --dal --dal-remote <user>@<ip>:<port> ...`                 |
| Remove Local Node/DAL         | `tezbake remove --node`, `tezbake remove --dal`                           |
| Generate CA                   | `tezbake <app> prism generate-ca --output=/bake-buddy/<app>/prism/keys/ca`|
| Generate Key                  | `tezbake <app> prism generate-key --ca=... --name=... --output=...`       |
| View Key Info                 | `tezbake <app> prism key-info --path=<key>.prism`                         |
| Activate Configuration        | `tezbake upgrade`                                                         |
| Start All                     | `tezbake start`                                                           |

---

## Overview

Prism is Tez Capital's in-house tunneling system for TezBake. Built on QUIC, Prism provides low-latency, resilient connections with seamless client IP migration.

**Key Benefits:**
- Lightweight and built for baking performance
- Supports node ↔ signer ↔ DAL topologies
- Easy to manage with `tezbake` CLI

This guide assumes basic familiarity with Linux, networking, and remote SSH access.

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
```

> **Tip:** You can combine both in a single command.  
> If you've previously installed node or dal locally, remove it first:
```bash
tezbake remove --node
tezbake remove --dal
```

> **Note:** During remote setup, TezBake injects its own SSH keys into the remote machine for secure automation (setup, upgrade, info, etc.).

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
      "node": true
    }
    # ...
  }
  # ...
}
```

> **Note:** Signer does not require a Prism section in this layout.

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

---

### Key Distribution

Manually copy the generated `.prism` keys to the correct app directories:

| Key               | Destination                                         |
|------------------|-----------------------------------------------------|
| `signer.prism`    | `/bake-buddy/signer/prism/keys/signer.prism`       |
| `node.prism`      | `/bake-buddy/signer/prism/keys/node.prism`         |
| `dal.prism`       | `/bake-buddy/signer/prism/keys/dal.prism`          |

> ⚠️ **Do not copy or expose the CA file.**  
> If compromised, regenerate all keys with a new CA.

---

## Final Step: Start Everything

Once all keys and configs are in place, run:

```bash
tezbake start
```

TezBake will initialize all services and Prism tunnels. You should now have a secure, low-latency connection across your baking infrastructure.

---

## Need Help?

- [Discord](https://discord.gg/cVGMA4MaNM)  
- [Telegram](https://t.me/tezcapital)
