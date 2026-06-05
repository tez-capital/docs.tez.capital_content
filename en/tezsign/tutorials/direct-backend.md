---
title: "Direct TezSign Backend"
weight: 7
type: docs
summary: Configure TezBake to bake directly through the TezSign backend
---

## When to Use This

Use this only after your TezSign keys are active and stable. Avoid switching to the direct TezSign backend during the initial 3-cycle activation window.

Before:

```text
octez-node -> octez-signer -> tezsign
```

After:

```text
octez-node -> tezsign
```

The direct backend can reduce latency by bypassing the standard `octez-signer`.

## Enable the Direct Backend

Use the signer configuration CLI. You do not need to edit `/bake-buddy/signer/app.json` manually.

```bash
tezbake signer modify configuration.BACKEND tezsign
tezbake signer show configuration.BACKEND
tezbake upgrade --signer
tezbake start --signer
```

The `show` command should confirm that `configuration.BACKEND` is set to `tezsign` before you apply and restart the signer.

## Revert to the Default Backend

Unset the direct backend configuration, confirm it is no longer set to `tezsign`, then apply and restart:

```bash
tezbake signer modify --unset configuration.BACKEND
tezbake signer show configuration.BACKEND
tezbake upgrade --signer
tezbake start --signer
```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
