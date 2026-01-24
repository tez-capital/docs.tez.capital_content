---
title: "Baking With Multiple Instances"
weight: 15
type: docs
summary: How to manage and switch between multiple TezBake instances.
---

## Overview

TezBake supports managing multiple independent instances on a single machine. This is useful for running multiple networks (e.g., Mainnet and Ghostnet) or maintaining separate configurations.

The `tezbake instance` command (alias `i`) allows you to interact with these instances easily.

## Command Cheatsheet

| Task                           | Command                                       |
| ------------------------------|-----------------------------------------------|
| List Instances                | `tezbake instance list`                       |
| Run Command on Instance       | `tezbake instance <alias> <command>`          |
| Example: Start 'ghostnet'     | `tezbake instance ghostnet start`             |
| Example: Check Log            | `tezbake instance ghostnet node log -f`       |

---

## Listing Instances

To see all available instances:

```bash
tezbake instance list
```

This will display a list of all named instances found in `/bake-buddy/instances` as well as the `default` instance if one exists in the root `/bake-buddy` directory.

## Running Commands

All `tezbake` commands available for the default instance can be run on a named instance by prefixing them with `tezbake instance <alias>`.

**Syntax:**

```bash
tezbake instance <alias> <any-tezbake-command>
```

Alternatively, if you run:

```bash
tezbake instance <alias>
```

you will enter an interactive environment for that instance. All subsequent commands will be executed for the given instance only, until you exit the environment by typing `exit`.

**Examples:**

Start the 'ghostnet' instance:
```bash
tezbake instance ghostnet start
```

Upgrade the 'mainnet' instance:
```bash
tezbake instance mainnet upgrade
```

View logs for the node on 'testnet' instance:
```bash
tezbake instance testnet node log
```

This acts exactly as if you were running `tezbake ...` but targets the specific instance's directory and configuration.
