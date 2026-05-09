---
title: "Advanced Configuration"
weight: 1
type: docs
summary: Advanced TezBake CLI configuration and customization options
---

# Advanced Configuration

TezBake stores runtime settings under the node configuration. Advanced users can inspect and customize those values with `tezbake node show` and `tezbake node modify`.

## Customize node startup arguments

Use `configuration.STARTUP_ARGS` to pass additional arguments to the Octez node process.

For example, to set the node synchronization threshold to `0`:

```bash
tezbake node modify configuration.STARTUP_ARGS '["--synchronisation-threshold=0"]'
```

Restart TezBake after changing startup arguments so the node starts with the new configuration:

```bash
tezbake restart
```

You can verify the configured startup arguments with:

```bash
tezbake node show configuration.STARTUP_ARGS
```
