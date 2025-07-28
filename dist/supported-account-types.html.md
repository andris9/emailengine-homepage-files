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

# Supported Account Types

Email backends that EmailEngine can connect to

EmailEngine supports various email backends to interact with users' email accounts. This document outlines the different protocols and services that EmailEngine can connect to, including their specific requirements and considerations.

## IMAP and SMTP

IMAP and SMTP are the most common protocols used to access email accounts. Nearly every email hosting provider supports these protocols. EmailEngine uses IMAP and SMTP as the default connection methods.

### IMAP and SMTP over OAuth2

Many OAuth2-capable email hosting providers support OAuth2 authentication over IMAP and SMTP. This means that when requesting permissions to access an email account, a standard OAuth2 authorization flow is used. When a user grants EmailEngine permission to access their email account, they are granting access to IMAP and SMTP using OAuth2 tokens rather than traditional username and password authentication.

When EmailEngine establishes an IMAP or SMTP session, it uses the OAUTHBEARER or XOAUTH2 SASL extensions to authenticate, similar to connecting to a regular IMAP or SMTP account.

#### Gmail

For [Gmail](https://docs.emailengine.app/setting-up-gmail-oauth2-for-imap-api/), the required OAuth2 scope is `https://mail.google.com/`. Note that this is a "restricted" scope and requires a mandatory security review. Unless your use case requires the ability to permanently delete emails, it is unlikely that Google will grant you this scope. In such cases, you might want to consider using the Gmail API as the backend instead.

With IMAP and SMTP, Gmail counts data traffic and the number of email recipients. These limit counters are account-wide, meaning that if multiple email clients are connected to the account and collectively download a large amount of data or send emails to many recipients, they might hit the daily data transfer limit. When this limit is reached, IMAP access is blocked for all email clients for that email account.

#### Microsoft 365/Outlook

For [Microsoft 365 (MS365) and Outlook](https://docs.emailengine.app/setting-up-oauth2-with-outlook/), the required OAuth2 scopes are `IMAP.AccessAsUser.All` and `SMTP.Send`. Be aware that many MS365 organizations have the SMTP scope disabled by default. While a user can grant access to it, using SMTP may still fail. In this case, you might want to consider using the MS Graph API as the backend instead.

### Indexing Methods: Full and Fast Indexers

EmailEngine supports two indexing modes when dealing with IMAP accounts: the "full" indexer and the "fast" indexer.

#### Full Indexer

The full indexer builds a comprehensive index of the email account, which is stored in Redis. EmailEngine uses this index to detect new, deleted, and updated emails (such as when an email is marked as seen or unseen). While this method provides the most features, the downside is that the index can consume a significant amount of space in Redis. The indexing process can also be slow when processing email accounts with a large number of emails. In some cases, indexing might take so much time that the IMAP session expires and is closed, causing EmailEngine to fail in properly indexing the emails on that account.

#### Fast Indexer

The fast indexer stores only minimal required information about the state of the email account. There is almost no storage overhead in Redis, and the indexing process is, as the name suggests, fast. The downside is that EmailEngine can only detect new emails but not deleted or modified emails with the fast indexer.

#### Selecting an Indexer

To select an indexer, you can set the preferred indexer in the Service configuration page:

* Navigate to **Configuration > Service**
* Look for the **Indexing Method for IMAP Accounts** setting

You can also specify the preferred indexer by providing the `imapIndexer` property in the account data when adding a new email account to EmailEngine. If you have not set it, the default preferred indexer setting will be used.

**Note:** Once an indexer is set for an account, it cannot be changed directly. If you want to change the indexer, you need to use the [Account Flush API](https://api.emailengine.app/#operation/putV1AccountAccountFlush) to clear the existing index for that account. You can set the new indexer as part of the request so that once the existing index has been deleted, a new index is configured based on the provided input.

## Gmail API

When [using the Gmail API as the mail backend](https://docs.emailengine.app/setting-up-gmail-api-access/), EmailEngine does not open IMAP or SMTP sessions against Gmail and G Suite. Instead, it makes REST API requests to the Gmail API servers whenever it needs to perform an operation. EmailEngine normalizes all inputs and outputs, so when using EmailEngine's API, it should be transparent whether the backend is an IMAP server or the Gmail API.

To normalize some results, such as listing emails, EmailEngine might need to run multiple API requests instead of a single request, which can be slower.

**Considerations:**

* **Rate Limits:** Gmail API counts API requests for rate limiting. Different API requests have different weights, so specific operations (like listing long pages of emails) might use up the daily allocated quota faster.
* **Application-Specific Limits:** These limits are application-specific. If one connected app uses up its limits, it does not affect other apps connecting to that account.

**OAuth2 Scope:**

* The correct OAuth2 scope for using the Gmail API with EmailEngine is `https://www.googleapis.com/auth/gmail.modify`.

**Notifications:**

* `gmail.modify` is a "restricted" scope and requires a mandatory security review.
* To receive notifications about changes on the email account, EmailEngine needs to set up a Cloud Pub/Sub consumer. Therefore, a service account with the `https://www.googleapis.com/auth/pubsub` scope must also be set up for EmailEngine to use.

## Microsoft Graph API

When [using the Microsoft Graph API as the mail backend](https://docs.emailengine.app/setting-up-oauth2-with-outlook/), EmailEngine does not open IMAP or SMTP sessions against Microsoft 365 (MS365) and Outlook/Hotmail accounts. Instead, it makes REST API requests to the Microsoft Graph API servers whenever it needs to perform an operation.

**Considerations:**

* **Search Capabilities:** Compared to IMAP, using the Microsoft Graph API has somewhat more limited search capabilities. Some search fields that are available for other types of connections are unavailable (for example, the To and Cc fields).
* **Threading Support:** On the upside, the Microsoft Graph API supports threading, allowing you to search for threaded emails from all folders at once, instead of enumerating emails from each folder separately.

**OAuth2 Scopes:**

* The correct OAuth2 scopes for the Microsoft Graph API are `https://graph.microsoft.com/Mail.ReadWrite` and `https://graph.microsoft.com/Mail.Send`.
* **GCC High and DoD Clouds:** Experimentally, EmailEngine can support GCC High and DoD clouds, which have slightly different scopes.

## Other Protocols and Backends

Currently, EmailEngine cannot use other protocols or backends. It does not support POP3, ActiveSync, or proprietary APIs of different email hosting providers.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
