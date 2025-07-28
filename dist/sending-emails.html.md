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

# Sending Emails with EmailEngine

**EmailEngine** simplifies sending emails through registered accounts' SMTP servers or external sending services, making email management easier for applications without storing account credentials.

When you send an email using EmailEngine, it first queues the email for delivery. Then, it attempts to transfer the message to the account's SMTP server or the selected sending service.

By using EmailEngine, you avoid directly managing the credentials of individual email accounts. Instead, you only need access to EmailEngine and the account ID from which you wish to send the email.

> See also: Sending emails using [Email Templates and Mail-Merge](https://emailengine.app/email-templates).

## API

Sending emails using the API

To send emails using the API, use the [*submit* API endpoint](https://api.emailengine.app/#operation/postV1AccountAccountSubmit).

**Example Request:**

```bash
curl -XPOST "http://127.0.0.1:3000/v1/account/example/submit" \
    -H "Authorization: Bearer f77cf263b704...9793022df08d91b9" \
    -H "Content-type: application/json" \
    -d '{
      "from": {
        "name": "Andris Reinman",
        "address": "andris@example.com"
      },
      "to": [
        {
          "name": "Ethereal",
          "address": "andris@ethereal.email"
        }
      ],
      "subject": "Test message",
      "text": "Hello from myself!",
      "html": "<p>Hello from myself!</p>"
    }'
```

> **Tip:** The `from` address is optional. If not provided, EmailEngine uses the registered account email.

**Response Example:**

```json
{
  "response": "Queued for delivery",
  "messageId": "<78aa92af-67c9-4130-b1bf-c22feb2ec3f6@example.com>",
  "sendAt": "2022-03-24T10:23:38.196Z",
  "queueId": "17fbb7408d44367d9b7"
}
```

### Sending Raw Messages

For sending a pre-formatted RFC822 email, submit it as a base64-encoded `raw` property value. If the `envelope` property is not specified, recipients are derived from the email headers.

**Example:**

```bash
curl -XPOST "http://127.0.0.1:3000/v1/account/example/submit" \
    -H "Authorization: Bearer f77cf263b704...9793022df08d91b9" \
    -H "Content-type: application/json" \
    -d "{
      \"raw\" : \"` echo 'From: andris@example.com
To: andris@ethereal.email
Subject: test message
Mime-Version: 1.0

Hello world!
' | base64`\"
    }"
```

## SMTP Interface

Sending via the bundled SMTP interface

To use EmailEngine’s bundled SMTP interface, enable it from the SMTP Interface configuration page.

![](https://lib.umso.co/lib_pTNsKLAHHUZrxQKE/24t5it4ipnsg1fot.png)

Once enabled, configure your email sending library to use EmailEngine’s SMTP server. The SMTP username is the account ID, and the password is the `SMTP password` from the configuration page.

> Note: EmailEngine’s SMTP server does not support TLS/SSL encryption, so make sure these settings are not configured.

**Example SMTP Response:**

```
250 Message queued for delivery as 17fbb0a97504803a15a (2022-03-24T08:28:27.856Z)
```

### Client Library Examples:

**Nodemailer:**

```js
const transporter = nodemailer.createTransport({
    host: 'localhost',
    port: 2525,
    auth: {
        user: 'example',
        pass: 'a57518a9e8858187463d53d5b7d3aa0c'
    }
});
```

**PHPMailer:**

```php
$mail = new PHPMailer(true);
$mail->isSMTP();
$mail->Host = 'localhost';
$mail->SMTPAuth = true;
$mail->Username = 'example';
$mail->Password = 'PUg6PzUe3ErkAKUqJ6';
$mail->Port = 2525;
```

**SwiftMailer:**

```php
$transport = (new Swift_SmtpTransport('localhost', 2525))
  ->setUsername('example')
  ->setPassword('a57518a9e8858187463d53d5b7d3aa0c');
```

## OAuth2 Integration

### Gmail Accounts

Follow the steps for setting up OAuth2 with Gmail [here](https://docs.emailengine.app/setting-up-gmail-oauth2-for-imap-api/).

### Gmail Service Accounts

Instructions for setting up OAuth2 for service accounts can be found [here](https://docs.emailengine.app/gmail-oauth-service-accounts/).

### Microsoft Accounts

Guide for OAuth2 setup for Microsoft accounts can be found [here](https://docs.emailengine.app/setting-up-oauth2-with-outlook/).

## Open and Click Tracking

Tracking user action events

EmailEngine provides optional open and click tracking, which rewrites outgoing email HTML to route links through the tracking system. Tracked events generate webhook notifications for opens and clicks.

### Enabling Tracking

1. Navigate to the Service Configuration page.
2. Enable "Sent Email Tracking."
3. Configure webhooks for `trackOpen` and `trackClick` events.

### API Options for Tracking

To enable tracking for a specific message, use the `trackClicks` and `trackOpens` options:

```bash
curl -XPOST "http://127.0.0.1:3000/v1/account/example/submit" \
    -H "Authorization: Bearer ..." \
    -H "Content-type: application/json" \
    -d '{
      "trackClicks": true,
      "trackOpens": true
    }'
