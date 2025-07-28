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

# Docker

Run EmailEngine as a Docker container

## Docker Hub

**Pull EmailEngine from Docker Hub**

To get started, pull the EmailEngine image from [Docker Hub](https://hub.docker.com/r/postalsys/emailengine):

```
$ docker pull postalsys/emailengine:v2
```

> EmailEngine is also available from the [GitHub Container Registry](https://github.com/postalsys/emailengine/pkgs/container/emailengine).

**Configure EmailEngine**

You can configure EmailEngine by setting environment variables. In the example below, we set up the Redis connection URL and bind port 3000:

```
$ docker run -p 3000:3000 --env EENGINE_REDIS="redis://host.docker.internal:6379/7" postalsys/emailengine:v2
```

For more configuration options, visit the [configuration documentation](/configuration).

**Access EmailEngine in Your Browser**

After running the command above, open the following URL in your browser: <http://127.0.0.1:3000>.

> The EmailEngine Docker images support multiple architectures, so you can easily run them on Apple processors as well.

### EmailEngine Tag Types

EmailEngine offers various tag types for its Docker images:

1. `latest`: Corresponds to the most recent commit in the master branch. While it includes the latest features, it might be unstable.
2. `v2`: Represents the latest tagged release for the v2 branch.
3. `v2.x.x`: Refers to a specific tagged release.

## Docker Compose

Download the example [Compose file](https://raw.githubusercontent.com/postalsys/emailengine/master/docker-compose.yml) and add or edit included configuration values. Most probably, you want to remove the example webhook destination settings.

Next, start the app. The Compose file already includes a basic Redis setup, so you do not have to install or configure Redis separately.

```
$ docker-compose up
```

Open <http://127.0.0.1:3000> in your browser to see the UI.

## Unsupported platform?

1. Download the source code of the [latest EmailEngine release](https://github.com/postalsys/emailengine/releases/latest).
2. Edit Dockerfile in the root folder of the project; for example, change the base image `node:lts-alpine` to `arm32v7/node:lts-alpine` if you want to run EmailEngine on a Raspberry Pi (find the complete list of available base images [here](https://hub.docker.com/_/node)).
3. Run a Docker build

```
$ docker build -t emailengine .
```

4. Run the container
   ```
   $ docker run -p 3000:3000 --env EENGINE_REDIS="redis://host.docker.internal:6379/7" emailengine
   ```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
