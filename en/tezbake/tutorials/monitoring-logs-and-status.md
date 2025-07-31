---
title: "Monitoring Logs and Status"
weight: 1
type: docs
summary: TezBake Monitoring Tutorial
---

Follow along on Youtube!
{{< youtube tqqLNBo1OeE >}}

## Monitoring
TezBake gives you the option to monitor all Tezos node and baking services. TezBake utilizes the journalctl utility included in most Linux distribution to store and manage its logging.  You can extract all logs for a node easily to diagnose any issues. This tutorial will walk you through the process of monitoring your TezBake node.

---

## Monitor overall TezBake status
Run this command to get the status of your baker in a snapshot. After first setting up and starting the baker, you may need to give it a minute or 2 before the status display stops changing and missing some information. If you notice there is an error status on any of the fields after around 3-5 minutes of starting the services, seek out help in the Tez Capital community as there is something that probably needs tweaking.

   ```bash
   tezbake info
   ```

![<Info screen showing status of node services and Ledger>](/tezbake/tutorial/tezbakeInfo.png) 

## Monitor TezBake logs

### Monitor full TezBake node and baker logs in real time
Using the command below shows everything your baker is doing on a second-by-second basis. If there is a problem to be discovered, it will be mentioned here.

   ```bash
   tezbake node log -f
   ```

![<TezBake node log with all blocks synchronized and all attempted baking and endorsing work>](/tezbake/tutorial/tezbakeNodeLogF.png)

### Monitor limited TezBake node or baker logs in real time
Using these commands you can focus on just the node, just the baking/endorsing process or just the accusation (of double baking) processes

   ```bash
   tezbake node log node -f
   tezbake node log baker -f
   tezbake node log accuser -f
   ```

Monitoring just the `node` process shows blocks being synchronized in real time by your node.

Monitoring just the `baker` process shows all attempts to endorse and bake blocks. The baker process works along with the node process to inject blocks and endorsements into the Tezos network.

Monitoring just the `accuser` process shows the attempts of your node to call our double baking events on the Tezos network. Normally you should not see anything in this log.

### Monitor TezBake baker logs for the next protocol
During times when Tezos protocol are being switched out you will notice that there is a `baker` process as well as a `baker-next` process. The same is true about the `accuser` process. The `baker-next` process is the baker process that will be used when the next protocol is activated. The `baker` process will be used until the next protocol is activated. The `accuser-next` process is the accuser process that will be used when the next protocol is activated. The `accuser` process will be used until the next protocol is activated.

![<TezBake -next protocol services>](/tezbake/tutorial/tezbakeNext.png)

   ```bash
   tezbake node log baker-next -f
   tezbake node log accuser-next -f
   ```

The `baker-next` process will show one of two things.

1. Protocol is waiting to start. This means everyone is waiting for the new proposal to be activated on mainnet. Nothing needs to be done until the new protocol is activated
2. The baker process is running like normally. This means the new proposal has already activated and the TezBake needs to be updated to reflect it. There is no penalty for not updating unless you do not update in time for the next proposal. In other words, updating is only necessary when a proposal switch is about to happen. We still recommend that all Tezos bakers update their TezBake to the latest production version as soon as possible to get the latest Octez improvements.

### Monitor full TezBake signer logs in real time
Using the command below shows everything your Ledger is signing or trying to sign. If there is a hardware wallet issue, this log will confirm it.

   ```bash
   tezbake signer log -f
   ```

![<TezBake signer log with all work that node is attempting to have signed by the Ledger>](/tezbake/tutorial/tezbakeSignerLogF.png)

## General Linux monitoring

All TezBake logs are stored in the `/var/log/syslog` file. You can view the logs by running the commands below. You can also use tezbake itself to view the logs in real time.

   ```bash
   cat /var/log/syslog | grep tezbake
   ```
   
   ```bash
   tail -f /var/log/syslog | grep tezbake
   ```

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 