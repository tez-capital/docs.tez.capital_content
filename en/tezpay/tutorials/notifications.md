---
title: "Notifications"
weight: 5
type: docs
summary: Configure Discord, Telegram, Twitter, and email payout notifications with TezPay
---

TezPay can send notifications after payouts are generated. Notification settings live in your TezPay `config.hjson` file under the `notifications` list.

Add only the notification blocks you actually use. Keep API tokens, webhook URLs, and SMTP passwords private.

---

## Notification Configuration

Each notification entry has a `type` and a `message_template`. TezPay sends the configured message when the notification runs.

```hjson
notifications: [
  {
    type: discord
    webhook_url: "https://discord.com/api/webhooks/..."
    message_template: "Rewards have been paid with TezPay."
  }
]
```

After editing `config.hjson`, test the notification path:

```bash
tezpay test-notify
```

If you run TezPay through TezBake, run the command from the TezPay environment or use the equivalent TezBake pay command for your installation.

---

## Discord

Use a Discord webhook when you want TezPay to post payout messages into a channel.

1. In Discord, open the target channel settings.
2. Go to **Integrations** -> **Webhooks**.
3. Create a webhook and copy its URL.
4. Add this block to `config.hjson`:

```hjson
notifications: [
  {
    type: discord
    webhook_url: "https://discord.com/api/webhooks/<webhook-id>/<webhook-token>"
    message_template: "Rewards have been paid with TezPay."
  }
]
```

You can also split the webhook into `webhook_id` and `webhook_token`:

```hjson
notifications: [
  {
    type: discord
    webhook_id: "<webhook-id>"
    webhook_token: "<webhook-token>"
    message_template: "Rewards have been paid with TezPay."
  }
]
```

For private operator alerts, set `admin: true` on a separate Discord notification block.

---

## Telegram

Use Telegram notifications when you want TezPay to send payout messages to one or more Telegram chats.

1. Create a Telegram bot with [BotFather](https://t.me/BotFather).
2. Save the bot API token.
3. Add the bot to the chat that should receive payout notifications.
4. Get the chat ID for each receiver.
5. Add this block to `config.hjson`:

```hjson
notifications: [
  {
    type: telegram
    api_token: "<telegram-bot-api-token>"
    receivers: [
      -1234567890
    ]
    message_template: "Rewards have been paid with TezPay."
  }
]
```

---

## Twitter

Use Twitter notifications when you want TezPay to post payout announcements from a Twitter/X account.

1. Sign in to the account that should post payout notifications.
2. Open the [Twitter/X Developer Portal](https://developer.twitter.com/).
3. Create an app with write permission.
4. Generate and save the consumer key, consumer secret, access token, and access token secret.
5. Add this block to `config.hjson`:

```hjson
notifications: [
  {
    type: twitter
    access_token: "<access-token>"
    access_token_secret: "<access-token-secret>"
    consumer_key: "<consumer-key>"
    consumer_secret: "<consumer-secret>"
    message_template: "Rewards have been paid with TezPay."
  }
]
```

Twitter/X developer settings change over time. If the portal labels differ, look for app permissions, user authentication settings, and access token generation.

---

## Email

Use email notifications when you want TezPay to send payout messages through an SMTP server.

```hjson
notifications: [
  {
    type: email
    sender: "baker@example.com"
    smtp_server: "smtp.example.com:465"
    smtp_identity: ""
    smtp_username: "baker@example.com"
    smtp_password: "<smtp-password>"
    recipients: [
      "delegate-one@example.com"
      "delegate-two@example.com"
    ]
    message_template: "Rewards have been paid with TezPay."
  }
]
```

Use an app-specific SMTP password when your email provider supports it.

---

## Multiple Notifications

You can configure multiple notification targets in the same `notifications` list:

```hjson
notifications: [
  {
    type: discord
    webhook_url: "https://discord.com/api/webhooks/..."
    message_template: "Rewards have been paid with TezPay."
  }
  {
    type: email
    sender: "baker@example.com"
    smtp_server: "smtp.example.com:465"
    smtp_identity: ""
    smtp_username: "baker@example.com"
    smtp_password: "<smtp-password>"
    recipients: [
      "delegate@example.com"
    ]
    message_template: "Rewards have been paid with TezPay."
  }
]
```

---

## Related Guides

* [TezPay Setup](/tezpay/tutorials/setup/) - Initial TezPay configuration
* [Paying Delegators](/tezpay/tutorials/paying-delegators/) - Run payouts manually or continuously
* [TezWatch Setup](/tezwatch/tutorials/setup/) - Discord baker alerts

---

Any questions/comments/concerns? Please contact the Tez Capital team on
[Discord](https://discord.gg/cVGMA4MaNM) or [Telegram](https://t.me/tezcapital)
