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

# SystemD

Running EmailEngine as a SystemD service.

In most cases, you probably do not want to run EmailEngine as a regular application but as a background service. Under Linux, the easiest way to create such services would be with SystemD. EmailEngine does not fork itself and sends all its logs to standard output, so it's not a good match with the SysV Init kind of system, but it runs well with SystemD.

### Prepare the App

First, we need the application to be present. In this example, we download the executable and place it under the `/usr/local/bin` directory. Why that one, you say? Well, it's just as good as any other place and it's usually in $PATH; you could store it somewhere else.

```
$ wget https://github.com/postalsys/emailengine/releases/latest/download/emailengine.tar.gz
$ tar xzf emailengine.tar.gz
$ rm -rf emailengine.tar.gz
$ sudo mv emailengine /usr/local/bin
```

If `wget` is not available, use `curl` to download the application.

```
$ curl https://github.com/postalsys/emailengine/releases/latest/download/emailengine.tar.gz -L -o emailengine.tar.gz
```

### Create a service unit

All SystemD services require to have a valid service file. We can create one via the command line.

```
$ sudo nano /etc/systemd/system/emailengine.service
```

Once the editor is open, paste the following service file content into it and save it:

```
[Unit]
Description=EmailEngine
# Depending on how you installed Redis, it's service name might be different
After=redis-server

[Service]
# Configure environment variables
Environment="EENGINE_REDIS=redis://@127.0.0.1:6379/8"
Environment="EENGINE_PORT=3000"

# Folder where EmailEngine executable is located
WorkingDirectory=/tmp

# EmailEngine does not require any special privileges
User=www-data
Group=www-data

# Run the EmailEngine executable
ExecStart=/usr/local/bin/emailengine

Type=simple
Restart=always

SyslogIdentifier=emailengine

[Install]
WantedBy=multi-user.target
```

Now we can enable and start the service. Next time when we reboot our server, the service is started automatically.

```
$ sudo systemctl enable emailengine
$ sudo systemctl start emailengine
```

Verify that the service is running either by checking the service status or connecting to EmailEngine's port.

```
$ sudo systemctl status emailengine
● emailengine.service - EmailEngine
     Loaded: loaded (/etc/systemd/system/emailengine.service; disabled; vendor >
     Active: active (running) since Sat 2021-10-02 11:04:29 UTC; 1h 34min ago
   Main PID: 368410 (emailengine)
      Tasks: 17 (limit: 9284)
     Memory: 416.3M
     CGroup: /system.slice/emailengine.service
             └─368410 /usr/local/bin/emailengine
...

$ curl 127.0.0.1:3000/metrics
# HELP thread_starts Number of started threads
# TYPE thread_starts counter
thread_starts 7
...
```

### Checking output logs

When EmailEngine is installed as a SystemD service, you can use standard SystemD tools for different tasks and logging.

For example, to tail output from EmailEngine, you can use the `journalctl` command:

```
$ journalctl -t emailengine -f

-- Logs begin at Tue 2021-06-01 12:53:14 UTC. --
Oct 02 12:43:43 srv-01 emailengine[369048]: {"level":30,"time":1633178623716,"pid":369048,"hostname":"srv-01","msg":"Starting EmailEngine","version":"1.14.5","node":"16.8.0"}
Oct 02 12:43:43 srv-01 emailengine[369048]: {"level":20,"time":1633178623716,"pid":369048,"hostname":"srv-01","msg":"IMAP Worker Count","workersImap":4}
Oct 02 12:43:43 srv-01 emailengine[369048]: {"level":20,"time":1633178623716,"pid":369048,"hostname":"srv-01","msg":"Webhooks Worker Count","workersWebhooks":1}
Oct 02 12:43:43 srv-01 emailengine[369048]: {"level":20,"time":1633178623717,"pid":369048,"hostname":"srv-01","msg":"Submission Worker Count","workersWebhooks":1}
```

This output is not very well readable, but you can't directly use the log renderers shown [here](/logging) because of the metadata entries in front of EmailEngine output logs. These lines are not valid JSON, so the renderers would not be able to process these.

Luckily we can modify `journalctl` output, so we can ask it to drop all the metadata and only show log entries from EmailEngine. This way, we can pipe that output to a log renderer of our choice.

```
$ journalctl -t emailengine -f -o cat | jq
{
  "level": 20,
  "time": 1633178713318,
  "pid": 369143,
  "hostname": "srv-01",
  "msg": "Webhooks Worker Count",
  "workersWebhooks": 1
}
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
