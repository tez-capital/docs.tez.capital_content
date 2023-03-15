---
title: "How to Update"
weight: 1
type: docs
summary: TezBake Updating Tutorial
---

## TezBake Updating
The TezBake software consists of four components:
1. ami - https://github.com/alis-is/ami https://github.com/alis-is/ami
2. eli -  Lua Interpreter & Essential libraries for simple cross platform scripting - https://github.com/alis-is/eli 
3. bb-cli - Command line interface for setting and monitoring your baker, using the help of the 2 tools above
4. Octez binaries - Tezos node binaries published by the Tezos core developers - https://gitlab.com/tezos/tezos/-/releases

We regularly update all parts of the TezBake stack and the Tezos core developments regularly publish new Octez versions and protocols.  All mandatory (to keep baking) and optional updates are posted across Tez.Capital social media channels.  Updates must be performed manually at this time, to ensure the Tezos node operator is the only person able to control their baking operation.

There are three upgrade method that are supported depending on what the Tezos node operator wants to do:
1. Update Octez binaries only (#4 only)
2. Update ami & eli and Octez binaries (#1, #2, #4)
3. Update the entire TezBake software stack (#1, #2, #3, #4)

### What is the difference between the three upgrade methods?

Using the first upgrade method is recommended when one only wants to change or update the Tezos binaries, in cases such as:
* Tezos protocol forkless update preparation
* Removal of stale protocol after forkless update
* Updating from old Octez version to new Octez version, while continuing to run the same Tezos protocol

Using the second upgrade method is recommended for the same reasons as above, as well as:
* Getting the latest ami & eli fixes and improvements

Using the third upgrade method is recommended for all the same reasons listes so far, as well as:
* Getting the latest bb-cli improvements and fixes

### Which TezBake upgrade method should I use?

We will sometimes specify which upgrade method to use by referencing its number.  If a number is not specified, use the guidelines below to determine which method to use:
* If you'd like to be as thorouh as possible and ensure you have the latest of everything, use the #3 method.  Using this method is not necessary if there has not been a new bb-cli version.
* If you want to get most improvements and fixes from the TezBake side as well as from the Tezos core dev side, use the #2 method.  This is the recommended option for most circumstances.
* If you just want to keep baking on Tezos or get the latest Octez improvements without modifying any other components, use the #1 method

## Update Octez binaries only (#1)

   ```
   bb-cli upgrade
   ```

## Update ami & eli and Octez binaries (#2)

   ```
   bb-cli upgrade -a
   ```

## Update the entire TezBake software stack (#3)
First update your bb-cli binary to the latest version:

   ```
   # If you have a regular Intel or AMD computer that's not "ARM" based:
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/bb-cli-linux-amd64 && chmod +x bb-cli-linux-amd64
   sudo mv bb-cli-linux-amd64 /usr/sbin/bb-cli
   # If you have an ARM based computer such as a Raspberry Pi:
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/bb-cli-linux-arm64 && chmod +x bb-cli-linux-arm64
   sudo mv bb-cli-linux-arm64 /usr/sbin/bb-cli
   ```
Then update the rest of the TezBake software stack:

   ```
   bb-cli upgrade -a
   ```

## What should I do after updating?
After all updates and changes to your Tezos node, always ensure your baking process continues successfully by monitoring its performance on https://TzStats.com and https://TzKT.io.

---

Any questions/comments/concerns please contact the Tez.Capital team on
[Discord](https://discord.gg/vykxNSnvQY) or [Telegram](https://t.me/bakebuddy) 