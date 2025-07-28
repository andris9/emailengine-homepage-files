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

# Prepared license key

Set up the license key programmatically

While you can set the license key using the administration interface, there are cases you might want to do this programmatically without manual intervention.

We can use the EmailEngine CLI to handle the license key in these cases.

## Exporting the license key

Using the EmailEngine CLI, you can export the license as a single-line encoded string.

> `--dbs.redis` argument is used as an example. Without proper database settings, you would be exporting the license data from a wrong place.

```
$ emailengine license export --dbs.redis="redis://localhost:6379"
i0-AgqFsxFWFoWvEDGC7UN0krkiX_UkBn6FuslBvc...
```

EmailEngine returns encoded token data that we can either import to another EmailEngine instance via the CLI or use as a predefined license key set on application startup.

## Importing the license key

You can import the exported license key similarly as You exported it.

> In this example, I'll be omitting database settings, but you still would have to provide it.

```
$ emailengine license import -l i0-AgqFsxFWFoWvEDGC7UN0krkiX_UkBn6FuslBvc...
License key was imported
```

Where:

* **-l** or **--license** is the encoded license key data you got from the export command **or** the license key you got from Postal Systems, but this value would be longer and multi-line

## Set the license key on application startup

We can also load the license key as a predefined value via the `EENGINE_PREPARED_LICENSE` environment variable:

```
$ export EENGINE_PREPARED_LICENSE=i0-AgqFsxFWFoWvEDGC7...
$ emailengine
...
{"level":30,"time":1639517442546,"pid":30649,"hostname":"andrismacbook.lan","msg":"Imported license key","source":"import"}
```

Or use the command-line argument `--preparedLicense`:

```
$ emailengine --preparedLicense="i0-AgqFsxFWFoWvEDGC7...."
...
{"level":30,"time":1639517517937,"pid":30673,"hostname":"andrismacbook.lan","msg":"Imported license key","source":"import"}
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
