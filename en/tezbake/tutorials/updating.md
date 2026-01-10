---
title: "Updating"
weight: 1
type: docs
summary: How to update TezBake, Octez binaries, and protocol versions safely
---

## TezBake Updating

The TezBake software consists of four components:

1. ami - App management engine: templating and orchestration - <https://github.com/alis-is/ami>
2. eli - Lua Interpreter & Essential libraries for simple cross platform scripting - <https://github.com/alis-is/eli>
3. tezbake - Command line interface for setting and monitoring your baker, using the help of the two tools above
4. Octez binaries - Tezos node binaries published by the Tezos core developers - <https://gitlab.com/tezos/tezos/-/releases>

We regularly update all parts of the TezBake stack and the Tezos core developers regularly publish new Octez versions and protocols.  All mandatory updates and optional updates are posted across Tez.Capital social media channels.  Updates must be performed manually at this time, to ensure the Tezos node operator is the only person able to control their baking operation.

### Which TezBake upgrade method should I use?

We will sometimes specify which upgrade method to use by referencing its letter.  If a letter recommendation is not specified, use the guidelines below to determine which method to use:

* (A) This upgrade method works in most cases. The only component not updated is the tezbake binary itself.
* (B) This upgrade method is used when you want to update the Octez binaries only. This is useful when old protocol binaries need to be removed from your baker.
* (C) This upgrade method is used when you want to update the entire TezBake stack. If you have the time, this is the recommended method as it makes sure you get the latest version of all components.
* (D) This upgrade method is used when you want to update the tezbake binary only. Sometimes we release new versions of tezbake that have fixes and new features to make your baker setup and operation easier. This upgrade method doesn't take your baker down as the tezbake binary is not used during the baking process.

## (A) Update ami & eli and Octez binaries

(updates components: #1, #2, #4)

```bash
tezbake upgrade -s
```

> The `-s` flag is used when there is a need to manually upgrade the Octez storage. This is a rare case and is only needed when the Octez storage format changes. This flag is not needed for regular updates. Using the flag when there is no update to storage needed doesn't have an impact on your baker.

## (B) Update Octez binaries only

(updates components: #4)

```bash
tezbake upgrade
```

## (C) Update the entire TezBake stack

(updates components: #1, #2, #3, #4)

First update your tezbake binary to the latest version, depending on your computer architecture:

```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
# you may be prompted for sudo password
```

Then update the rest of the TezBake software stack:

```bash
tezbake upgrade -s
```

> The `-s` flag is used when there is a need to manually upgrade the Octez storage. This is a rare case and is only needed when the Octez storage format changes. This flag is not needed for regular updates. Using the flag when there is no update to storage needed doesn't have an impact on your baker.

## (D) Update tezbake only

(updates components: #3)

Update your tezbake binary to the latest version, depending on your computer architecture:

```bash
wget -q https://github.com/tez-capital/tezbake/raw/main/install.sh -O /tmp/install.sh && sudo sh /tmp/install.sh
# you may be prompted for sudo password
```

## What should I do after updating?

After all updates and changes to your Tezos node, always ensure your baking process continues successfully by monitoring its performance on <https://TzStats.com> and <https://TzKT.io>.

Check your TezBake stack versions to ensure they are up to date:

```bash
tezbake version --all
```

You should see the expected Octez version along with the release date of the binaries.

![<tezbake version, Octez versions>](/tezbake/tutorial/tezbakeVersionAll.png)

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
