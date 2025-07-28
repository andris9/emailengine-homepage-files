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

# Virtual Mailing Lists

Allow recipients to unsubscribe

When using [Mail Merge](https://docs.emailengine.app/mail-merge-with-emailengine/) to send emails, EmailEngine allows specifying an additional property, `listId`. If this value is set, EmailEngine treats the message as a virtual mailing list email.

EmailEngine does not have "real" mailing list capabilities—there are no contact lists, segmentation rules, or recorded history. Almost everything you'd expect from a mailing list manager is missing. Virtual mailing lists provide only a single feature: **list-unsubscribe**.

Sometimes, for legal reasons, it might be necessary to offer an unsubscribe option to recipients. Building all the required components only to support unsubscribing can be tedious, so EmailEngine provides this feature for you.

## Using `listId`

All Mail Merge emails sent with the same `listId` value are grouped into the same virtual list. If a recipient decides to unsubscribe from such a list, any subsequent emails with the same `listId` to that recipient will be ignored and not sent.

Virtual lists are global and not bound to any specific email account. For example, if you provide a SaaS service, you can assign all seats of a single organization to the same virtual list. If a recipient unsubscribes from an email sent by one account of an organization, other accounts of the same organization can't send mail to them anymore either.

## Unsubscribe Flow

![Screenshot\_2022-11-11\_at\_11.53.12.png](lib_pTNsKLAHHUZrxQKE/c1vp33nrzoicfvj3.png?w=600\&h=200\&fit=crop)

### Unsubscribe Link in the Email

Each recipient of the Mail Merge operation will get a unique unsubscribe link. Compatible email clients will also display a one-click unsubscribe link or button.

![Screenshot\_2022-11-11\_at\_11.58.34.png](lib_YPDxAnubLfzlHjLq/puhlr5lcr4i9v8pw.png?w=600\&h=200\&fit=crop)

### Unsubscribe Form

If the recipient clicks on the unsubscribe link, they are directed to an unsubscribe form hosted by EmailEngine, where they can confirm their request. If needed, they can re-subscribe if they change their mind.

## Sending List Emails

The format for the `listId` value is a subdomain name. These IDs do not require any registration, and you can come up with them ad hoc.

In addition to the `listId` value, there's an extra template variable `rcpt.unsubscribeUrl` that is replaced by an unsubscribe URL for the specific recipient.

Example:

```bash
curl -XPOST "https://example.com/v1/account/example/submit" \
  -H "Content-type: application/json" \
  -H "Authorization: Bearer test123" \
  -d '{
    "subject": "List email",
    "html": "<a href=\"{{ rcpt.unsubscribeUrl }}\">Unsubscribe</a>",
    "mailMerge": [
      {
        "to": {
          "name": "Andris Reinman",
          "address": "andris@ethereal.email"
        }
      }
    ],
    "listId": "my.special.list"
  }'
```

> **Note:** If `andris@ethereal.email` clicks on the unsubscribe link, any subsequent emails with the same `listId` sent to `andris@ethereal.email` will not be sent.

## Manually Blocking Recipients

If you want to manually unsubscribe recipients from specific lists, you can use the [Blocklist API](https://api.emailengine.app/#tag/Blocklists).

Example:

```bash
curl -XPOST "https://example.com/v1/blocklist/my.special.list" \
  -H "Content-type: application/json" \
  -H "Authorization: Bearer test123" \
  -d '{
    "account": "example",
    "recipient": "andris@ethereal.email",
    "reason": "manual block"
  }'
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
