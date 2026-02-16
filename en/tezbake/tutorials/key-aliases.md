---
title: "Key Aliases"
weight: 7
type: docs
summary: "How to configure additional key aliases for consensus keys, DAL companion keys, and multi-key setups"
---

# Key Aliases

When baking with TezBake, the baker automatically injects your default baker key. If you use additional keys — such as a separate consensus key or a DAL companion key — you need to register them as key aliases so the baker knows about them.

There are two approaches:

| Method | Behavior | Use when... |
|--------|----------|-------------|
| `additional_key_aliases` | Adds keys **on top of** the default baker key | You want to keep the default baker key and add extra keys |
| `key_aliases` | **Replaces** the default key list entirely | You need full control over which keys are injected |

---

## Recommended: `additional_key_aliases`

This is the simplest and safest approach. Your default baker key continues to be injected automatically, and the aliases you specify are added alongside it.

### Add aliases

```bash
tezbake node modify --set configuration.additional_key_aliases '["bb1_consensus","bb1_dal"]'
```

### View current aliases

```bash
tezbake node show configuration.additional_key_aliases
```

### Remove a specific alias

```bash
tezbake node modify --remove configuration.additional_key_aliases '"bb1_dal"'
```

### Clear all additional aliases

```bash
tezbake node modify --unset configuration.additional_key_aliases
```

After any change, restart your baker:

```bash
tezbake stop && tezbake start
```

---

## Advanced: `key_aliases`

Use `key_aliases` when you need **complete control** over which keys the baker uses.

> **⚠️ WARNING:** When you set `key_aliases`, the default baker key is **NOT** automatically included. You must add it explicitly if you still want it injected.

### Set key aliases (replaces defaults)

```bash
tezbake node modify --set configuration.key_aliases '["bb1_consensus","bb1_dal"]'
```

### Add a single alias

```bash
tezbake node modify --add configuration.key_aliases '"bb2_dal"'
```

### Remove a single alias

```bash
tezbake node modify --remove configuration.key_aliases '"bb2_dal"'
```

### View current aliases

```bash
tezbake node show configuration.key_aliases
```

### Clear and return to default behavior

```bash
tezbake node modify --unset configuration.key_aliases
```

After any change, restart your baker:

```bash
tezbake stop && tezbake start
```

---

## Which Should I Use?

| Scenario | Method |
|----------|--------|
| Adding a consensus key alongside your baker | `additional_key_aliases` |
| Adding a DAL companion key alongside your baker | `additional_key_aliases` |
| Running multiple keys and need full control | `key_aliases` |
| Simple single-key baking setup | Neither — TezBake handles it automatically |

**Rule of thumb:** If you want the baker to keep working as normal and just need to add a key, use `additional_key_aliases`. Only use `key_aliases` if you have a specific reason to override the default key injection behavior.

---

## Migration from `additional_key_aliases.list`

> **⚠️ DEPRECATED:** The file-based method (`/bake-buddy/node/additional_key_aliases.list`) is deprecated. Migrate to the CLI-based configuration described above.

If you currently have an `additional_key_aliases.list` file:

1. Note the aliases in your file
2. Set them via CLI:
   ```bash
   tezbake node modify --set configuration.additional_key_aliases '["alias1","alias2"]'
   ```
3. Delete the old file:
   ```bash
   rm /bake-buddy/node/additional_key_aliases.list
   ```
4. Restart:
   ```bash
   tezbake stop && tezbake start
   ```

---

## Common Examples

### Consensus key + DAL companion

```bash
tezbake node modify --set configuration.additional_key_aliases '["consensus","companion"]'
```

### Consensus key only (already on tz4/BLS)

If your consensus key is imported under the default `baker` alias, you only need to add the companion:

```bash
tezbake node modify --set configuration.additional_key_aliases '["companion"]'
```

### Verify everything is set

```bash
tezbake node show configuration.additional_key_aliases
tezbake info
```

---

## Related Guides

- [Baking with Consensus Key](/tezbake/tutorials/baking-with-consensus-key/) — Full consensus key setup
- [Baking with TezSign](/tezbake/tutorials/baking-with-tezsign/) — Hardware signing with multiple keys
- [Baking with DAL](/tezbake/tutorials/baking-with-dal/) — DAL node and companion key setup
