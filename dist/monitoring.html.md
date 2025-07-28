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

# Monitoring

Geting visibility into EmailEngine

### Setting up Prometheus

EmailEngine allows monitoring via a Prometheus scraping endpoint. To enable it, you need first to generate an access token for Prometheus. To do so, click on the "Create new" button on the access tokens page in EmailEngine, uncheck "All scopes" in the form and check the "Metrics" scope before creating the token.

Next, set up a Prometheus scraping job. Use the newly generated access token as the value for the `credentials` field.

```yaml
  - job_name: 'emailengine'
    scrape_interval: 10s
    metrics_path: '/metrics'
    scheme: 'http'
    authorization:
      type: Bearer
      credentials: 795f623527c16d617b106...
    static_configs:
    - targets: ['127.0.0.1:3000']
```

Next, restart the Prometheus service and check the "Targets" page in the Prometheus UI to see if it was able to pick up the metrics from EmailEngine or not.

### Prometheus metrics

Here's a list of keys that EmailEngine includes in the Prometheus metrics output:

* **thread\_starts** (*Counter*) – Number of started threads
* **thread\_stops** (*Counter*) – Number of stopped threads
* **api\_call** (*Counter*) – Number of API calls \[labels: *"method"*, *"statusCode"*, *"route"*]
* **imap\_connections** (*Gauge*) – Current IMAP connection state \[labels: *"status"*]
* **imap\_responses** (*Counter*) – IMAP responses \[labels: *"response"*, *"code"*]
* **webhooks** (*Counter*) – Webhooks sent \[labels: *"status"*, *"event"*]
* **events** (*Counter*) – Events fired \[labels: *"event"*]
* **webhook\_req** (*Histogram*) – Duration of webhook requests
* **queue\_size** (*Gauge*) – Queue size \[labels: *"queue"*, *"state"*]
* **queues\_processed** (*Counter*) – Processed job count \[labels: *"queue"*, *"status"*]
* **redis\_uptime\_in\_seconds** (*Gauge*) – Redis uptime in seconds
* **redis\_rejected\_connections\_total** (*Gauge*) – Number of connections rejected by Redis
* **redis\_config\_maxclients** (*Gauge*) – Maximum client number for Redis
* **redis\_connected\_clients** (*Gauge*) – Number of client connections for Redis
* **redis\_slowlog\_length** (*Gauge*) – Number of of entries in the Redis slow log
* **redis\_commands\_duration\_seconds\_total** (*Gauge*) – How many seconds spend on processing Redis commands
* **redis\_commands\_processed\_total** (*Gauge*) – How many commands processed by Redis
* **redis\_keyspace\_hits\_total** (*Gauge*) – Number of successful lookup of keys in Redis
* **redis\_keyspace\_misses\_total** (*Gauge*) – Number of failed lookup of keys in Redis
* **redis\_evicted\_keys\_total** (*Gauge*) – Number of evicted keys due to maxmemory limit in Redis
* **redis\_memory\_used\_bytes** (*Gauge*) – Total number of bytes allocated by Redis using its allocator
* **redis\_memory\_max\_bytes** (*Gauge*) – The value of the Redis maxmemory configuration directive
* **redis\_mem\_fragmentation\_ratio** (*Gauge*) – Ratio between used\_memory\_rss and used\_memory in Redis
* **redis\_key\_count** (*Gauge*) – Redis key counts \[labels: *"db"*]
* **redis\_last\_save\_time** (*Gauge*) – Unix timestamp of the last RDB save time
* **redis\_instantaneous\_ops\_per\_sec** (*Gauge*) – Throughput operations per second
* **redis\_command\_runs** (*Gauge*) – Redis command counts \[labels: *"command"*]
* **redis\_command\_runs\_fail** (*Gauge*) – Redis command counts \[labels: *"command"*, *"status"*]

### Grafana dashboard

You can download the example Grafana dashboard definition for EmailEngine from [here](https://gist.github.com/andris9/746661bc56c6c0d951ca4ba510a2a1fc). See the contents of the dashboard from the screenshot below.

![emailengine-grafana.png](lib_pTNsKLAHHUZrxQKE/7rku450cey72fl0k.png)

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
