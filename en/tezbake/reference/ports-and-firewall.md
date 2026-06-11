---
title: "Ports and Firewall"
weight: 2
type: docs
summary: Network ports used by TezBake, DAL, Prism, and TezPeak
---

Use this page as a quick firewall reference for a TezBake baker host. Keep RPC and dashboard ports private unless you intentionally protect them with a VPN, reverse proxy, or other access control.

## Common Ports

| Port | Protocol | Component | Exposure |
|------|----------|-----------|----------|
| `9732` | TCP | Tezos node P2P | Public inbound recommended |
| `8732` | TCP | Tezos node RPC | Local/private only |
| `11732` | TCP | DAL node P2P | Public inbound recommended |
| `10732` | TCP | DAL node RPC | Local/private only |
| `20080` | UDP | Prism tunnel endpoint | Open only on the configured Prism public endpoint |
| `8733` | TCP | TezPeak web UI | Local/private unless protected |

## Suggested Firewall Rules

For a basic Linux host using `ufw`, open the public P2P ports:

```bash
sudo ufw allow 9732/tcp comment "Tezos P2P"
sudo ufw allow 11732/tcp comment "DAL P2P"
```

If this host is the public endpoint for Prism, open the configured Prism UDP port:

```bash
sudo ufw allow 20080/udp comment "TezBake Prism"
```

Do not expose `8732`, `10732`, or `8733` directly to the public internet unless you have added separate access controls.

## Related Guides

* [Hardware Requirements](/getting-started/hardware-requirements/) - Base network requirements
* [Baking with Prism](/tezbake/tutorials/baking-with-prism/) - Remote component tunnels
* [TezPeak Setup](/tezpeak/tutorials/setup/) - Dashboard access
* [TezBake Troubleshooting](/tezbake/tutorials/troubleshooting/) - DAL connectivity checks

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
