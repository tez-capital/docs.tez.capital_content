---
title: "> Setup"
weight: 1
type: docs
summary: How to setup TezPay
---

Follow along on Youtube!
{{< youtube 1SUPwKBWgTU >}}

## Preparation

There is minimal preparation needed to utilize TezPay, assuming that
you have a functioning public baker running.

**Preparation Summary**

1. Create a directory (folder) on your desktop (or appropriate
    location) to store the application files and required config files
2. Download the Tezpay software, sample configuration, and payout
    wallet files from github
3. Create a 'pay-out' wallet and store the private key (not baker or
    hot wallet)

---

### Preparation: Step 1 *Create TezPay Directory*

Create a new folder on the desktop or in a location that you want
to store the necessary files for Tezpay.

| ![<Newly created TezPay directory, will contain all files for Tezpay Application.>](/tezpay/tutorial/tezpayImage1.png) |
|-|

This folder will eventually contain 3 files:

1. TezPay Application File
2. Configuration File
3. Private-Key File

The *TezPay Application File* will execute the commands that will run
TezPay, the *Configuration File* will essentially tell the application
file how to run and will be edited by the baker (we will cover this
later), and the *Private-Key File* will contain the private key for a
wallet you have designated to pay your delegators (we will explain an
easy way to get this later as well).
> **⚠️ SECURITY WARNING: Private Key File**
>
> Do NOT use an existing wallet that contains important assets (NFTs, tokens, etc.). Create a new dedicated wallet that contains only enough Tezos to pay out delegates.

---

### Preparation: Step 2 *Download Files*

Download the software from Github using the link:
<https://github.com/tez-capital/tezpay/releases>. In this repository,
select the correct operating system and system architecture (arm64 or
amd64). For this example we will be using *arm64*

| ![Download options based on operating system for TezPay application.](/tezpay/tutorial/tezpayImage2.png) |
|-|

Note: If you do not know the system architecture you can find out by
opening a Terminal and executing the command `uname -m`, and this will
return the system architecture`arm64` or `amd64`.

The example below displays *arm64*

| ![Command to determine system architecture.](/tezpay/tutorial/tezpayImage3.png) |
|-|

If you would rather utilize CLI to download the application you will need
to change directory (cd) to the TezPay directory created in step 1 in
the terminal, **then** use command `wget -q
https://raw.githubusercontent.com/tez-capital/tezpay/main/install.sh -O
/tmp/install.sh && sh /tmp/install.sh`

See example code below (*note: input begins after last '%' character*):

| ![Command to dl TezPay application using CLI for Linux.](/tezpay/tutorial/tezpayImage4.png) |
|-|

**The above example is an alternative to downloading from the GitHub
repository**

**Next**, you will download the sample configuration
file & *payout\_wallet\_private.sample.key* file from :
<https://github.com/tez-capital/tezpay/blob/main/docs/configuration/> - *or
you can open file in GitHub and copy text to a text editor*.

*Note, there are two choices for the configuration files*:

1. Simple Config file : config.default.hjson (basic arguments to run
    Tezpay)
2. Full Config: config.sample.hjson (advanced arguements to run
    Tezpay)

| ![Repository with configuration Files.](/tezpay/tutorial/tezpayImage5.png) |
|-|

---

### Preparation: Step 3 - Payout Wallet (optional)

**Lastly**, you will need to download the create a new wallet and
retrieve the private key. We will be using the Temple Wallet extension
to create a new wallet and to get the private key. If you already have a
payout wallet, or know how, skip this section.

- **First**, open **Temple Wallet** and select the icon in upper right

| ![Access Temple Wallet menu.](/tezpay/tutorial/tezpayImage6.jpg) |
|-|

- **Next**, select 'new account', and input a new (ie Tezpay1)

| ![Name and confirm the new payout wallet.](/tezpay/tutorial/tezpayImage7.jpg) |
|-|

- **Then**, click the icon in the upper right again

| ![Access Temple Wallet menu, again.](/tezpay/tutorial/tezpayImage8.png) |
|-|

- **Then**, go to **Settings**

| ![Select settings in Temple Wallet menu.](/tezpay/tutorial/tezpayImage9.jpg) |
|-|

- **Then**, go to **Reveal Private Key**

| ![Scroll to Reveal Private Key.](/tezpay/tutorial/tezpayImage10.jpg) |
|-|

- **Then**, you will be prompt to input your password for the account

| ![Input password for the account.](/tezpay/tutorial/tezpayImage11.png) |
|-|

Your *private key* will be displayed here:

| ![Copy the private key of the payout wallet.](/tezpay/tutorial/tezpayImage12.png) |
|-|

---
---

## Setup

The next section will review the setup needed for Tezpay. We will
review the Configuration File and Payout Wallet files. In this section
we will review:

1. Configuration File (simple)
2. Configuration File (advanced)
3. Private-Key File

---

### Setup: Step 1a: Configuration File (simple)

