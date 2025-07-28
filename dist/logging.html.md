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

# Log management

What's going on under the hood

## Pino

EmailEngine logs all its messages to standard output in [Pino](https://github.com/pinojs/pino) format. Pino uses JSON structures with some predefined keys, so it is kind of human-readable but not really. Luckily, there are many tools that we can use to transform EmailEngine logs into a more suitable format. The working principle for all these log renderers is the same – you should pipe the standard output from EmailEngine to the log rendering process.

![](lib_pTNsKLAHHUZrxQKE/dnnoot0tgmeaaaol.png)

## Log levels

As EmailEngine uses pino logging library, standard pino log levels apply.

* **trace** (10)
* **debug** (20)
* **info** (30)
* **warn** (40)
* **error** (50)
* **fatal** (60)

You can limit output logs by setting a minimum log level. The default is `trace`, which means that everything is logged.

To change the logging level, either set an environment variable.

```
EENGINE_LOG_LEVEL=trace
```

Or use a command-line argument.

```
$ emailengine --log.level="trace"
```

## jq

`jq` is a well-known command for processing JSON structures in the command line. It is not EmailEngine specific at all, but we can easily use it to make EmailEngine logs slightly more readable.

```bash
$ emailengine | jq
```

![](lib_pTNsKLAHHUZrxQKE/g8zk6fui4jfzdtxq.png)

## pino-pretty

`pino-pretty` is a command-line utility that makes Pino formatted logs more readable. You can install it from the npm registry as a global command.

```
$ npm install -g pino-pretty
$ emailengine | pino-pretty
```

![](lib_pTNsKLAHHUZrxQKE/k1r29qzdnbrubbrd.png)

## eerawlog

`eerawlog` is an EmailEngine-specific log renderer. It is meant to help debug IMAP and API call transactions. This is the only thing it's suitable for, so it's not a general log renderer – it tends to throw away everything that is not IMAP-specific. Easier to understand how EmailEngine communicates with IMAP servers. Hard to see how the application is doing in general.

> EmailEngine must be started with the `--log.raw=true` command-line argument or with the `EENGINE_LOG_RAW=true` environment variable. Otherwise, detailed logs required by the `eerawlog` command will be disabled.

Detailed logs are disabled by default for the following reasons:

1. raw logs can be pretty large as it includes all bytes sent and received, that includes complete email message sources
2. raw logs do not modify secrets. Regular logs are always processed in a way where account credentials and other secrets are filtered out. Raw logs include all bytes transferred between EmailEngine and the server

```
$ npm install -g eerawlog
$ emailengine --log.raw=true | eerawlog
```

![](lib_pTNsKLAHHUZrxQKE/9vlxp9d8u78gzgju.png)

### Filtering output

You can filter by the keys listed in log entries by adding a cli argument `--filter.[key]="value"`. If you want to include multiple values, set the same keyword multiple times.

**Example.** Only display IMAP traffic from accounts `"account1"` and `"account2"`

```
$ EENGINE_LOG_RAW=true emailengine | eerawlog --filter.account="account1" --filter.account="account2"
```

## pino-gelf

`pino-gelf` is another useful utility that sends all log messages from EmailEngine to a [Graylog](https://www.graylog.org/) server. A neat feature is that `pino-gelf` can also work in a throughput mode so that it both sends the logs to Graylog and logs messages to console so we can mix it up with other tools like `pino-pretty`

```
$ npm install -g pino-gelf
$ emailengine | pino-gelf log -h graylog.server.com -t | pino-pretty
```

Something to keep in mind with `pino-gelf` is that it creates separate log keys *only* for root-level log entry keys. Object values are serialized into JSON strings:

```json
{  
  "version":"1.1",
  "host":"emailengine",
  "short_message":"message entry",
  "full_message":"message entry",
  "timestamp":1481840140.708,
  "level":6,
  "_name":"emailengine",
  "_object_value":"{\"key\":\"value\"}",
}
```

In this case you can't search for `object_value.key` field in Graylog. Instead there will be a text field `object_value` with `{"key":"value"}` as it's value.

## Journald

If you set EmailEngine up as a [SystemsD service](/system-d-service), the application logs end up in journald. You can access these logs using the `journalctl` command.

> If you install EmailEngine from [DigitalOcean Marketplace](https://marketplace.digitalocean.com/apps/emailengine) or by using the [install script](https://emailengine.app/set-up#ubuntu), then EmailEngine uses *systemd* and *journald* by default.

**Tail logs in realtime**

```
$ journalctl -t emailengine -f -o cat | pino-pretty
```

**List logs for the last hour**

Journald also allows to retrieve logs with time based queries. The following command lists logs for the last hour.

```
$ journalctl -t emailengine --since "1 hour ago" -o cat | pino-pretty
```

**Tail prettified logs for a specific account**

If you have the [eerawlog](#logs-eerawlog) tool installed and set up, you can filter and pretty print IMAP and API traffic for a specific account on a running system.

```
$ journalctl -t emailengine -f -o cat | eerawlog --filter.account=account_id
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
