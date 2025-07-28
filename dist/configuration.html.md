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

# Configuration

Configuring and Running EmailEngine

EmailEngine uses two types of configurations:

1. **Application Configuration**: This is loaded when the application starts and includes settings such as the HTTP port number.
2. **Runtime Configuration**: This can be updated at any time via the [Settings API endpoint](https://api.emailengine.app/#operation/postV1Settings) or through the built-in web interface. Examples include the webhook destination URL.

You can configure the application using either command-line arguments or environment variables. If both are provided for the same setting, the environment variable will take precedence over the command-line argument.

## General settings

These are the main application settings.

| Configuration Option                                                          | CLI Argument                   | ENV Value                                                | Default   |
| ----------------------------------------------------------------------------- | ------------------------------ | -------------------------------------------------------- | --------- |
| IMAP Worker Count                                                             | `--workers.imap=4`             | `EENGINE_WORKERS=4`                                      | `4`       |
| Max Command Duration                                                          | `--service.commandTimeout=10s` | `EENGINE_TIMEOUT=10s`                                    | `10s`     |
| Delay Between Creating Each IMAP Connection                                   | `--service.setupDelay=0ms`     | `EENGINE_CONNECTION_SETUP_DELAY=0ms`                     | `0ms`     |
| [Log Level](https://emailengine.app/logging#loglevels)                        | `--log.level="level"`          | `EENGINE_LOG_LEVEL=level`                                | `"trace"` |
| [Log Raw Data](/logging#logs-eerawlog)                                        | `--log.raw=false`              | `EENGINE_LOG_RAW=false`                                  | `false`   |
| Webhook Worker Count                                                          | `--workers.webhooks=1`         | `EENGINE_WORKERS_WEBHOOKS=1`                             | `1`       |
| Submit Worker Count                                                           | `--workers.submit=1`           | `EENGINE_WORKERS_SUBMIT=1`                               | `1`       |
| [Prepared Settings](/prepared-settings)                                       | `--settings='{"JSON"}'`        | `EENGINE_SETTINGS='{"JSON"}'`                            | not set   |
| [Prepared Access Token](/prepared-access-token)                               | `--preparedToken="token..."`   | `EENGINE_PREPARED_TOKEN="token..."`                      | not set   |
| [Prepared License Key](/prepared-license)                                     | `--preparedLicense="..."`      | `EENGINE_PREPARED_LICENSE="..."`                         | not set   |
| [Encryption Secret](https://docs.emailengine.app/enabling-secret-encryption/) | `--service.secret="****"`      | `EENGINE_SECRET="****"`                                  | not set   |
| Chunk Size for Attachment Download Streams (in bytes)                         | not available                  | `EENGINE_CHUNK_SIZE=1MB`                                 | `1MB`     |
| Time to Wait for IMAP Access Lock After Failed Login Attempts                 | not available                  | `EENGINE_MAX_IMAP_AUTH_FAILURE_TIME=3days`               | `3 days`  |
| Restrict IP Addresses for Admin UI Access                                     | not available                  | `EENGINE_ADMIN_ACCESS_ADDRESSES=127.0.0.1,192.168.1.152` | not set   |

## IMAP Worker Count

You can set a fixed number of worker threads (the default is 4), or use the special value `"cpus"`, which automatically matches the number of worker threads to the available CPU cores.

**Example:**

```
$ emailengine --workers.imap=cpus
```

## Redis Configuration

Database options.

| Configuration Option | CLI Argument        | ENV Value                         | Default                      |
| -------------------- | ------------------- | --------------------------------- | ---------------------------- |
| Redis Connection URL | `--dbs.redis="url"` | `EENGINE_REDIS="url"`             | `"redis://127.0.0.1:6379/8"` |
| Key Prefix           | not available       | `EENGINE_REDIS_PREFIX="myprefix"` | not set                      |

If you run multiple EmailEngine instances on the same Redis server, ensure that you use a different database number or unique key prefixes to prevent conflicts.

For non-default Redis configurations, such as when your Redis instance is hosted remotely or requires authentication, provide the Redis connection details as a connection string in the following format:

```
redis://:password@hostname:port/db-number
```

**Example:**

```
$ emailengine --dbs.redis="redis://:supersecret@178.32.207.71:6379/8"
```

## API server settings

Web server settings for the API and web UI.

| Configuration Option                       | CLI Argument                  | ENV Value                         | Default       |
| ------------------------------------------ | ----------------------------- | --------------------------------- | ------------- |
| Host to Bind To                            | `--api.host="1.2.3.4"`        | `EENGINE_HOST="1.2.3.4"`          | `"127.0.0.1"` |
| Port to Bind To                            | `--api.port=port`             | `EENGINE_PORT=port`               | `3000`        |
| Max Attachment Size                        | `--api.maxSize=5MB`           | `EENGINE_MAX_SIZE=5MB`            | `5MB`         |
| Max POST Body Size When Uploading Messages | `--api.maxBodySize=50MB`      | `EENGINE_MAX_BODY_SIZE=50MB`      | `50MB`        |
| Max Allowed Time to Upload a Message       | `--api.maxPayloadTimeout=10s` | `EENGINE_MAX_PAYLOAD_TIMEOUT=10s` | `10s`         |
| CORS Allowed Origin                        | `--cors.origin="*"`           | `EENGINE_CORS_ORIGIN="*"`         | not set       |
| CORS Max Age                               | `--cors.maxAge=60s`           | `EENGINE_CORS_MAX_AGE=60s`        | `60s`         |

> To specify multiple CORS origins, use a space-separated string value, or add a separate `--cors.origin` argument for each origin.

| Configuration Option    | CLI Argument                     | ENV Value                               | Default   |
| ----------------------- | -------------------------------- | --------------------------------------- | --------- |
| HTTPS Enabled           |                                  | `EENGINE_API_TLS="true"`                | `"false"` |
| HTTPS Private Key       | `--api.tls.keyPath="/key.pem"`   | `EENGINE_API_TLS_KEY_FILE="/key.pem"`   |           |
| HTTPS Certificate Chain | `--api.tls.certPath="/cert.pem"` | `EENGINE_API_TLS_CERT_FILE="/cert.pem"` |           |

> **Note:** Use HTTPS only if your reverse proxy and EmailEngine instances are on different servers. For localhost connections, prefer HTTP.

## Queue Settings

Concurrency options for queue handling.

### Concurrency

Each queue worker processes one job at a time (concurrency = 1).\
Increase concurrency only when event order is irrelevant, because setting concurrency greater than 1 can reorder events.

| Queue      | CLI flag            | ENV variable          | Default |
| ---------- | ------------------- | --------------------- | ------- |
| Webhooks   | `--queues.notify=1` | `EENGINE_NOTIFY_QC=1` | `1`     |
| Submission | `--queues.submit=1` | `EENGINE_SUBMIT_QC=1` | `1`     |

### Submission delay

| Setting                   | CLI flag           | ENV variable              | Default |
| ------------------------- | ------------------ | ------------------------- | ------- |
| Delay between submissions | `--submitDelay=5s` | `EENGINE_SUBMIT_DELAY=5s` | *unset* |

When configured, EmailEngine waits the given duration after delivering one message before attempting the next message in the submission queue.

**Global scope**: the delay applies to the entire submission queue, not per account.\
Example: 10 accounts × 10 messages × 6 s delay = 10 min to send all 100 messages.

## Dotenv File

If the working directory contains a "dotenv" file (named `.env`), EmailEngine will use it to set environment variables.

The dotenv file follows the `KEY=VALUE` format, with each entry on a separate line, like this:

```
EENGINE_WORKERS=2
EENGINE_REDIS="redis://127.0.0.1:6379/8"
```

Lines starting with `#` are treated as comments and will be ignored, as will empty lines.

## ENV Values from Files

You can also set individual environment variables by specifying a file that contains the value. To do this, use an environment key with a `_FILE` suffix and provide the path to the file.

If an environment variable is not set directly, EmailEngine will check if a corresponding file path variable exists. If it finds one, EmailEngine will attempt to read the value from the specified file.

In the example below, the value for `EENGINE_SECRET` is provided from a file located at `"/path/to/secret.txt"`:

```
$ echo 'secretpass' > /path/to/secret.txt
$ export EENGINE_SECRET_FILE=/path/to/secret.txt
$ emailengine
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
