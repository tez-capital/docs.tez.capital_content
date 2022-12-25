---
title: "Tutorial"
weight: 1
type: docs
summary: tezpay tutorial
---
**TezPay**
---
*A payment distribution tool
developed by YourBakeBuddy for Bakers on the Tezos Blockchain*

TezPay is a tool developed by the development team behind YourBakeBuddy,
a simplified workflow to set up and execute a Baker (Validator) on the
Tezos blockchain. The goal of this document is to instruct a baker on
how to set up TezPay and how to pay-out the bakers delegates. The
workflow will accomplish these tasks from Command-line-interface(CLI) -
if you do not have experience in CLI this tutorial will include screen
captures as well as examples and explanations of the steps. This
tutorial is meant to cover all skill levels from novice to expert. 

---
---

## Preparation

There is minimal preparation needed to utilize TezPay, assuming that
you have a functioning public baker running. To get help with setting up
a baker see *YourBakeBuddy - Setting up a public Baker*. 

**Preparation Summary**

1.  Create a directory (folder) on your desktop (or appropriate
    location) to store the application files and required config files
2.  Download the Tezpay software, sample configuration, and payout
    wallet files from github
3.  Create a 'pay-out' wallet and store the private key (not baker or
    hot wallet)

---

### Preparation: Step 1 *Create TezPay Directory*

Create a new folder on the desktop or in a location that you want
to store the necessary files for Tezpay.


| ![<Newly created TezPay directory, will contain all files for Tezpay Application.>](/tezpay/tutorial/tezpayImage1.png) |
|-|


This folder will eventually contain 3 files:

1.  TezPay Application File
2.  Configuration File
3.  Private-Key File 

