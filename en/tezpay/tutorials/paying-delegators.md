---
title: "Paying Delegators"
weight: 4
type: docs
summary: How to Pay Delegators Using TezPay
---

## Table of Contents

1. [Step 1: Generating a Payout Table](#using-tezpay-step-1---generating-a-payout-table)
2. [Step 2: Testing Notifications](#using-tezpay-step-2---testing-notifications)
3. [Step 3: Initiating a Payout](#using-tezpay-step-3---initiating-a-payout)
4. [Paying Multiple Cycles](#using-tezpay-paying-multiple-cycles-at-a-time)
5. [Running Continual Payouts](#using-tezpay-running-a-continual-payout)

---

## Using TezPay

### Summary of Using Tezpay

Using the TezPay application is through CLI for now and will be
carried out through a few commands.

This section will review:

1. Generating a payout table for all delegates
2. Generating a test notification (*Testing the
    **Twitter**/**Discord**/**E-mail** notifications from the advanced
    configuration file*)
3. Generating an actual payout
4. Running a continual payout
5. Keeping TezPay up-to-date

Before using a command in TezPay, you will need to run
***two additional commands</span>*** that will
allow you to call the application via commands and set permissions
While in the TezPay directory in the terminal, run the following
commands (*see code and examples below*):

   `mv tezpay-linux-amd64 tezpay`

| ![Command to move TezPay application in order to run.](/tezpay/tutorial/tezpayImage16.png) |
|-|

   `chmod +x tezpay`

   ![Command to change permissions for TezPay application in order to run.](/tezpay/tutorial/tezpayImage17.png)

---

### Using TezPay: Step 1 - Generating a payout Table

Generating a payout table is a tool that is useful for Public
Bakers because it allows the baker to see the incoming rewards from
baking (both block rewards & fees from delegates), review payout per
delegator from their stake, and other data (see below).

The command to generate a payout table is as follows (always remember to be in the
*tezpay* directory:

   `./tezpay generate-payouts`

This will generate a payout table, as exampled below:

| ![Description of output for ./tezpay generate-payout command.](/tezpay/tutorial/tezpayImage18.png) |
|-|

***Note: the Delegator & Recipient columns have been hidden***

1. Represents the table of *invalid* delegators that will **not** be
    paid out due to not meeting specific thresholds (ie minimum payout
    threshold/minimum balance)
2. Represents the table of *valid* delegators that will be paid out
3. The list of Delegators first 4 and last 4 characters of their
    wallet
4. The list of Recipients of payment first 4 and last 4 characters of
    their wallet (often the same as Delegator)
5. The delegated balance for each Delegator for that particular cycle
6. Represents the kind of reward given (ie delegator reward, baker
    reward, and fee income)
7. The amount of the specific reward to be paid out
8. The fee rate for each delegator, will reflect those delegators who
    have custom fees per the configuration file
9. The fee collected by the baker from each delegator for hosting their
    delegation
10. The transaction fee to pay out each reward

### Using TezPay: Step 2 - Testing Notifications

This step will be used to test the notification system for your
TezPay application. This step will NOT need to be completed every
payout, but only when you changes messages to test that it was
successful.

while in the *tezpay* directory, use the command:

   `./tezpay test-notify`

### Using TezPay: Step 3 - Initiating a Payout

To use the TezPay application to send a payment will require 1
line of code and one additional confirmation while it runs.  

To run TezPay, run the code:

`./tezpay pay`

The system will initiate the TezPay application at this point and present a payout
table, similar to the table(s) from step 1.

Once the table is displayed, the prompt will ask you to confirm the payout, and you need to confirm *y* or *n* (see below):

| ![Output and subseqant request for confirmation while running TezPay.](/tezpay/tutorial/tezpayImage19.png) |
|-|

1. You will be asked to confirm, type '***y***' for yes or '***n***'
    for no

If you confirm the transaction, the payouts will proceed and your
delegates will be paid out, as well as your Baker wallet. Remember, if
you are using a separate wallet to payout from your Baker wallet you
will need to add funds to cover the payments.

At this point you have successfully ran the TezPay application and paid out your
delegates. Be sure to check the confirmations and any errors that may
have been broadcasted in the terminal (ie, the example above did not
send notifications because that was not set up).

### Using TezPay: Paying multiple cycles at a time

TezPay provides bakers a way to aggregate multiple cycles and pay delegators in a lumpsum fashion.

Using the `--interval` argument you can specify how many cycles you want to aggregate into each payment. Cycles must complete before they can be paid. For example, with interval `7`, cycles 1-7 are paid during cycle 8, then cycles 8-14 are paid during cycle 15, and so on.

**Shifting the interval with `--interval-trigger-offset`:**

By default, intervals align to cycle 1. The offset lets you shift this alignment to match your preferred schedule. For example, if it's currently cycle 10 and you want to start paying during cycle 11 for the previous 7 cycles, then continue every 7 cycles, use offset `3`. This works because 7 is the end of the default interval, and adding 3 makes 10 the end of the interval. Instead of cycles 1-7 being paid, cycles 3-10 are paid.

`./tezpay pay --interval 7 --interval-trigger-offset 3`

**Alternative: specify the cycle manually**

Instead of calculating an offset, you can specify the payment cycle directly with `--cycle` and the interval will be counted from there.

For example, if it's cycle 65 and you'd like to pay cycles 56-62:

`./tezpay pay --interval 7 --cycle 63`

If you're worried about failed or partially successful payments, you can utilize the `--include-previous-cycles` argument. This checks X cycle in the past, before the first cycle of your interval, for any failed payments and includes them in the current payment batch.

`./tezpay pay --interval 7 --include-previous-cycles 7`

Let's say it's currently cycle 63. In the example above, you will pay cycles 56-62 and check cycles 49-55 for any missed payments.

### Using TezPay: Running a Continual Payout

To use the TezPay application to send a payment will require 1
line of code and one additional confirmation while it runs.  

To run TezPay in continual mode, run the command:

   `./tezpay continual`

   | ![Run TezPay in continuous mode](/tezpay/tutorial/tezpayImage20Continual.png) |
   |-|

Running in continual mode will start its first payment a little bit after the current cycle (one during which you launched it) finishes and the next one begins (usually around 30-60 minutes after the beginning of the new cycle).

If you would like to start in continual mode but still need to pay your delegators for last cycle, run the command from Step 3. first and then launch TezPay in continual mode.

> **⚠️ IMPORTANT: Protocol Upgrades**
>
> TezPay continual mode will **stop** when a Tezos protocol upgrade occurs. After a protocol upgrade:
> 1. Run a dry-run for the first cycle after the upgrade: `./tezpay generate-payouts`
> 2. Check for any errors in the output
> 3. If successful, manually pay the first cycle: `./tezpay pay`
> 4. Restart continual mode: `./tezpay continual`

---

## Summary: TezPay

TezPay is an application that allows for easy payouts for public
bakers on the Tezos Blockchain. This allows for significant
customizations and flexibility with respect to the payout system and
empowers bakers to further take control of their Bakery and aid in
decentralizing the Tezos Blockchain.

---

## Related Guides

**Configuration & Automation:**

* [TezBake Integration](/tezpay/tutorials/tezbake-integration/) - Automate payouts with your baker
* [Notifications](/tezpay/tutorials/notifications/) - Set up payout alerts
* [Configuration Examples](/tezpay/configuration/examples/) - Sample configs

**Setup:**

* [TezPay Setup](/tezpay/tutorials/setup/) - Initial setup guide
* [Baking on Mainnet](/tezbake/tutorials/baking-on-mainnet/) - Set up your baker

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
