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

# Local addresses

If your server has multiple IP addresses/interfaces available, you can configure EmailEngine to use these whenever making an outbound connection to an email server.

Using multiple addresses is primarily helpful if you are making many connections and might get rate limited by destination server based on your IP address. This approach allows you to distribute connections between separate IP addresses.

## Set up local addresses

### Open the Network configuration page.

On this page, you can manage network-related configurations like proxying and local addresses.

### Choose address selection strategy.

EmailEngine can either use the default address, assign a static address for each account or select a random address every time a new connection to an email server is made.

### Set up address pool.

EmailEngine provides you with a list of IP addresses it can use. Check all addresses that are valid for your use case. If you happen to remove an address that is used as a static address for some accounts, then no need to worry – EmailEngine will automatically pick another one.

![](lib_pTNsKLAHHUZrxQKE/ujbyvmvifcjqdbv7.png?w=800\&h=500\&fit=crop)

## Proxies as an alternative

In addition to local addresses, you can route all your connections through proxy servers. The email server would see the proxy server's IP address as the client IP.

EmailEngine supports HTTP [CONNECT](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/CONNECT)-based proxies and SOCKS proxies (versions 4 and 5).

The format of the Proxy URL is the following:

```
proxytype://username:password@server:port
```

Example HTTP proxy with username and password (if the server is using a standard HTTP or HTTPS port, then you do not have to specify it):

```
http://andris:verysecret@proxy.example.com
```

Example SOCKS5 proxy url:

```
socks5://proxy.example.com:1080
```

### Option 1 – global proxy

On the same network configuration page, as shown above, you can also set up a global proxy. In that case, all connections, both for IMAP and SMTP, would be proxied through this specific proxy server. This is primarily useful when your network is private, and you have to make requests through a dedicated proxy server with a public interface.

### Option 2 – account specific proxies

Another option is to set up account-specific proxies. When [creating](https://api.emailengine.app/#operation/postV1Account) or [updating](https://api.emailengine.app/#operation/putV1AccountAccount) an account, use the `proxy` property that takes the proxy URL as its value.

The following example sets up the account to make all IMAP/SMTP connections for that account through a proxy server at `socks5://proxy.example.com:1080`

```
curl -XPOST "http://127.0.0.1:3000/v1/account" \
    -H "Authorization: Bearer f77cf263b70488..." \
    -H "Content-type: application/json" \
    -d '{
      "account": "example",
      "name": "Andris Reinman",
      "email": "andris@example.com",
      "proxy": "socks5://proxy.example.com:1080",
      "imap": {
        "auth": {
          "user": "andris",
          "pass": "sercretpass"
        },
        "host": "mail.example.com",
        "port": 993,
        "secure": true
      },
      "smtp": {
        "auth": {
          "user": "andris",
          "pass": "secretpass"
        },
        "host": "mail.example.com",
        "port": 465,
        "secure": true
      }
    }'
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
