[![Your Logo](lib_pTNsKLAHHUZrxQKE/xwb20trbbhmhskes.png?w=160)](/)

[Download](/#downloads)

Using EmailEngine

[API reference](https://api.emailengine.app/)

[Authenticating API requests](/authenticating-api-requests)

[Hosted authentication](/hosted-authentication)

[Webhooks](/webhooks)

[Sending emails](/sending-emails)

[Supported Account Types](/supported-account-types)

[OAuth2 configuration](/oauth2-configuration)

[Bounce detection](/bounces)

[Email templates](/email-templates)

[Shared Mailboxes in MS365](/shared-mailboxes-in-ms-365)

[Virtual mailing lists and unsubscribe](/virtual-mailing-lists)

[Pre-processing functions](/pre-processing-functions)

Operating EmailEngine

[Installation instructions](/set-up)

[Redis requirements](/redis)

[Configuration options](/configuration)

[Reset password](/reset-password)

[Run as a SystemD service](/system-d-service)

[Run as a Docker container](/docker)

[Monitoring](/monitoring)

[Log management](/logging)

[Local IP-addresses](/local-addresses)

[Prepared settings](/prepared-settings)

[Prepared access token](/prepared-access-token)

[Prepared license key](/prepared-license)

[Troubleshooting](/troubleshooting)

[Use Nginx as a proxy](/expose-public-https)

[FAQ](/#faq)[Blog](https://docs.emailengine.app/)[Support](/support)

[Get a license key](https://postalsys.com/plans)

Menu

* [Download](/#downloads)
* Using EmailEngine

  [API reference](https://api.emailengine.app/)

  [Authenticating API requests](/authenticating-api-requests)

  [Hosted authentication](/hosted-authentication)

  [Webhooks](/webhooks)

  [Sending emails](/sending-emails)

  [Supported Account Types](/supported-account-types)

  [OAuth2 configuration](/oauth2-configuration)

  [Bounce detection](/bounces)

  [Email templates](/email-templates)

  [Shared Mailboxes in MS365](/shared-mailboxes-in-ms-365)

  [Virtual mailing lists and unsubscribe](/virtual-mailing-lists)

  [Pre-processing functions](/pre-processing-functions)
* Operating EmailEngine

  [Installation instructions](/set-up)

  [Redis requirements](/redis)

  [Configuration options](/configuration)

  [Reset password](/reset-password)

  [Run as a SystemD service](/system-d-service)

  [Run as a Docker container](/docker)

  [Monitoring](/monitoring)

  [Log management](/logging)

  [Local IP-addresses](/local-addresses)

  [Prepared settings](/prepared-settings)

  [Prepared access token](/prepared-access-token)

  [Prepared license key](/prepared-license)

  [Troubleshooting](/troubleshooting)

  [Use Nginx as a proxy](/expose-public-https)
* [FAQ](/#faq)
* [Blog](https://docs.emailengine.app/)
* [Support](/support)
* [Get a license key](https://postalsys.com/plans)

# Bounces

Built-in Bounce Detection

EmailEngine automatically scans incoming emails for bounce responses. When a bounce is detected, EmailEngine sends a webhook notification. Additionally, when listing emails, any bounce information associated with a specific message is included in the message details as a `bounces` array.

> **Note:** EmailEngine does not use [VERP addresses](https://en.wikipedia.org/wiki/Variable_envelope_return_path). Since EmailEngine sends emails via standard channels and not directly, it can only detect bounces that include standard bounce information. Custom, human-readable messages without metadata markers may not be detected. Fortunately, the majority of email servers use standard bounce formats that EmailEngine can process.

## Bounce Webhooks

When EmailEngine detects an incoming email, it checks to see if the email is a bounce response. If so, it sends a webhook with the event type `messageBounce`.

Because EmailEngine cannot immediately identify the original message that triggered the bounce, the webhook payload does not include a `"id"` field. Instead, the `messageId` field is provided to help you match the bounce to a message in your system.

An example of a bounce webhook payload:

```json
{
  "account": "account_id",
  "date": "2021-11-28T10:17:29.728Z",
  "event": "messageBounce",
  "data": {
    "bounceMessage": "AAAAAQAABWw",
    "recipient": "failed.recipient@example.com",
    "action": "failed",
    "response": {
      "source": "smtp",
      "message": "550 5.1.1 <failed.recipient@example.com>: Recipient address rejected: User unknown in relay recipient table",
      "status": "5.1.1"
    },
    "mta": "mx.example.com",
    "queueId": "BFC608226A",
    "messageId": "<4dbe4a40f37e9c2ba5b25912bc7c8997@example.com>"
  }
}
```

Note that some fields might be missing depending on how much information EmailEngine can extract from the bounce response.

## Bounces in Message Listings

When listing messages in the Sent Mail folder, EmailEngine checks for bounces associated with each listed message. If a bounce is detected, it is included in the message entry as a `bounces` array.

Here’s an example message listing that includes bounce information:

```json
{
  "id": "AAAAAgAAAZ0",
  "uid": 413,
  "date": "2021-11-28T10:17:27.000Z",
  "size": 839,
  "subject": "Example message",
  "from": {
    "name": "Sender Name",
    "address": "sender@example.com"
  },
  "to": [
    {
      "name": "",
      "address": "failed.recipient@example.com"
    }
  ],
  "messageId": "<4dbe4a40f37e9c2ba5b25912bc7c8997@example.com>",
  "bounces": [
    {
      "message": "AAAAAQAABWw",
      "recipient": "failed.recipient@example.com",
      "action": "failed",
      "response": {
        "message": "550 5.1.1 <failed.recipient@example.com>: Recipient address rejected: User unknown in relay recipient table",
        "status": "5.1.1"
      },
      "date": "2021-11-28T10:17:29.722Z"
    }
  ]
}
```

## Retrieving Bounce Content

To retrieve the actual contents of a bounce message, you can use the `data.bounceMessage` value from the webhook payload or the `bounces[].message` value from the message listing.

For example, to fetch the bounce content for `AAAAAQAABWw`, use the following `curl` command:

```
$ curl "http://localhost:3000/v1/account/ekiri/message/AAAAAQAABWw?textType=*" \
    -H 'Authorization: Bearer d2d438e2a965...'
```

The response will return the full content of the bounce message:

```json
{
  "id": "AAAAAQAABWw",
  "uid": 1388,
  "date": "2021-11-28T10:17:28.000Z",
  "unseen": true,
  "size": 3611,
  "subject": "Undelivered Mail Returned to Sender",
  "from": {
    "name": "Mail Delivery System",
    "address": "MAILER-DAEMON@smtp.example.com"
  },
  "to": [
    {
      "name": "",
      "address": "sender@example.com"
    }
  ],
  "attachments": [
    {
      "id": "AAAAAQAABWwy",
      "contentType": "message/delivery-status",
      "encodedSize": 480,
      "filename": false,
      "embedded": false,
      "inline": false
    },
    {
      "id": "AAAAAQAABWwz",
      "contentType": "message/rfc822",
      "encodedSize": 1437,
      "filename": false,
      "embedded": false,
      "inline": false
    }
  ],
  "messageId": "<20211128101728.144CB82458@smtp.example.com>",
  "text": {
    "plain": "This is the mail system at host smtp.example.com.\n\nI'm sorry to have to inform...",
    "html": "<!DOCTYPE html PUBLIC \"-//W3C//DTD XHTML 1.0 Transitional//EN\">\n<html><body style=...",
    "hasMore": false
  }
}
```

## Bounce Types

There are various reasons why an email might bounce. EmailEngine identifies these types of bounces.

[![SoWkIImgAStDuN8iA53GjLDmpKtCp77DIy\_CIxLIi4a5yUoua0H1Qe9zGm8GaY0UXavJ0mC5nzAIZDIyaipan9BC\_3m5Dw1qQWgwMK1hWKRbfXON9wQdkgJcP9Qb5bMw5XNb5w5OYs51Nhv2RdvHga9HQabHPcenNcCXDjjnEQJcfG2z2W00.png](lib_YPDxAnubLfzlHjLq/z2bolquggvj8yxic.png?w=400\&h=200\&fit=crop)](https://cloudup.com/ikfKrxtMPtE)

### [messageFailed](https://cloudup.com/ikfKrxtMPtE)

[The SMTP server did not accept the email for delivery.](https://cloudup.com/ikfKrxtMPtE)

[![file.png](lib_YPDxAnubLfzlHjLq/s7s8bq18h16zdwfn.png?w=400\&h=200\&fit=crop)](https://cloudup.com/iDlfPlTWjV7)

### [messageBounce](https://cloudup.com/iDlfPlTWjV7)

[The email was accepted by the SMTP server but was later rejected by the recipient's mail server (MX server).](https://cloudup.com/iDlfPlTWjV7)

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
