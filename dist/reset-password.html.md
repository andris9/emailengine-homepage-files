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

# Reset password

Set admin password

If you've forgotten your admin password or need to set it during an automated setup process, you can do so using the command-line interface.

```
$ emailengine password -p secretvalue --dbs.redis="redis://127.0.0.1:6379/8"
secretvalue
```

Where

* `secretvalue` is the password to use. If a password is not provided, then a random password is generated.

> You can execute the `emailengine` [command](/#downloads) on any computer, such as the actual EmailEngine server or your own development machine. Just make sure to use the appropriate Redis URL, which should point to the Redis server associated with your EmailEngine instance. Keep in mind that you must have access to the Redis server from the machine where you're running the `emailengine` command.

The command returns the updated admin password. This is primarily useful if you did not set the password yourself and skipped the `-p` or `--password` argument.

***

If you need to reset your password from the command line, be aware that doing so will also disable any two-factor authentication (2FA) setup you may have in place. If you've lost access to your authenticator app and are unable to log in to your EmailEngine instance, resetting the password through the command line will be necessary to regain access.

### Exporting and importing

You can also use the password reset feature to export a password hash that you can use as a prepared password value. Add either `--hash` or `-r` flag to the password reset command to return not the password itself but the portable hash value.

```
$ emailengine password -p secretvalue --hash --dbs.redis="redis://127.0.0.1:6379/8"
JHBia2RmMi1zaGEyNTYkaT0yNTAwMCRuUlpVa1RxV2ptcHE5eWZrVWlaZmxnJFZ2KzVRN3VxcD...
```

You can then use this value either as `EENGINE_PREPARED_PASSWORD` environment variable value or `--preparedPassword` command line argument value.

It does not contain the actual password but its hash, so it is safe to include in configuration files or otherwhere. It assumes that the password you use is not easy enough to brute force.

```
$ emailengine --preparedPassword=JHBia2RmMi1zaGEyNTYkaT0yNTA...
...
{"level":20,"msg":"Imported hashed password","hash":"$pbkdf2-sha256$i=25000$nRZUkTqWjmp..."}
```

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