```

Alternatively, use the `X-EE-Tracking-Enabled` SMTP header:

```
X-EE-Tracking-Enabled: true
```

**Sample Webhook for `trackOpen`:**

```json
{
  "account": "example",
  "event": "trackOpen",
  "data": {
    "messageId": "<id@example.com>",
    "remoteAddress": "1.2.3.4",
    "userAgent": "Mozilla/5.0 ..."
  }
}
```

## Avoiding duplicates

Sending requests might occasionally time out or get killed, which does not necessarily mean the email was not sent. If a request times out, there's no reliable way to check if the email was queued or processed, so retrying might result in duplicate deliveries.

To avoid this, EmailEngine supports an **Idempotency-Key**. The Idempotency-Key is a unique identifier you provide to mark a specific email sending request. If an email has already been sent with the same key, EmailEngine will not resend it but will return the same response as before. You can safely retry sending without creating duplicates.

To use this feature, include the Idempotency-Key value.

#### Using the API

Add the Idempotency-Key header to the API request:

```
curl -X 'POST' \
  'https://ee.example/v1/account/example/submit' \
  -H 'Idempotency-Key: UNIQUE_VALUE' \
  -H 'Content-Type: application/json' \
  -d '{...}'
```

#### Using the SMTP gateway

Add the X-EE-Idempotency-Key message header:

```
From: andris@headless.email
To: Andris Ethereal <andris@ethereal.email>
X-EE-Idempotency-Key: UNIQUE_VALUE
```

Use the same Idempotency-Key value whenever retrying the same email.

## Delivery Delays

Send emails at a specific time

Schedule email delivery by specifying a `sendAt` time. Example:

```bash
curl -XPOST "http://127.0.0.1:3000/v1/account/example/submit" \
    -H "Authorization: Bearer ..." \
    -H "Content-type: application/json" \
    -d '{
      "sendAt": "2023-07-08T07:06:34.336Z"
    }'
```

## SMTP Gateways

Send emails through an external service

Send emails through external services like SendGrid, Mailgun, or AWS SES by registering a gateway via API.

**Register Gateway Example:**

```bash
curl -XPOST "http://127.0.0.1:3000/v1/gateway" \
    -H "Authorization: Bearer ..." \
    -d '{
      "gateway": "sendgrid",
      "user": "apikey",
      "pass": "<YOUR_API_KEY>"
    }'
```

Then, send emails via the gateway by specifying it in the request:

```bash
curl -XPOST "http://127.0.0.1:3000/v1/account/example/submit" \
    -H "Authorization: Bearer ..." \
    -d '{
      "gateway": "sendgrid"
    }'
```

## Outbox Management

Managing queued emails

EmailEngine allows you to list and delete emails that have been queued but are not yet transferred to the account's SMTP server. Outbox includes all emails that are scheduled to be delivered in the future, and it also contains emails that should have been sent but for whatever reason are not – for example, the SMTP server is currently not accessible.

Outbox is global as it includes queued emails for all accounts. It is not account-specific.

You can access the Outbox using [the *outbox* API endpoints](https://api.emailengine.app/#tag/outbox).

## Bounces

You can read about detecting and handling bounces from [here](https://emailengine.app/bounces).

## Replies and forwarding

EmailEngine includes helpers that mimic regular email clients when replying or forwarding emails. Read about it [here](https://docs.emailengine.app/sending-reply-and-forward-emails/).

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
