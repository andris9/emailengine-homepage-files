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

# Webhooks

Using webhooks with EmailEngine

## Event list

EmailEngine can notify your application about mailbox, delivery, and tracking events by sending an HTTP POST request—called a *webhook*—to a URL you define. Each request contains a JSON payload that describes the event. Enable or disable individual events from **Configuration → Webhooks**.

| Event                                         | Name                   | Description                                                            |
| --------------------------------------------- | ---------------------- | ---------------------------------------------------------------------- |
| [messageNew](#messageNew)                     | New email              | A new email was detected in a folder.                                  |
| [messageDeleted](#messageDeleted)             | Message deleted        | A previously present email was removed from a folder.                  |
| [messageUpdated](#messageUpdated)             | Flags updated          | Flags of an email were updated.                                        |
| `messageMissing`                              | Message missing        | An expected message was not found; indicates a sync error.             |
| [messageSent](#messageSent)                   | Message accepted       | A queued email was accepted by the MTA.                                |
| [messageDeliveryError](#messageDeliveryError) | SMTP error             | Delivery to the SMTP server failed; EmailEngine retries automatically. |
| [messageFailed](#messageFailed)               | Message bounced        | Delivery to the MTA failed permanently.                                |
| [messageBounce](#messageBounce)               | Bounce received        | A bounce email was received.                                           |
| [messageComplaint](#messageComplaint)         | Complaint received     | A feedback‑loop complaint was detected.                                |
| `mailboxReset`                                | Mailbox reset          | **UIDValidity** of a folder changed.                                   |
| `mailboxDeleted`                              | Folder deleted         | A previously present folder was removed.                               |
| `mailboxNew`                                  | New folder             | A new folder was detected.                                             |
| `authenticationError`                         | Authentication failure | Account authentication failed.                                         |
| `authenticationSuccess`                       | Authentication success | Account authenticated successfully.                                    |
| `connectError`                                | Connection failure     | Connection to the email server failed.                                 |
| `accountAdded`                                | Account added          | A new account was registered with EmailEngine.                         |
| `accountInitialized`                          | Account initialized    | Initial sync completed for an account.                                 |
| `accountDeleted`                              | Account deleted        | An account was removed from EmailEngine.                               |
| [trackOpen](#trackOpen)                       | Email opened           | A recipient opened an email.                                           |
| [trackClick](#trackClick)                     | Link clicked           | A recipient clicked a link in an email.                                |
| [listUnsubscribe](#listUnsubscribe)           | Unsubscribed           | A recipient unsubscribed from a list.                                  |
| `listSubscribe`                               | Subscribed             | A recipient resubscribed to a list.                                    |

### `messageNew` webhook

Triggered whenever a message is **added** to a folder—whether by delivery, upload, copy, or move. IMAP itself does not distinguish between incoming messages and messages inserted by other means.

> **Note**\
> The example below has **Text content** enabled, therefore `data.text.plain` and `data.text.html` are present. By default these fields are omitted. Enable them via **Configuration → Webhooks → Text content → Include text content in new email webhooks**, or set `notifyText: true` via the [Settings API](https://api.emailengine.app/#operation/postV1Settings).

```json
{
  "serviceUrl": "https://emailengine-dev.apps.srv.dev",
  "account": "postalsys",
  "date": "2023-03-14T11:13:10.609Z",
  "path": "[Gmail]/All Mail",
  "specialUse": "\\All",
  "event": "messageNew",
  "data": {
    "id": "AAAAAQABDFI",
    "uid": 68690,
    "path": "[Gmail]/All Mail",
    "emailId": "1760341395373354319",
    "threadId": "1760341395373354319",
    "date": "2023-03-14T11:12:45.000Z",
    "flags": [],
    "labels": [
      "\\Important",
      "\\Inbox"
    ],
    "unseen": true,
    "size": 8271,
    "subject": "Impressed by the new features of EmailEngine!",
    "from": {
      "name": "Andris Reinman",
      "address": "andris.reinman@gmail.com"
    },
    "replyTo": [
      {
        "name": "Andris Reinman",
        "address": "andris.reinman@gmail.com"
      }
    ],
    "sender": {
      "name": "Andris Reinman",
      "address": "andris.reinman@gmail.com"
    },
    "to": [
      {
        "name": "",
        "address": "andris@postalsys.com"
      }
    ],
    "messageId": "<CAPacwgwKMETwvftKuQyieSKmp1C+NJcZtqjZ2pSALZkjB-CCbQ@mail.gmail.com>",
    "text": {
      "id": "AAAAAQABDFKTkaExkaEykA",
      "encodedSize": {
        "plain": 1535,
        "html": 1630
      },
      "plain": "Dear Postal Systems OÜ,\n\nI hope this email finds you well...\n",
      "html": "<div dir=\"ltr\">Dear Postal Systems OÜ,<br><br>I hope this email finds you well...</div>\n",
      "hasMore": false
    },
    "category": "primary",
    "isAutoReply": false,
    "seemsLikeNew": true,
    "messageSpecialUse": "\\Inbox"
  },
  "_route": {
    "id": "AAABhpMgNhsAAAAE"
  }
}
```

#### Field reference

* **\_route** – present when the event is delivered through a Webhook Router.
* **serviceUrl** – base URL of the EmailEngine instance that generated the event.
* **account** – EmailEngine account ID that owns the mailbox.
* **path** – IMAP folder path that generated the event.
* **specialUse** – special‑use flag of the folder. For Gmail this is almost always `\All`; see `data.messageSpecialUse` for the logical folder.
* **data.messageSpecialUse** – special‑use flag that best classifies the message (for Gmail this is typically `\Inbox`). Prefer this over `specialUse`.
* **data.id** – EmailEngine internal message ID.
* **data.uid** – IMAP UID.
* **data.emailId / data.threadId** – Gmail‑specific identifiers, when available.
* **data.messageId** – RFC 5322 `Message‑ID` header value.
* **data.date** – original message `Date` header, in ISO 8601.
* **data.flags** – IMAP flags applied when the event was generated.
* **data.labels** – Gmail labels applied when the event was generated.
* **data.unseen** – `true` if the message *does not* have the `\Seen` flag.
* **data.size** – full RFC 822 size in bytes.
* **data.category** – Gmail inbox category tab (Primary, Social, etc.). Requires **Labs → Resolve Gmail categories**.
* **data.isAutoReply** – `true` if EmailEngine detects an auto‑reply (vacation, bounce, etc.).
* **data.seemsLikeNew** – `true` if EmailEngine has no prior record of this message (≈ 99 % accuracy).
* **data.text** – object with parsed `plain` and `html` bodies. Included only when **Text content** is enabled.

All other top‑level fields (`from`, `to`, `sender`, `subject`, …) mirror their respective RFC 5322 headers.

#### Gmail tab categories

Gmail sorts incoming emails into different categories—*promotions*, *social*, *primary*, *updates*, and *forums*—and displays these emails under the corresponding category tabs. Over IMAP, all of these emails still appear in the `INBOX` folder. EmailEngine can resolve the category of incoming emails and include this information in the payload of the `messageNew` webhook.

Category resolution is not enabled by default. To enable it, navigate to *Configuration* → *Service*, and then to the *Labs* section. Mark the **Resolve Gmail categories** checkbox and save the settings.

![](https://cldup.com/WXAO6j0uEU.png)

Now, whenever an email lands in the inbox of an email account that EmailEngine tracks, the `messageNew` webhook for that email includes a property called `category`, whose value is one of the following: *social*, *promotions*, *updates*, *forums*, or *primary*.

### `messageDeleted`

Emitted when a message disappears from a folder—either permanently deleted or moved elsewhere.

```json
{
  "account": "pangalink",
  "date": "2022-01-17T11:54:02.089Z",
  "path": "[Gmail]/All Mail",
  "specialUse": "\\All",
  "event": "messageDeleted",
  "data": {
    "id": "AAAAAQAANJc",
    "uid": 13463
  }
}
```

### `messageUpdated`

Emitted whenever EmailEngine detects that an existing message has changed — most often when its flags are updated (for example, when the user marks the message **read** or **unread**).

#### Payload

```json
{
  "account": "pangalink",
  "date": "2022-01-17T11:56:13.146Z",
  "path": "[Gmail]/All Mail",
  "specialUse": "\\All",
  "event": "messageUpdated",
  "data": {
    "id": "AAAAAQAANJY",
    "uid": 13462,
    "changes": {
      "flags": {
        "added": ["\\Seen"],
        "removed": []
      }
    }
  }
}
```

`changes.flags.added` and `changes.flags.removed` contain only the deltas: the flags added to or removed from the message since the previous state.

### `messageSent`

Emitted after **EmailEngine** has successfully handed the message off to the upstream mail server (MTA). The webhook is generated as soon as the server acknowledges acceptance.

#### Payload

```json
{
  "account": "<accountId>",
  "date": "<ISO-8601 timestamp>",
  "event": "messageSent",
  "data": {
    "messageId": "<final Message-ID>",
    "response": "<raw SMTP or API response>",
    "envelope": {
      "from": "<sender address>",
      "to": ["<recipient address>"]
    }
  }
}
```

#### Message‑ID rewriting

Some MTAs (e.g. Amazon SES, AWS WorkMail, Microsoft Graph) replace the original **Message‑ID** header. When EmailEngine detects this, it adds `originalMessageId` to the payload while `messageId` is updated to the value returned by the server.

```json
{
  "account": "<accountId>",
  "date": "<ISO-8601 timestamp>",
  "event": "messageSent",
  "data": {
    "messageId": "<rewritten Message-ID>",
    "originalMessageId": "<Message-ID generated by EmailEngine>",
    "response": "<raw SMTP or API response>",
    "envelope": {
      "from": "<sender address>",
      "to": ["<recipient address>"]
    }
  }
}
```

`messageId` always contains the final value stored on the message accepted by the remote server.

### messageFailed

Emitted when EmailEngine abandons delivery after all retry attempts fail.\
Because the message is retried in the background, this webhook can be delayed until the final attempt has completed.

```json
{
  "account": "example",
  "date": "2021-12-23T11:58:50.181Z",
  "event": "messageFailed",
  "data": {
    "messageId": "<97ac5d9a-93c7-104b-8d26-6b25f8d644ec@ekiri.ee>",
    "queueId": "610c2c93e608bd37",
    "error": "Error: Invalid login: 535 5.7.8 Error: authentication failed: "
  }
}
```

### messageComplaint

Emitted when EmailEngine detects an ARF‑formatted feedback loop (FBL) report in **Inbox**. The report is parsed and posted as a `messageComplaint` webhook.

> **Field coverage depends on the reporting provider. Headers such as *Message‑ID* may be missing.**

```json
{
  "serviceUrl": "http://127.0.0.1:3000",
  "account": "example",
  "date": "2022-09-27T12:33:42.635Z",
  "event": "messageComplaint",
  "data": {
    "complaintMessage": "AAAAAQAABvE",
    "arf": {
      "source": "Hotmail",
      "feedbackType": "abuse",
      "abuseType": "complaint",
      "originalRcptTo": [
        "recipient@hotmail.co.uk"
      ],
      "sourceIp": "1.2.3.4",
      "arrivalDate": "2021-10-22T13:04:36.017Z"
    },
    "headers": {
      "messageId": "<67d3846d-b819-9c54-5053-a81fdc432425@example.com>",
      "from": "user@example.com",
      "to": [
        "recipient@hotmail.co.uk"
      ],
      "subject": "Hello world",
      "date": "2021-10-22T16:04:33.000Z"
    }
  }
}
```

### messageDeliveryError

Emitted after every failed delivery attempt. EmailEngine keeps retrying until either the message is delivered or the maximum number of attempts is reached. You receive one `messageDeliveryError` webhook per attempt.

```json
{
  "serviceUrl": "http://127.0.0.1:3000",
  "account": "example",
  "date": "2022-09-14T15:07:35.832Z",
  "event": "messageDeliveryError",
  "data": {
    "queueId": "1833c8a88a86109a1bf",
    "envelope": {
      "from": "andris@ekiri.ee",
      "to": [
        "andris@ethereal.email"
      ]
    },
    "messageId": "<29e26263-7125-ff56-4f80-83a5cf737d5e@ekiri.ee>",
    "error": "400 Message Not Accepted",
    "errorCode": "EPROTOCOL",
    "smtpResponseCode": 400,
    "job": {
      "attemptsMade": 1,
      "attempts": 10,
      "nextAttempt": "2022-09-14T15:07:45.465Z"
    }
  }
}
```

### messageBounce

Emitted when EmailEngine receives a bounce (DSN) message.

* The payload includes `messageId` but no internal `id`. Use *Message‑ID* to correlate with the original email.
* If a message sent to multiple recipients bounces for several addresses, EmailEngine emits a separate `messageBounce` event for each recipient.

> **Field coverage depends on the bounce format and what EmailEngine can parse.**

```json
{
  "account": "pangalink",
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

### trackOpen

Emitted when the tracking pixel embedded in the email is requested.

> Requires **Configuration → Service → Labs → Track opens and clicks** to be enabled.

False positives are possible, for example when a webmail client caches linked images.

```json
{
  "account": "example",
  "date": "2022-03-24T08:28:32.992Z",
  "event": "trackOpen",
  "data": {
    "messageId": "<2d4696ea-cb47-7af4-0bc3-81ea7a8008be@example.com>",
    "remoteAddress": "1.2.3.4",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.83 Safari/537.36"
  }
}
```

### trackClick

Emitted when a tracked link is requested.

> Requires **Configuration → Service → Labs → Track opens and clicks** to be enabled.

False positives may occur when security software or link scanners pre‑fetch URLs.

```json
{
  "account": "example",
  "date": "2022-03-24T08:27:24.572Z",
  "event": "trackClick",
  "data": {
    "messageId": "<49b6cdb1-5d43-e100-0833-bd61867b7bb3@example.com>",
    "url": "https://google.com/",
    "remoteAddress": "1.2.3.4",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.83 Safari/537.36"
  }
}
```

### listUnsubscribe

Emitted when a recipient clicks the **List‑Unsubscribe** link or when the email client issues a one‑click unsubscribe request.

```json
{
  "account": "example",
  "date": "2022-03-24T08:27:24.572Z",
  "event": "listUnsubscribe",
  "data": {
    "recipient": "recipient@example.com",
    "messageId": "<49b6cdb1-5d43-e100-0833-bd61867b7bb3@example.com>",
    "listId": "my-customers-list",
    "remoteAddress": "1.2.3.4",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.83 Safari/537.36"
  }
}
```

## Webhook headers

Define additional HTTP headers for webhook requests with the `webhooksCustomHeaders` configuration option or via **Configuration → Webhooks** in the dashboard.

EmailEngine also injects diagnostic headers into every request:

| Header                    | Example value                          | Description                                                     |
| ------------------------- | -------------------------------------- | --------------------------------------------------------------- |
| **X‑EE‑Wh‑Event‑Id**      | `af8435d9-ceee-4715-be71-08ac9d2dc04a` | UUID for the event. All retries share the same ID.              |
| **X‑EE‑Wh‑Custom‑Route**  | `AAABiL8tBKsAAAAG`                     | Identifier of the custom webhook route.                         |
| **X‑EE‑Wh‑Queued‑Time**   | `0s`                                   | Time the event spent in the queue before the POST.              |
| **X‑EE‑Wh‑Attempts‑Made** | `1`                                    | Attempt counter. Values > 1 indicate earlier delivery failures. |
| **X‑EE‑Wh‑Id**            | `907889`                               | ID of the queued webhook entry.                                 |

## Webhook retries

* EmailEngine makes **up to 10** delivery attempts per webhook. After the 10th failure the payload is marked **undeliverable**.

* Exponential back‑off, base delay **5000 ms**:

  ```text
  delay = 5000 ms × 2^(attempt − 1)
  ```

* Jobs live in the **notify** queue (**Tools → Bull Board → notify**).

  * Pending retries: **Delayed** section.
  * Undeliverable: **Failed** section.

* Retention limits for completed/failed jobs are set in **Configuration → Service → Queue Settings**.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