This section will review how to setup the configuration file
(simple-version) to be used by the TezPay application.

*Note: must have file name **config.hjson**, case-sensitive*

| ![Simple Configuration file for TezPay.](/tezpay/tutorial/tezpayImage13.png) |
|-|

> **ℹ️ NOTE:** If you're familiar with JSON but new to HJSON (Human JSON), you can learn more at [https://hjson.github.io/](https://hjson.github.io/). HJSON is more forgiving than JSON and supports comments.

*Yellow is areas you will input data/edit fields*  
*Blue are comments about Configuration File objects*

1. Paste your **Baker** wallet in between the quotes
2. Set your Baker fee (ie 0.05 = 5% fee)
3. Input minimum payout for delegators (ie. 1 = 1 XTZ)
4. This section allows you to split payments for baking (Bonds) and
    fees from delegators (Fees) if you want - input **Baker** wallet
    address and percentage to split. This example does not split, where
    1=100% to baker wallet.
5. This section refers to network parameters to fetch information about
    the baker in order to compile rewards and payouts - **Do not edit
    unless you are an experienced user**
6. Over delegation projection prohibits you from over paying beyond
    your delegation limit - **Do not edit unless you are an experience
    user**

This is as basic configuration file for running the TezPay
application. The next section will review the advanced configuration
file, giving more options and customization to the TezPay application. A
user may splice sections from the advanced file to the simple to build a
custom file as well - but should be done by experienced users.

---

### Setup: Step 1b: Configuration File (advanced)

This section will review how to setup the configuration file
(advanced-version) to be used by the TezPay application.

*Note: must have file name **config.hjson**, case-sensitive*

| ![Advanced Configuration file for TezPay.](/tezpay/tutorial/tezpayImage14.png) |
|-|

*Yellow is areas you will input data/edit fields*
*Blue are comments about Configuration File objects*

> **💡 TIP: Start Simple**
>
> The example below is a more advanced configuration file showcasing several customization features. For most bakers, a simpler configuration is recommended. See [TezPay Starter Configuration](https://docs.tez.capital/tezpay/configuration/examples/starter/) which works for 90% of bakers.

1. Paste your **Baker** wallet in between the quotes
2. Set your Baker fee (ie 5% = 0.05)
3. Input minimum payout for delegators (ie. 1 = 1 XTZ)
4. Input minimum staking balance for delegators
5. Input delegators wallets that you wish to ignore completely (not
    often used)
6. Overrides: These will override global settings for delegators - you
    may *delete/remove* specific fields where you want to ignore
    1. Input wallet address for specific delegator you wish to apply
        overrides for
    2. Input a delegator-specific custom fee (ie 0.5% = 0.005)
    3. Input either true or false where true removes the fee from
        delegator, and false applies the fee specified in the line
        above
    4. Input minimum balance override for specified wallet\\
    5. If you wish to add multiple wallets for over rides, add another
        wallet following same format keeping the `{ }` to contain the
        wallet attribute as shown.
7. This section allows you to split payments for baking (Bonds), input
    wallet address and percentage to allocate (see example above for
    structure of more than 1 wallet. 100%=1.00
8. This section allows you to split payments for fees collected (Fees),
    input wallet address and percentage to allocate (see example above
    for structure of more than 1 wallet. 100%=1.00
9. Donations refer to donating a specific amount of XTZ to wallets
    every payout
    1. Input a donation amount in *donate* field as a percentage (ie
        2.5% = 0.025)
    2. Input wallets that you wish to donate to and percentage for each
        (ie 100% = 1.0)
10. This section refers to network parameters to fetch information about
    the baker in order to compile rewards and payouts **Do not edit
    unless you are an experienced user**
11. Over delegation projection prohibits you from over paying beyond
    your delegation limit **Do not edit unless you are an experience
    user**
12. This section allows for automatic notifications/messages to be sent
    to specific platforms in order to broadcast messages (ie *'Bakery
    has paid 100XTZ in rewards for latest cycle'* **We will cover this
    in a separate section**
    *see appendix X* and platforms supported are ***Twitter, Discord, and E-mail***

---

### Setup: Step 2: Private-Key File

The Private Key file is used to sign the transaction(s) that will
payout the rewards. From a security perspective, it is recommended
***NOT*** to use the Baker private key due to security concerns with the
private key being displayed in plain-text in a file. This section will
use the new account and private-key that was acquired in Step 3 - Payout
Wallet.

1. Retrieve the private-key of the wallet you wish to use from the
    payout wallet of your choice
2. Paste into a text file (or replace the example that was downloaded
    from the GitHub repository
3. Save the file with ***exact*** file name and extension
    *payout\_wallet\_private.key* - note: the .key is required in this
    instance (see below)

| ![<format for naming private key file - must be exactly the same.>](/tezpay/tutorial/tezpayImage15.jpg) |
|-|

---

### Setup: Summary

At this point there should be 3 files in the directory *tezpay*.
These three files will be used to run the application and payouts.

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
