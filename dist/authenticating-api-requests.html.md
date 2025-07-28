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

# Authenticating API Requests

Using Access Tokens with EmailEngine

Each request against the EmailEngine API must be authenticated with an access token. This requirement cannot be disabled in production, as certain parts of EmailEngine, such as the [hosted authentication](/hosted-authentication) feature, assume that EmailEngine may be publicly accessible.

Generating an Access Token

###

To generate an access token, navigate to the "Access Tokens" page in EmailEngine's administration dashboard.

###

Each token can have a specific scope assigned. For example, if you are using Prometheus to monitor EmailEngine, choose the "Metrics" scope. This limits access to only the necessary data, avoiding full API access.

###

Access tokens are displayed only once. If you close the dialog without saving the token, you will not be able to retrieve it again, as EmailEngine stores only hashed tokens, not the actual token value.

![](lib_pTNsKLAHHUZrxQKE/cd34bas2fe09u8rw.gif)

> For programmatic access to tokens, refer to [Prepared Access Token](/prepared-access-token).

## Making API Requests

EmailEngine requires Bearer authorization for API requests. You must include the access token in the `Authorization` header as follows:

```
Authorization: Bearer <token>
```

Where:

* `<token>` is the access token generated on the "Access Tokens" page.

#### Example

If your token value is `"abcdef"`, you can make a request using `curl`:

```
$ curl http://127.0.0.1:3000/v1/stats -H "Authorization: Bearer abcdef"
```

The response might look like this:

```json
{
  "version": "2.8.0",
  "license": "AGPL-3.0-or-later OR LICENSE_EMAILENGINE",
  "accounts": 2,
  "node": "16.13.1",
  ...
}
```

### Passing Token via Query Parameter

If you cannot set HTTP headers, you can pass the access token as a query parameter:

```
$ curl http://127.0.0.1:3000/v1/stats?access_token=abcdef
```

## Using Prometheus with EmailEngine

Prometheus can scrape EmailEngine metrics from the `/metrics` endpoint (e.g., `http://127.0.0.1:3000/metrics`). These requests also require authorization, and you should generate an access token with the "Metrics" scope specifically for Prometheus.

### Configuring Prometheus

To enable Prometheus to authenticate with EmailEngine, include the following configuration in the `scrape_configs` section:

```yaml
authorization:
  type: Bearer
  credentials: <token>
```

### Full Example

Below is a complete example of a Prometheus scraping job for EmailEngine:

```yaml
  - job_name: 'emailengine'
    scrape_interval: 10s
    metrics_path: '/metrics'
    scheme: 'http'
    authorization:
      type: Bearer
      credentials: e209fc049632c....46dabbb7cd28
    static_configs:
    - targets: ['127.0.0.1:3000']
```

### Testing with Curl

To check if the `/metrics` endpoint is functioning, you can use a `curl` command similar to regular API requests:

```
$ curl http://127.0.0.1:3000/metrics -H "Authorization: Bearer abcdef"
```

The response will contain metrics in the following format:

```
# HELP thread_starts Number of started threads
# TYPE thread_starts counter
thread_starts 7

# HELP thread_stops Number of stopped threads
# TYPE thread_stops counter
thread_stops 0
...
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