The *TezPay Application File* will execute the commands that will run
TezPay, the *Configuration File* will essentially tell the application
file how to run and will be edited by the baker (we will cover this
later), and the *Private-Key File* will contain the private key for a
wallet you have designated to pay your delegators (we will explain an
easy way to get this later as well). 
***A note on the Private-key file - it is not recommended to use an existing wallet that may house
important assets (including NFT's or crypto tokens) but to create a new
wallet that contains only enough Tezos to pay out delegates.***

---

### Preparation: Step 2 *Download Files*

Download the software from Github using the link:
<https://github.com/alis-is/tezpay/releases>. In this repository,
select the correct operating system and system architecture (arm64 or
amd64). For this example we will be using *arm64*

| ![<Download options based on operating system for TezPay application.>](/tezpay/tutorial/tezpayImage2.png) |
|-|

Note: If you do not know the system architecture you can find out by
opening a Terminal and executing the command `uname -m`, and this will
return the system architecture`arm64` or `amd64`. 

The example below displays *arm64*

| ![<Command to determine system architecture.>](/tezpay/tutorial/tezpayImage3.png) |
|-|

If you would rather utilize CLI to download the application you will need
to change directory (cd) to the TezPay directory created in step 1 in
the terminal, **then** use command `wget -q
https://raw.githubusercontent.com/alis-is/tezpay/main/install.sh -O
/tmp/install.sh && sh /tmp/install.sh` 

See example code below (*note: input begins after last '%' character*): 
    
| ![<Command to dl TezPay application using CLI for Linux.>](/tezpay/tutorial/tezpayImage4.png) |
|-|

**The above example is an alternative to downloading from the GitHub
repository**

**Next**, you will download the sample configuration
file & *payout\_wallet\_private.sample.key* file from :
<https://github.com/alis-is/tezpay/blob/main/docs/configuration/> - *or
you can open file in GitHub and copy text to a text editor*.

*Note, there are two choices for the configuration files*:

1.  Simple Config file : config.default.hjson (basic arguments to run
    Tezpay)
2.  Full Config: config.sample.hjson (advanced arguements to run
    Tezpay)

| ![<Repository with configuration Files.>](/tezpay/tutorial/tezpayImage5.png) |
|-|

---

### Preparation: Step 3 - Payout Wallet (optional)

**Lastly**, you will need to download the create a new wallet and
retrieve the private key. We will be using the Temple Wallet extension
to create a new wallet and to get the private key. If you already have a
payout wallet, or know how, skip this section.

  - **First**, open **Temple Wallet** and select the icon in upper right
    

| ![<Access Temple Wallet menu.>](/tezpay/tutorial/tezpayImage6.jpg) |
|-|

  - **Next**, select 'new account', and input a new (ie Tezpay1)

| ![<Name and confirm the new payout wallet.>](/tezpay/tutorial/tezpayImage7.jpg) |
|-|

  - **Then**, click the icon in the upper right again

| ![<Access Temple Wallet menu, again.>](/tezpay/tutorial/tezpayImage8.png) |
|-| 

  - **Then**, go to **Settings**

| ![<Select settings in Temple Wallet menu.>](/tezpay/tutorial/tezpayImage9.jpg) |
|-|

  - **Then**, go to **Reveal Private Key**

| ![<Scroll to Reveal Private Key.>](/tezpay/tutorial/tezpayImage10.jpg) |
|-|

  - **Then**, you will be prompt to input your password for the account

| ![<Input password for the account.>](/tezpay/tutorial/tezpayImage11.png) |
|-|

Your *private key* will be displayed here:

| ![<Copy the private key of the payout wallet.>](/tezpay/tutorial/tezpayImage12.png) |
|-|

---
---

## Set Up

The next section will review the setup needed for Tezpay. We will
review the Configuration File and Payout Wallet files. In this section
we will review: 

1.  Configuration File (simple)
2.  Configuration File (advanced)
3.  Private-Key File

---

### Set Up: Step 1a: Configuration File (simple)

This section will review how to setup the configuration file
(simple-version) to be used by the TezPay application. 

*Note: must have file name **config.hjson**, case-sensative*
    
| ![<Simple Configuration file for TezPay.>](/tezpay/tutorial/tezpayImage13.png) |
|-|
    
*Yellow is areas you will input data/edit fields*  
*Blue are comments about Configuration File objects*

1.  Paste your **Baker** wallet in between the quotes
2.  Set your Baker fee (ie 0.05 = 5% fee)
3.  Input minimum payout for delegators (ie. 1 = 1 XTZ)
4.  This section allows you to split payments for baking (Bonds) and
    fees from delegators (Fees) if you want - input **Baker** wallet
    address and percentage to split. This example does not split, where
    1=100% to baker wallet.
5.  This section refers to network parameters to fetch information about
    the baker in order to compile rewards and payouts - **Do not edit
    unless you are an experienced user**
6.  Over delegation projection prohibits you from over paying beyond
    your delegation limit - **Do not edit unless you are an experience
    user**

This is as basic configuration file for running the TezPay
application. The next section will review the advanced configuration
file, giving more options and customization to the TezPay application. A
user may splice sections from the advanced file to the simple to build a
custom file as well - but should be done by experienced users.
    
---    

### Set Up: Step 1b: Configuration File (advanced)

This section will review how to setup the configuration file
(advanced-version) to be used by the TezPay application. 

*Note: must have file name **config.hjson**, case-sensative*
    
| ![<Advanced Configuration file for TezPay.>](/tezpay/tutorial/tezpayImage14.png) |
|-|

*Yellow is areas you will input data/edit fields* 
*Blue are comments about Configuration File objects*

1.  Paste your **Baker** wallet in between the quotes
2.  Set your Baker fee (ie 5% = 0.05)
3.  Input minimum payout for delegators (ie. 1 = 1 XTZ)
4.  Input minimum staking balance for delegators
5.  Input delegators wallets that you wish to ignore completely (not
    often used)
6.  Overrides: These will override global settings for delegators - you
    may *delete/remove* specific fields where you want to ignore
    1.  Input wallet address for specific delegator you wish to apply
        overrides for
    2.  Input a delegator-specific custom fee (ie 0.5% = 0.005)
    3.  Input either true or false where true removes the fee from
        delegator, and false applies the fee specified in the line
        above
    4.  Input minimum balance override for specified wallet\\
    5.  If you wish to add multiple wallets for over rides, add another
        wallet following same format keeping the `{ }` to contain the
        wallet attribute as shown.
7.  This section allows you to split payments for baking (Bonds), input
    wallet address and percentage to allocate (see example above for
    structure of more than 1 wallet. 100%=1.00
8.  This section allows you to split payments for fees collected (Fees),
    input wallet address and percentage to allocate (see example above
    for structure of more than 1 wallet. 100%=1.00
9.  Donations refer to donating a specific amount of XTZ to wallets
    every payout
    1.  Input a donation amount in *donate* field as a percentage (ie
        2.5% = 0.025)
    2.  Input wallets that you wish to donate to and percentage for each
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

### Set Up: Step 2: Private-Key File

The Private Key file is used to sign the transaction(s) that will
payout the rewards. From a security perspective, it is recommended
***NOT*** to use the Baker private key due to security concerns with the
private key being displayed in plain-text in a file. This section will
use the new account and private-key that was acquired in Step 3 - Payout
Wallet.

1.  Retrieve the private-key of the wallet you wish to use from the
    payout wallet of your choice
2.  Paste into a text file (or replace the example that was downloaded
    from the GitHub repository 
3.  Save the file with ***exact*** file name and extension
    *payout\_wallet\_private.key* - note: the .key is required in this
    instance (see below)

| ![<format for naming private key file - must be exactly the same.>](/tezpay/tutorial/tezpayImage15.jpg) |
|-|

---    
    
### Set Up: Summary

At this point there should be 3 files in the directory *tezpay*.
These three files will be used to run the application and payouts. The
next section will show the commands to run the TezPay software.

---
---
    
## Using TezPay



#### Summary of Using Tezpay

Using the TezPay application is through CLI for now and will be
carried out through a few commands. 

This section will review:

1.  Generating a payout table for all delegates
2.  Generating a test notification (*Testing the
    **Twitter**/**Discord**/**E-mail** notifications from the advanced
    configuration file*)
3.  Generating an actual payout

Before using a command in TezPay, you will need to run
***two additional commands</span>*** that will
allow you to call the application via commands and set permissions
While in the TezPay directory in the terminal, run the following
commands (*see code and examples below*):

    `mv tezpay-linux-amd64 tezpay`

   

| ![<Command to move TezPay application in order to run.>](/tezpay/tutorial/tezpayImage16.png) |
|-|

     `chmod +x tezpay`
    
| ![<Command to change permissions for TezPay application in order to run.>](/tezpay/tutorial/tezpayImage17.png) |
|-|
    
---    

#### Using TezPay: Step 1 - Generating a payout Table

Generating a payout table is a took that is useful for Public
Bakers because it allows the baker to see the incoming rewards from
baking (both block rewards & fees from delegates), review payout per
delegator from their stake, and other data (see below).

The command to generate a payout table is as follows (always remember to be in the
*tezpay* directory: 

    `./tezpay generate-payouts`

This will generate a payout table, as exampled below: 

| ![<Description of output for ./tezpay generate-payout command.>](/tezpay/tutorial/tezpayImage18.png) |
|-|

***Note: the Delegator & Recipient columns have been hidden***

1.  Represents the table of *invalid* delegators that will **not** be
    paid out due to not meeting specific thresholds (ie minimum payout
    threshold/minimum balance)
2.  Represents the table of *valid* delegators that will be paid out
3.  The list of Delegators first 4 and last 4 characters of their
    wallet
4.  The list of Recipients of payment first 4 and last 4 characters of
    their wallet (often the same as Delegator)
5.  The delegated balance for each Delegator for that particular cycle
6.  Represents the kind of reward given (ie delegator reward, baker
    reward, and fee income)
7.  The amount of the specific reward to be paid out
8.  The fee rate for each delegator, will reflect those delegators who
    have custom fees per the configuration file
9.  The fee collected by the baker from each delegator for hosting their
    delegation
10. The transaction fee to pay out each reward



#### Using TezPay: Step 2 - Testing Notifications

This step will be used to test the notification system for your
TezPay application. This step will NOT need to be completed every
payout, but only when you changes messages to test that it was
successful. 

while in the *tezpay* directory, use the command: 


   ` ./tezpay test-notify`



#### Using TezPay: Step 3 - Initiating a Payout

To use the TezPay application to send a payment will require 1
line of code and one additional confirmation while it runs.  

To run TezPay, run the code: 

`./tezpay pay`


The system will initiate the TezPay application at this point and present a payout
table, similar to the table(s) from step 1.

Once the table is displayed, the prompt will ask you to confirm the payout, and you need to confirm *y* or *n* (see below):

| ![<Output and subseqant request for confirmation while running TezPay.>](/tezpay/tutorial/tezpayImage19.png) |
|-|

1.  You will be asked to confirm, type '***y***' for yes or '***n***'
    for no

If you confirm the transaction, the payouts will proceed and your
delegates will be paid out, as well as your Baker wallet. Remember, if
you are using a separate wallet to payout from your Baker wallet you
will need to add funds to cover the payments. 

At this point you have successfully ran the TezPay application and paid out your
delegates. Be sure to check the confirmations and any errors that may
have been broadcasted in the terminal (ie, the example above did not
send notifications because that was not set up).
    
For access to future releases via Linux CLI:
    `wget -q https://raw.githubusercontent.com/alis-is/tezpay/main/install.sh -O /tmp/install.sh && sh /tmp/install.sh`

---
    
## Summary: TezPay

TezPay is an application that allows for easy payouts for public
bakers on the Tezos Blockchain. This allows for significant
customizations and flexibility with respect to the payout system and
empowers bakers to further take control of their Bakery and aid in
decentralizing the Tezos Blockchain. 

---
---

Any questions/comments/concerns please contact the YourBakeBuddy team on
[Discord](https://discord.gg/vykxNSnvQY),
[Twitter](https://twitter.com/YourBakeBuddy), or
[Telegram](https://t.me/bakebuddy)

## Appendix



## Notification setup in Configuration File
    ## Summary
    
    Notifications can be automatically sent out when payouts are generated using TezPay. The text are configured in the config.json file, however there are preliminary steps needed inorder to be able to utilize these features. This section will review the necessary steps and information needed from platforms that allow for auto-notifications sent via TezPay. **This tutorial is meant for individuals that have zero experience with interacting with these systems - feel free to skip sections if you are an advanced user.**
    
    ## Twitter
    
    A Baker can use TezPay to automatically send notifications that payments have been made. This allows for ease of communications for the public baker to the delegates and we will walk through steps to allow for this here. There will be two sections:
    
    1. Twitter Setup
    2. TezPay config.json file setup
    
    ## Twitter Setup
    
    In order to setup twitter, be sure you are signed in on a browser window to your twitter account. 
    
 1. While being signed into Twitter in a seperate browser, navigate to https://developer.twitter.com/ and select 'Developer Portal' in the top menu- the developer portal site where you will set up your development account and get the required information for the config.json file and set up permissions.
    
    | ![<Navigate to the Twitter Development Portal website>](/tezpay/tutorial/twitterNotificationimage0.png) |
|-|
    
 2. You will then need to fill in some basic information in order to enable the Development Portal profile to be active. be sure that all of the information is correct (if you use multiple twitter accounts be sure the one you want to enable for notifications is the correct one). 
    
    | ![<Fill in Basic information to set up a Developer Account for Twitter>](/tezpay/tutorial/twitterNotificationimage1.png) |
|-|
    
 3. Once you submit the information for your development profile - you will need to accept the Terms and Conditions.
    
    | ![<Accept Terms and Conditions to Proceed>](/tezpay/tutorial/twitterNotificationimage2.png) |
|-|
    
 4. After accepting Terms and Conditions - you will need to verify your e-mail address that you provided by clicking the link in the e-mail sent from Twitter. 
    
    | ![<Verify the e-mail used for your twitter account - you may need to set this up in Twitter before hand if not done>](/tezpay/tutorial/twitterNotificationimage3.png) |
|-|
    
 5. Once you verify your email - this will automatically link you back to your Developer Portal Site and prompt you for an 'App Name' (see TezPay Notif in screen capture). 
    
    | ![<Name your APP on Twitter Development Portal website>](/tezpay/tutorial/twitterNotificationimage4.png) |
|-|
    
 6. Once you name your App - you will be given 3 seperate Keys. These will be used to inorder to call the Twitter profile that you are looking to send Notifcations to - and sign for the permissions. Be sure to have these saved in a secure place - they will be used later. Once you have the keys secured, click 'Dashboard' button in lower right. 
    
     | ![<Save the Keys for your Twitter Development Profile and App>](/tezpay/tutorial/twitterNotificationimage5.png) |
|-|
    
 7. You will now be in your Twitter Development Dashboard and see the App you created (for this example TezPayNotifs). Click the 'gear' icon to continue set-up. 
    
    | ![<Click the gear icon on the app you created for twitter notifications for TezPay and continue to setup your app.>](/tezpay/tutorial/twitterNotificationimage6.png) |
|-|
    
 8. Navigate to 'User Authentication Settings' section and click 'Set-up' to continue.
               
        | ![<Navigate to User authentication setting and click 'Set up'>](/tezpay/tutorial/twitterNotificationimage7.png) |
|-|
    
 9. We will fill out the required items for the form under User Authentication settings as follows: <br>
        i. App Permissions: Select 'Read and write' <br>
        ii. Type of App: Select 'Web App, Automated App or Bot'<br>
        iii. App Info: <br>
            a. Callback URI/Redirect URL: this can be anything but for this example we will use 'https://www.TezPayisthebest.com/'<br>
            b. Website URL: this can be anything but for this example we will use 'https://www.TezPayisthebest.com/'<br>
        iv. Click 'save' when finished. <br>
    
    | ![<Select the outlined toggles under App Permissions & Type of App, then fill in a website for App Info>](/tezpay/tutorial/twitterNotificationimage8.png) |
|-|
               
  10. You will then get your Client ID and Client Secret - copy and save both of these with your other Keys attained earlier. Click 'Done'
            
               | ![<Copy the Cliend ID and Client Secret>](/tezpay/tutorial/twitterNotificationimage9.png) |
|-|   
      
  11. This will bring you back to the main screen of the App - navigate to the 'Keys and Tokens' toggle at the top of the page. Now, 'Generate' an Access Token and secret - write these down and then once you have confirmed you will be ready to edit the config.json in TezPay. Note - be sure the Access Token and Secret has 'read and write' permission (see image below). For the Next step - setting up the config.json - you will need four items:<br>
        a. API Key and Secret (also known as Consumer Key/Secret)<br>
        b. Access Token and Secret
    
    | ![<Generate your Acces Token and Access Token Secret - and then verify the information.>](/tezpay/tutorial/twitterNotificationimage10.png) |
|-|   
               
 ## TezPay Config Setup
    
    We will use the API Key and Secret as well as the Access Token and Secret to edit the Config file to allow for permissions and calls to push notifcations upon each successful payout.
               
     1. You will add the following code block at the base of the config.json file to enable the notifications for TezPay. Note that the message_template can be anything and you can use the <Cyle>, <Delegators>, <DistributedRewards> terms to automatically fill in these terms for each cycle. 
               ` notifications: [
    {
      access_token: Your_Acces_Token
      access_token_secret: Your_Access_Token_Secret
      consumer_key: Your_Consumer_Key
      consumer_secret: Your_Consumer_Key_Secret
      message_template: Rewards for <Cycle> have been paid to <Delegators> delegates in amount of <DistributedRewards> using #TezPay.👀 XTZ baked with bakebuddy.xyz! We donate to wallet tz1R2GnBudU97Lra8Q3VDG7cUooNvUQ9ghCs to fund future development in Tezos. #cerberusbakery #tezos.
      type: twitter
    }
    ]`
    
                   | ![<Fill in the information for the config.json file with you information!>](/tezpay/tutorial/twitterNotificationimage11.png) |
|-|   
                              
     2. Once you have filled in the necessary information - you have setup Notifications for Twitter! Test the notification by using command: 
                              a. <code> tezpay test-notify</code>

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
