{\\rtf1\\ansi\\ansicpg1252\\cocoartf2706
\\cocoatextscaling0\\cocoaplatform0{\\fonttbl\\f0\\fswiss\\fcharset0
Helvetica;} {\\colortbl;\\red255\\green255\\blue255;}
{\\\*\\expandedcolortbl;;}
\\margl1440\\margr1440\\vieww11520\\viewh8400\\viewkind0
\\pard\\tx720\\tx1440\\tx2160\\tx2880\\tx3600\\tx4320\\tx5040\\tx5760\\tx6480\\tx7200\\tx7920\\tx8640\\pardirnatural\\partightenfactor0

\\f0\\fs24 \\cf0 #TezPay\\ *A payment distribution tool
developed by YourBakeBuddy for Bakers on the Tezos Blockchain*\\ \\

TezPay is a tool developed by the development team behind YourBakeBuddy,
a simplified workflow to set up and execute a Baker (Validator) on the
Tezos blockchain. The goal of this document is to instruct a baker on
how to set up TezPay and how to pay-out the bakers delegates. The
workflow will accomplish these tasks from Command-line-interface(CLI) -
if you do not have experience in CLI this tutorial will include screen
captures as well as examples and explanations of the steps. This
tutorial is meant to cover all skill levels from novice to expert. \\ \\

## Preparation

\\ There is minimal preparation needed to utilize TezPay, assuming that
you have a functioning public baker running. To get help with setting up
a baker see *YourBakeBuddy - Setting up a public Baker*. \\ ----\\ \\
**Preparation Summary**\\ \\

1.  Create a directory (folder) on your desktop (or appropriate
    location) to store the application files and required config files\\
2.  Download the Tezpay software, sample configuration, and payout
    wallet files from github\\
3.  Create a 'pay-out' wallet and store the private key (not baker or
    hot wallet)\\

\\ ----\\

### Preparation: Step 1 *Create TezPay Directory*

\\ \\ Create a new folder on the desktop or in a location that you want
to store the necessary files for Tezpay.\\ \\
\\{\\{:screenshot\_2022-11-17\_at\_1.10.24\_pm.png?200|\\}\\}\\ \\ This
folder will contain 3 files: \\

1.  TezPay Application File\\
2.  Configuration File\\
3.  Private-Key File \\

\\ The *TezPay Application File* will execute the commands that will run
TezPay, the *Configuration File* will essentially tell the application
file how to run and will be edited by the baker (we will cover this
later), and the *Private-Key File* will contain the private key for a
wallet you have designated to pay your delegators (we will explain an
easy way to get this later as well). \\ \\ ***A note on the Private-key
file - it is not recommended to use an existing wallet that may house
important assets (including NFT's or crypto tokens) but to create a new
wallet that contains only enough Tezos to pay out delegates.***\\ \\ \\

### Preparation: Step 2 *Download Files*

\\ Download the software from Github using the link:
<https://github.com/alis-is/tezpay/releases>\\ \\ In this repository,
select the correct operating system and system architecture (arm64 or
amd64). For this example we will be using *arm64*\\ \\
\\{\\{:screenshot\_2022-11-17\_at\_2.10.30\_pm.png?600|\\}\\}\\ \\ \\ \\
\\ Note: If you do not know the system architecture you can find out by
opening a Terminal and executing the command `uname -m`, and this will
return the system architecture`arm64` or `amd64`. The example below
displays *arm64*\\ \\
\\{\\{:screenshot\_2022-11-17\_at\_2.11.47\_pm.png?200|\\}\\}\\ \\ \\ If
you would rather utilize CLI to download the application you will need
to change directory (cd) to the TezPay directory created in step 1 in
the terminal, **then** use command `wget -q
https://raw.githubusercontent.com/alis-is/tezpay/main/install.sh -O
/tmp/install.sh && sh /tmp/install.sh` See example code below (*note:
input begins after last '%' character*): \\ \\
\\{\\{:screenshot\_2022-11-18\_at\_7.39.13\_pm.png?direct&600|\\}\\}\\
\\ **The above example is an alternative to downloading from the GitHub
repository**\\ \\ **Next**, you will download the sample configuration
file & *payout\_wallet\_private.sample.key* file from :
<https://github.com/alis-is/tezpay/blob/main/docs/configuration/> - *or
you can open file in GitHub and copy text to a text editor*. \\ \\
*Note, there are two choices for the configuration files*:\\

1.  Simple Config file : config.default.hjson (basic arguments to run
    Tezpay)\\
2.  Full Config: config.sample.hjson (advanced arguements to run
    Tezpay)\\

\\{\\{:screenshot\_2022-11-17\_at\_2.17.21\_pm.png?800|\\}\\}\\ \\
----\\ \\

### Preparation: Step 3 - Payout Wallet (optional)

\\ \\ **Lastly**, you will need to download the create a new wallet and
retrieve the private key. We will be using the Temple Wallet extension
to create a new wallet and to get the private key. If you already have a
payout wallet, or know how, skip this section\\ \\

  - **First**, open **Temple Wallet** and select the icon in upper right
    \\

\\ \\{\\{:wallet\_0.jpg?direct&200|\\}\\}\\ \\

  - **Next**, select 'new account', and input a new (ie Tezpay1)\\

\\ \\{\\{:wallet\_new.jpg?direct&200|\\}\\}\\ \\

    ***Then**, click the icon in the upper right again\

\\ \\{\\{:new\_wallet\_1.jpg?direct&200|\\}\\} \\ \\

    ***Then**, go to //Settings//\

\\ \\{\\{:wallet\_2.jpg?direct&200|\\}\\}\\ \\

    ***Then**, go to //Reveal Private Key//\

\\ \\{\\{:wallet\_3.jpg?direct&200|\\}\\}\\ \\

    ***Then**, you will be prompt to input your password for the account\

\\ \\
\\{\\{:screenshot\_2022-11-17\_at\_4.18.47\_pm.png?direct&200|\\}\\}\\
\\ Your *private key* will be displayed here\\ \\
\\{\\{:screenshot\_2022-11-17\_at\_4.20.31\_pm.png?direct&200|\\}\\}\\
\\ ----\\ \\

## Set Up

\\ \\ The next section will review the setup needed for Tezpay. We will
review the Configuration File and Payout Wallet files. In this section
we will review: \\

1.  Configuration File (simple)\\
2.  Configuration File (advanced)\\
3.  Private-Key File\\

\\

### Set Up: Step 1a: Configuration File (simple)

\\ \\ This section will review how to setup the configuration file
(simple-version) to be used by the TezPay application. \\ \\ *Note: must
have file name **config.hjson**, case-sensative*\\ \\ \\
\\{\\{:screenshot\_2022-11-17\_at\_5.42.43\_pm.png?direct&600|\\}\\}\\
\\ *Yellow is areas you will input data/edit fields* \\ \\ *Blue are
comments about Configuration File objects*\\ \\

1.  Paste your **Baker** wallet in between the quotes\\
2.  Set your Baker fee (ie 0.05 = 5% fee)\\
3.  Input minimum payout for delegators (ie. 1 = 1 XTZ)\\
4.  This section allows you to split payments for baking (Bonds) and
    fees from delegators (Fees) if you want - input **Baker** wallet
    address and percentage to split. This example does not split, where
    1=100% to baker wallet.\\
5.  This section refers to network parameters to fetch information about
    the baker in order to compile rewards and payouts - **Do not edit
    unless you are an experienced user**\\
6.  Over delegation projection prohibits you from over paying beyond
    your delegation limit - **Do not edit unless you are an experience
    user**\\

\\ This is as basic configuration file for running the TezPay
application. The next section will review the advanced configuration
file, giving more options and customization to the TezPay application. A
user may splice sections from the advanced file to the simple to build a
custom file as well - but should be done by experienced users. \\ \\ \\

### Set Up: Step 1b: Configuration File (advanced)

\\ \\ This section will review how to setup the configuration file
(advanced-version) to be used by the TezPay application. \\ \\ *Note:
must have file name **config.hjson**, case-sensative*\\ \\
\\{\\{:screenshot\_2022-11-18\_at\_1.36.42\_pm.png?direct&600|\\}\\}\\
\\ *Yellow is areas you will input data/edit fields*\\ \\ *Blue are
comments about Configuration File objects*\\ \\

1.  Paste your\\'a0**Baker**\\'a0wallet in between the quotes\\
2.  Set your Baker fee (ie 5% = 0.05)\\
3.  Input minimum payout for delegators (ie. 1 = 1 XTZ)\\
4.  Input minimum staking balance for delegators\\
5.  Input delegators wallets that you wish to ignore completely (not
    often used)\\
6.  Overrides: These will override global settings for delegators - you
    may *delete/remove* specific fields where you want to ignore\\
    1.  Input wallet address for specific delegator you wish to apply
        overrides for\\
    2.  Input a delegator-specific custom fee (ie 0.5% = 0.005)\\
    3.  Input either true or false where true removes the fee from
        delegator, and false applies the fee specified in the line
        above\\
    4.  Input minimum balance override for specified wallet\\
    5.  If you wish to add multiple wallets for over rides, add another
        wallet following same format keeping the \\{\\} to contain the
        wallet attribute as shown.\\
7.  This section allows you to split payments for baking (Bonds), input
    wallet address and percentage to allocate (see example above for
    structure of more than 1 wallet. 100%=1.00\\
8.  This section allows you to split payments for fees collected (Fees),
    input wallet address and percentage to allocate (see example above
    for structure of more than 1 wallet. 100%=1.00\\
9.  Donations refer to donating a specific amount of XTZ to wallets
    every payout\\
    1.  Input a donation amount in *donate* field as a percentage (ie
        2.5% = 0.025)\\
    2.  Input wallets that you wish to donate to and percentage for each
        (ie 100% = 1.0)\\
10. This section refers to network parameters to fetch information about
    the baker in order to compile rewards and payouts -\\'a0Do not edit
    unless you are an experienced user\\
11. Over delegation projection prohibits you from over paying beyond
    your delegation limit -\\'a0Do not edit unless you are an experience
    user\\
12. This section allows for automatic notifications/messages to be sent
    to specific platforms in order to broadcast messages (ie *'Bakery
    has paid 100XTZ in rewards for latest cycle'* **We will cover this
    in a separate section**- *see appendix X* and platforms supported
    are ***Twitter, Discord, and E-mail***\\

\\

### Set Up: Step 2: Private-Key File

\\ \\ The Private Key file is used to sign the transaction(s) that will
payout the rewards. From a security perspective, it is recommended
***NOT*** to use the Baker private key due to security concerns with the
private key being displayed in plain-text in a file. This section will
use the new account and private-key that was acquired in Step 3 - Payout
Wallet.\\ \\

1.  Retrieve the private-key of the wallet you wish to use from the
    payout wallet of your choice\\
2.  Paste into a text file (or replace the example that was downloaded
    from the GitHub repository \\
3.  Save the file with ***exact*** file name and extension
    *payout\_wallet\_private.key* - note: the .key is required in this
    instance (see below)\\

\\ \\{\\{:private-key\_file.jpg?direct&200|\\}\\}\\ \\

### Set Up: Summary

\\ At this point there should be 3 files in the directory *tezpay*.
These three files will be used to run the application and payouts. The
next section will show the commands to run the TezPay software. \\ \\
----\\ \\

## Using TezPay

\\ \\ \\ \\

#### Summary of Using Tezpay

\\ \\ Using the TezPay application is through CLI for now and will be
carried out through a few commands.\\ \\ This section will review:\\

1.  Generating a payout table for all delegates\\
2.  Generating a test notification (*Testing the
    **Twitter**/**Discord**/**E-mail** notifications from the advanced
    configuration file*)\\
3.  Generating an actual payout\\

\\ Before using a command in TezPay, you will need to run
**<span class="underline">two additional commands</span>** that will
allow you to call the application via commands and set permissions\\ \\
While in the TezPay directory in the terminal, run the following
commands (*see code and examples below*:\\

    mv tezpay-linux-amd64 tezpay

\\

    chmod +x tezpay

\\ \\
\\{\\{:screenshot\_2022-11-19\_at\_2.11.34\_pm.png?direct&600|\\}\\}\\
\\
\\{\\{:screenshot\_2022-11-19\_at\_2.12.51\_pm.png?direct&600|\\}\\}\\
\\ \\ \\ \\

#### Using TezPay: Step 1 - Generating a payout Table

\\ \\ Generating a payout table is a took that is useful for Public
Bakers because it allows the baker to see the incoming rewards from
baking (both block rewards & fees from delegates), review payout per
delegator from their stake, and other data (see below).\\ \\ The command
to generate a payout table is as follows (always remember to be in the
*tezpay* directory: \\ \\

    ./tezpay generate-payouts

\\ \\ This will generate a payout table, as exampled below: \\ \\
\\{\\{:screenshot\_2022-11-19\_at\_1.59.10\_pm.png?direct&600|\\}\\}\\
\\ ***Note: the Delegator & Recipient columns have been hidden***\\ \\

1.  Represents the table of *invalid* delegators that will **not** be
    paid out due to not meeting specific thresholds (ie minimum payout
    threshold/minimum balance)\\
2.  Represents the table of *valid* delegators that will be paid out\\
3.  The list of Delegators first 4 and last 4 characters of their
    wallet\\
4.  The list of Recipients of payment first 4 and last 4 characters of
    their wallet (often the same as Delegator)\\
5.  The delegated balance for each Delegator for that particular cycle\\
6.  Represents the kind of reward given (ie delegator reward, baker
    reward, and fee income)\\
7.  The amount of the specific reward to be paid out\\
8.  The fee rate for each delegator, will reflect those delegators who
    have custom fees per the configuration file\\
9.  The fee collected by the baker from each delegator for hosting their
    delegation\\
10. The transaction fee to pay out each reward\\

\\ \\

#### Using TezPay: Step 2 - Testing Notifications

\\ \\ This step will be used to test the notification system for your
TezPay application. This step will NOT need to be completed every
payout, but only when you changes messages to test that it was
successful. \\ \\ while in the *tezpay* directory, use the command: \\
\\

    ./tezpay test-notify

\\ \\

#### Using TezPay: Step 3 - Initiating a Payout

\\ \\ To use the TezPay application to send a payment will require 1
line of code and one additional confirmation while it runs. \\ \\ To run
TezPay, run the code: \\ \\ \<code\>./tezpay pay\</\>\\ \\ The system
will initiate the TezPay application at this point and present a payout
table, similar to the table(s) from step 1. \\ \\ Once the table is
displayed, the prompt will ask you to confirm the payout, and you need
to confirm *y* or *n* (see below)\\ \\
\\{\\{:screenshot\_2022-11-19\_at\_2.35.00\_pm.png?direct&600|\\}\\}\\
\\

1.  You will be asked to confirm, type '***y***' for yes or '***n***'
    for no\\

\\ If you confirm the transaction, the payouts will proceed and your
delegates will be paid out, as well as your Baker wallet. Remember, if
you are using a separate wallet to payout from your Baker wallet you
will need to add funds to cover the payments.\\ \\ At this point you
have successfully ran the TezPay application and paid out your
delegates. Be sure to check the confirmations and any errors that may
have been broadcasted in the terminal (ie, the example above did not
send notifications because that was not set up).\\ \\ \\

## Summary: TezPay

\\ \\ TezPay is an application that allows for easy payouts for public
bakers on the Tezos Blockchain. This allows for significant
customizations and flexibility with respect to the payout system and
empowers bakers to further take control of their Bakery and aid in
decentralizing the Tezos Blockchain.\\ \\ Any
questions/comments/concerns please contact the YourBakeBuddy team on
[Discord](https://discord.gg/vykxNSnvQY),
[Twitter](https://twitter.com/YourBakeBuddy), or
[Telegram](https://t.me/bakebuddy)\\ \\ \\

## Appendix

\\ \\

1.  Notification setup in Configuration File (*coming soon*)}
