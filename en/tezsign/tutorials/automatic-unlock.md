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

## Forgotten Unlock Password

Reflashing firmware, restoring a backup, or setting HWM does not reset the password protecting the existing keys. Do not initialize a replacement master store, delete keys, or generate new keys as a password-recovery step.

1. Check your password manager or other private password record.
2. If automatic unlock was configured, check the existing `tezsign.secret` in the signer installation **locally and privately**. A standard installation uses `/bake-buddy/signer/tezsign.secret`; custom installations may differ. The generated TezSign service script also contains the unlock password. Do not paste either file or the service environment into a support chat.
3. If you remember possible passwords, try them using the normal interactive command:

   ```bash
   tezbake tezsign unlock
   ```

An incorrect password attempt does not replace the encrypted key files. Current firmware rate-limits secured requests and may report `unlock throttled`; wait for the indicated retry time. Do not run a repeated guessing loop.

If no valid password or saved copy can be recovered, there is no password-reset command that decrypts the existing backup. Preserve the card and backups and contact the team to plan replacement consensus/companion keys through the baker's manager account. This is a key-rotation task, not a firmware or HWM repair.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
