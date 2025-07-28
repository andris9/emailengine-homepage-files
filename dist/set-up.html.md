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

# Set up EmailEngine

Installation instructions

All the following examples run EmailEngine with the default configuration. In most cases, you probably want to use specific settings—for example, change the Redis connection string. See the [configuration](/configuration) page for every available option.

* [Ubuntu / Debian](#ubuntu)
* [Linux](#linux)
* [MacOS](#macos)
* [Windows](#windows)
* [DigitalOcean](#digital-ocean)
* [Render](#render)
* [CapRover](#caprover)
* [Heroku](#heroku)
* [Other platforms](#other)

## Ubuntu / Debian

### Installing EmailEngine on Ubuntu

Use the automated install script to set up EmailEngine together with Redis, a Caddy reverse proxy, a Systemd service, and the upgrade helper `/opt/upgrade-emailengine.sh`.

**Important:** Run the script only on a fresh Ubuntu 20.04 LTS or Debian 11 server. It rewrites networking and service settings and can conflict with existing workloads. If the host already runs other applications, follow the [generic Linux procedure](#other) in the next section instead.

A VPS with **at least 2 GB of RAM** is recommended; smaller instances often stall during package compilation.

1. Fetch the installer:

```bash
wget https://go.emailengine.app -O install.sh
# or
curl -L https://go.emailengine.app -o install.sh
```

2. Make it executable, become root, and run it. Replace `example.com` with your domain or leave it empty to let the script choose one:

```bash
chmod +x install.sh
sudo su
./install.sh example.com
```

The script installs all components, obtains a TLS certificate, and starts EmailEngine. When it finishes, open `https://example.com` (or the generated hostname) to create the initial admin account.

## Linux

### Installing EmailEngine on Linux

The steps below show a minimal binary installation on Ubuntu. Adapt paths and package commands for your distribution.

#### 1. Redis

Add the RedisLabs PPA and install Redis:

```bash
sudo add-apt-repository -y ppa:redislabs/redis
sudo apt-get update
sudo apt-get -q -y install redis-server
sudo systemctl enable redis-server
sudo systemctl start redis-server
```

#### 2. Download EmailEngine

Get the compressed binary, extract it, and move it somewhere in `$PATH`:

```bash
wget https://github.com/postalsys/emailengine/releases/latest/download/emailengine.tar.gz
tar xzf emailengine.tar.gz
rm emailengine.tar.gz
sudo mv emailengine /usr/local/bin/
```

#### 3. Run

```bash
emailengine --dbs.redis="redis://127.0.0.1:6379/0"
```

Use flags or a `.env` file to override any defaults (see the [configuration](/configuration) guide).

#### Upgrading on Linux

Re‑download the latest `emailengine.tar.gz`, replace the existing binary, and restart the service/process.

## MacOS

### Installing EmailEngine on macOS

EmailEngine ships as a signed PKG installer for both Intel and Apple Silicon Macs.

#### 1. Redis

Install Redis with [Homebrew](https://brew.sh/):

```bash
brew update
brew install redis
brew services start redis
```

Alternatively, connect EmailEngine to a remote Redis server.

#### 2. Download EmailEngine

Download the latest installer:

* **Intel CPU:** <https://github.com/postalsys/emailengine/releases/latest/download/emailengine.pkg>
* **Apple Silicon:** <https://github.com/postalsys/emailengine/releases/latest/download/emailengine-arm.pkg>

The installer places the `emailengine` binary in `/usr/local/bin` and adds it to `$PATH`.

#### 3. Run

```bash
emailengine --dbs.redis="redis://127.0.0.1:6379/0"
```

Provide additional flags or a `.env` file as required.

#### Upgrading on macOS

Download the newest PKG for your CPU architecture and run it. The installer replaces the existing binary and preserves configuration.

#### Uninstalling on macOS

Remove the executable:

```bash
sudo rm /usr/local/bin/emailengine
```

## Windows

### Installing EmailEngine on Windows

EmailEngine ships as a standalone Windows executable.

#### 1. Redis

Redis is not officially maintained for Windows. Install a compatible fork such as [Memurai](https://www.memurai.com/) or point EmailEngine to a remote Redis server.

#### 2. Download EmailEngine

Grab the latest **emailengine.exe** from the [releases page](https://github.com/postalsys/emailengine/releases/latest/download/emailengine.exe) and place it in a convenient directory.

#### 3. Run

Open PowerShell and start EmailEngine:

```powershell
PS C:\EmailEngine> .\emailengine.exe --dbs.redis="redis://127.0.0.1:6379/0"
```

Configuration flags can also be provided through environment variables or a `.env` file in the same folder.

### Upgrading on Windows

Download the newest **emailengine.exe**, replace the existing file, and restart the process.

## DigitalOcean

### Installing EmailEngine on DigitalOcean

> **Important:** DigitalOcean blocks outbound SMTP ports **587** and **465** by default. If you need EmailEngine to send email, open a support ticket with DigitalOcean and request that these ports be unblocked for your droplet.

EmailEngine is published in the DigitalOcean Marketplace as a one‑click application. During creation you can either keep the built‑in Redis server or switch to DigitalOcean’s managed database service. If you keep the built‑in Redis, you are fully responsible for its availability and backups.

Use a droplet with **at least 2 GB of RAM**.

[![](https://cldup.com/QBubXuGF1M.svg)](https://marketplace.digitalocean.com/apps/emailengine?refcode=90a107552b31)

### Upgrading to the latest version

SSH into the droplet and run:

```bash
sudo /opt/upgrade-emailengine.sh
```

The script stops EmailEngine, downloads the latest release, applies migrations, and restarts the service.

## Render

### Installing EmailEngine on Render

Click **Deploy to Render** and follow the prompts. Leave default values unless you need custom environment variables.

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/postalsys/emailengine)

For a step‑by‑step walkthrough or customised setup, see the [Render installation guide](https://docs.emailengine.app/install-emailengine-on-render-com/).

## CapRover

### Installing EmailEngine on CapRover

CapRover provides a one‑click EmailEngine template. Open **One‑Click Apps** in your CapRover dashboard, search for *EmailEngine*, set the *App Name* (for example `emailengine`), and deploy.

After the container starts, enable HTTPS for the app and tick **Force HTTPS (redirect HTTP → HTTPS)**. Then open the application URL to access EmailEngine.

### Upgrading an existing CapRover installation

Open the CapRover admin panel, select the EmailEngine app, and go to the **Deployment** tab. In **Method 6 – Deploy via Image Name** enter:

```text
postalsys/emailengine:v2
```

Click **Deploy now**. CapRover pulls the latest image, recreates the container, and restarts EmailEngine without data loss.

## Heroku

### Installing EmailEngine on Heroku

Click **Deploy to Heroku**, enter the *App Name*, and leave all other fields unchanged.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/postalsys/emailengine)

Heroku regularly closes long‑running network connections. Because EmailEngine keeps persistent IMAP, SMTP, or API channels open, these disconnects can interrupt message processing. Deploy on Heroku only if the rest of your stack already runs there and allocate extra resources. If 2 GB RAM is ordinarily sufficient, provision a 4 GB dyno on Heroku to offset its stricter limits.

Heroku’s free and low‑tier Redis plans cap the number of concurrent connections. Every EmailEngine worker thread opens several Redis connections, so a small plan exhausts the limit quickly. Choose a Redis tier that allows at least **200 concurrent connections** or host Redis elsewhere.

## Other platforms

### Installing EmailEngine on Other Platforms

EmailEngine runs on any device that supports Node.js, including ARM‑based single‑board computers such as Raspberry Pi.

#### 1. Install Node.js

Download a current LTS build for your platform from <https://nodejs.org/en/download/> and follow the installer.

#### 2. Install Redis

Install and start a local Redis server. Official instructions for each platform are at <https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/>.

#### 3. Download and start EmailEngine

Create a working directory, fetch the latest release archive, and unpack it:

```bash
mkdir emailengine
cd emailengine
wget https://github.com/postalsys/emailengine/releases/latest/download/source-dist.tar.gz
tar xzf source-dist.tar.gz
rm source-dist.tar.gz
```

Create a minimal configuration file `.env`:

```bash
echo 'EENGINE_WORKERS=2
EENGINE_REDIS=redis://127.0.0.1:6379/8' > .env
```

Launch EmailEngine:

```bash
node server.js
```

The application reads configuration from `.env`. Browse to `http://localhost:3000` (or the port set in `EENGINE_PORT`) to open the UI.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
