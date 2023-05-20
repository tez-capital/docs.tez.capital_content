---
title: "How to Update"
weight: 1
type: docs
summary: TezBake Updating Tutorial
---

## TezBake Updating
The TezBake software consists of four components:
* ami - https://github.com/alis-is/ami https://github.com/alis-is/ami
* eli -  Lua Interpreter & Essential libraries for simple cross platform scripting - https://github.com/alis-is/eli 
* bb-cli - Command line interface for setting and monitoring your baker, using the help of the 2 tools above
* Octez binaries - Tezos node binaries published by the Tezos core developers - https://gitlab.com/tezos/tezos/-/releases

We regularly update all parts of the TezBake stack and the Tezos core developments regularly publish new Octez versions and protocols.  All mandatory (to keep baking) and optional updates are posted across Tez.Capital social media channels.  Updates must be performed manually at this time, to ensure the Tezos node operator is the only person able to control their baking operation.

There are a few upgrade methods that are supported depending on what the Tezos node operator wants to do:

* Update Octez binaries only (#4 only)
  * Using this upgrade method is recommended when one only wants to change or update the Tezos binaries, in cases such as:
    * Tezos protocol forkless update preparation
    * Removal of stale protocol after forkless update
    * Updating from old Octez version to new Octez version, while continuing to run the same Tezos protocol
* Update ami & eli and Octez binaries (#1, #2, #4)
  * Using this upgrade method is recommended for the same reasons as above, as well as:
    * Getting the latest ami & eli fixes and improvements
* Update the entire TezBake software stack (#1, #2, #3, #4)
  * Using this upgrade method is recommended for all the same reasons listes so far, as well as:
    * Getting the latest bb-cli improvements and fixes
* Update only bb-cli to the latest ALPHA version to receive the latest bb-cli improvements and proposed test fixes (#3 only)
  * Using this upgrade method is recommended when one only wants to change or update the bb-cli utility, in cases such as:
    * bb-cli is testing improvements and fixes such as new formatting or output options
  * Updating just bb-cli without running any setup commands does not change the Tezos node or baking process in any way

### Which TezBake upgrade method should I use?

We will sometimes specify which upgrade method to use by referencing its number.  If a number is not specified, use the guidelines below to determine which method to use:
* If you'd like to be as thorough as possible and ensure you have the latest of everything, use the #3 method.  Using this method is not necessary if there has not been a new bb-cli version.
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
   # you may be prompted for sudo password

   # If you have an ARM based computer such as a Raspberry Pi:
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/bb-cli-linux-arm64 && chmod +x bb-cli-linux-arm64
   sudo mv bb-cli-linux-arm64 /usr/sbin/bb-cli
   # you may be prompted for sudo password
   ```
Then update the rest of the TezBake software stack:

   ```
   bb-cli upgrade -a
   ```

## Update bb-cli to the ALPHA testing version  (#4)

   ```
   # If you have a regular Intel or AMD computer that's not "ARM" based:
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/ALPHA-bb-cli-linux-amd64 && chmod +x ALPHA-bb-cli-linux-amd64
   sudo mv ALPHA-bb-cli-linux-amd64 /usr/sbin/bb-cli
   # you may be prompted for sudo password

   # If you have an ARM based computer such as a Raspberry Pi:
   cd /tmp && wget https://gitlab.com/groktech/bakebuddy-cli/-/raw/main/ALPHA-bb-cli-linux-arm64 && ALPHA-bb-cli-linux-arm64
   sudo mv ALPHA-bb-cli-linux-arm64 /usr/sbin/bb-cli
   # you may be prompted for sudo password
   ```

## What should I do after updating?
After all updates and changes to your Tezos node, always ensure your baking process continues successfully by monitoring its performance on https://TzStats.com and https://TzKT.io.

Check your TezBake stack versions to ensure they are up to date:

   ```
   bb-cli version --all
   ```

You should see the expected Octez version along with the release date of the binaries.

![<bb-cli version, Octez versions>](/tezbake/tutorial/tezbakeVersionAll.png)


---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital) 