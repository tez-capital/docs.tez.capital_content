---
title: "Automatic Unlock"
weight: 8
type: docs
summary: Store the TezSign password so TezBake can unlock TezSign automatically
---

## When to Use This

Use this only after your TezSign setup is working and you understand the security tradeoff. Automatic unlock stores the TezSign password so TezBake can unlock the device after restarts.

## Enable Automatic Unlock

Run:

```bash
tezbake setup-tezsign --password
```

Enter the TezSign master/key decryption password when prompted.

Apply the updated signer configuration:

```bash
tezbake upgrade --signer
```

Restart if needed:

```bash
tezbake stop
tezbake start
```

## Disable Automatic Unlock

Run the same command and submit an empty password when prompted:

```bash
tezbake setup-tezsign --password
tezbake upgrade --signer
```

## Verify

```bash
tezbake tezsign status
tezbake info --signer
```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
