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

# Expose public HTTPS

Make EmailEngine available over public internet

In this example, we use Nginx as a reverse proxy in front of EmailEngine to make it publicly accessible.

### 1. Make sure Nginx is installed

If you do not have Nginx installed yet, install it with your platform's package manager. For example, in Debian or Ubuntu, use `apt`.

```
$ apt-get update
$ apt-get install nginx -y
```

### 2. Prepare dummy HTTPS certificates

At first, we are going to create some dummy HTTPS certificates. This step is optional, and we only do it so that we would be able to set up Nginx HTTPS virtual host before we provision actual HTTPS certificates.

```
$ sudo openssl req -subj "/CN=example.com/O=My Company Name LTD./C=US" -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout privkey.pem -out fullchain.pem
$ sudo chmod 0600 privkey.pem
$ sudo mv privkey.pem /etc/ssl/private/emailengine-privkey.pem
$ sudo mv fullchain.pem /etc/ssl/certs/emailengine-fullchain.pem
```

### 3. Nginx virtual host config

Create a new virtual host configuration file

```
$ sudo nano /etc/nginx/sites-available/emailengine.conf
```

And paste the following configuration. Make sure to change the domain name and verify that EmailEngine's HTTP port would be the correct one (defaults to 3000):

```
server {
    listen 80;
    listen 443 ssl http2;

    server_name example.com; # <- change this domain name

    ssl_certificate_key /etc/ssl/private/emailengine-privkey.pem;
    ssl_certificate /etc/ssl/certs/emailengine-fullchain.pem;

    location / {
        client_max_body_size 50M;
        proxy_http_version 1.1;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Scheme $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass http://127.0.0.1:3000; # <- use EmailEngine's HTTP port
    }

    # Enforce HTTPS
    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    }
}
```

Next, we would have to enable the configuration.

```
sudo ln -s /etc/nginx/sites-available/emailengine.conf /etc/nginx/sites-enabled/emailengine.conf
```

Also, verify that the Nginx configuration does not include any errors. Otherwise, reloading or restarting it would stop the Nginx service.

```
$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

And finally, apply the new config.

```
$ sudo systemctl reload nginx
```

At this point, if you'd open the domain name in your browser, it should show you EmailEngine's page. **Except** you are still using the dummy HTTPS certificates, so in most cases, you'd see the invalid certificate warning instead.

### 4. Provision HTTPS certificates

In this example, we'll use [acme.sh](https://acme.sh/) to provision HTTPS certificates from Let's Encrypt.

If you do not have it installed, you can do it easily like the following (in this example, we would be performing all acme.sh related operations as *root*)

```
$ sudo su
$ cd
$ curl https://get.acme.sh | sh -s email=my@example.com
```

> Make sure to use your actual email address

Once we have acme.sh installed and set up, we can provide the certificates (must run as *root*):

```
$ /root/.acme.sh/acme.sh --issue --nginx --server letsencrypt \
    -d example.com \
    --key-file       /etc/ssl/private/emailengine-privkey.pem  \
    --ca-file        /etc/ssl/certs/emailengine-chain.pem \
    --fullchain-file /etc/ssl/certs/emailengine-fullchain.pem \
    --reloadcmd     "/bin/systemctl reload nginx"
```

> Replace *example.com* with the domain name of your virtual host.

Acme.sh will be renewing these certificates automatically, so in the best-case scenario, you will never have to deal with these certificates again.

Unless something went utterly wrong, you should now have EmailEngine publicly accessible. Additionally, you can find a more thorough Nginx virtual host configuration example for EmailEngine [here](https://raw.githubusercontent.com/postalsys/emailengine/master/systemd/nginx-proxy.conf).

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
