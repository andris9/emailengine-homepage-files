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

# Troubleshooting

When things go wrong

## Accounts do not connect

**Symptom**: when starting EmailEngine, most accounts stay disconnected and do not recover.

**Potential issue:** Redis is out of space.

EmailEngine uses Redis to store all its data. If Redis runs out of maximum allocated storage, EmailEngine can not write to Redis anymore. Creating connections will fail, as writing connection state information to Redis is part of the operation.

Check the `redis_memory_used_bytes` and `redis_memory_max_bytes` in the [monitoring output](https://emailengine.app/monitoring) to see how much space Redis uses.

## EmailEngine not accessible

**Symptom:** EmailEngine stops processing requests. The reverse proxy might report 502 errors.

**Potential issue:** EmailEngine has exhausted the maximum open files limit.

EmailEngine opens a lot of TCP connections which all use up file descriptors. Operating systems apply a maximum allowed open files limit to applications, and EmailEngine might run into that limit. As web requests also require opening sockets that are counted against the open file limit, these requests can not be served.

If the maximum open files limit is reached, you should see `EMFILE` errors in the application output log.

Check the `ulimit -n` value and increase it if it is too small. If running EmailEngine as a SystemD service, use the [LimitNOFILE directive](https://github.com/postalsys/emailengine/blob/e71110471e2437f297d0eb12721a30eb49d0c6dc/systemd/emailengine.service#L88).

## Syncing Takes Too Long or Never Completes

**Symptom:**\
EmailEngine is taking an unusually long time to sync a specific email account, or the syncing process never completes.

**Possible Cause:**\
The email account is too large for EmailEngine to handle efficiently.

**Explanation:**

* **Large Account Size:** EmailEngine indexes all emails in an account. For accounts with a vast number of emails, this process can be extremely time-consuming.
* **IMAP Session Timeout:** In some cases, the IMAP session may expire before the indexing process is complete. This prevents EmailEngine from finishing the sync, causing it to restart the process repeatedly.

**Solutions:**

1. **Use API-Based Backends:**

   * **[Gmail API](https://emailengine.app/gmail-api) or [Microsoft Graph API](https://emailengine.app/outlook-and-ms-365):**

     * Switching to an API-based backend can handle large accounts more efficiently.

     * These APIs are designed to work with large datasets and can reduce syncing time.

     * **How to Implement:**

       * For Gmail accounts, configure EmailEngine to use the Gmail API.
       * For Microsoft accounts, set up EmailEngine to use the Microsoft Graph API.

2. **Switch to the [Fast Indexer](https://emailengine.app/supported-account-types#fast-indexer) (for IMAP Accounts):**

   * **Fast Indexer:**

     * Opt for the "fast" indexer when using IMAP connections.

     * This indexer requires fewer resources and significantly reduces indexing time.

     * **Benefits:**

       * Minimal storage overhead in Redis.
       * Faster initial sync and reduced risk of IMAP session timeouts.

     * **Considerations:**
       * The fast indexer detects new emails but does not track deleted or modified emails.

> This page is updated as new issues occur and are resolved.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
