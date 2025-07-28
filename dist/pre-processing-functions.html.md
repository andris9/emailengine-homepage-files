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

# Pre-processing Functions

Filter and modify webhooks and other processes

EmailEngine allows pre-processing of several actions, such as webhooks posted using the Webhook Routing feature and emails stored in ElasticSearch. The pre-processing function is a small JavaScript code snippet that you can provide to EmailEngine, which runs whenever a triggering event occurs.

In general, there are two types of pre-processing functions:

#### 1. Filter Functions

The output from this function determines if the event is processed or discarded. For the filter function to pass, it must return the boolean `true` value. Any other value or exception will discard the event. For webhooks, a failing filter means that the webhook is not posted, and for Document Store events, it means that the email is not stored in the Document Store.

#### 2. Mapping Functions

This function allows you to modify the structure of the processed data. You can add or remove properties or completely replace the value.

Here's how the Document Store pre-processing function configuration page looks:

![](https://cldup.com/mEQk0jEyGc.png)

### Specifications

**Editor Actions**

* **Toggle fullscreen** maximizes the editor window. To return to normal view, press the ESC key.
* **Set test payload** allows you to set the input payload manually or select an example payload.
* **Send webhook preview** sends the payload from the preview screen to the specified webhook URL.

**Variables**

* **payload** is the event payload. For webhooks, it is the webhook structure where email information is nested under the `data` property. For Document Store events, it is the email object itself.
* **env** is the [environment variable](#environment-variables) – defaults to an empty object if not defined.
* **logger** is a [Pino](https://getpino.io/#/docs/api) logger object. It does not provide the full feature set but only the basic logging options like `logger.error(...)` etc. The log entries are logged to EmailEngine's output log.

**Supported APIs**

EmailEngine disables most APIs for security reasons when running the pre-processing functions, so in general, only the native JavaScript functions are available. There are some specific Web APIs that are enabled:

* **[Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)** to run external web requests. Be aware, though, that this might slow down queue processing, so it is not advised to make external requests if you have a system that processes a large number of emails.
* **[URL](https://developer.mozilla.org/en-US/docs/Web/API/URL)** to parse and process URLs.

> Pre-processing functions support top-level `await` and `async` syntax. This is mostly needed for Fetch calls.

### Environment Variables

EmailEngine allows defining an environment for the pre-processing scripts. This is a JSON object string you can set either from the Service settings page or via the Settings API using the `scriptEnv` key.

Once the environment object value is set, it becomes available for all pre-processing scripts as the `env` object variable.

Defining the environment from the admin page:

![](https://cldup.com/O_6jskSB-E.png)

Defining the same environment using a [Settings API](https://api.emailengine.app/#operation/postV1Settings) call:

![](https://cldup.com/1v71uvQGYI.png)

Once you have the environment object set up, you can use it in the filter and map functions as the `env` variable.

The following filter function only passes if the account ID of the email is listed in the provided array:

![](https://cldup.com/NIElxzbPUF.png)

This mapping function sets a property to the email from the environment object:

![](https://cldup.com/W-0wCGyWCc.png)

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
