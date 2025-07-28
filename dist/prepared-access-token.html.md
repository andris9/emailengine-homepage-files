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

# Prepared Access Token

Set up the initial access token programmatically

EmailEngine only allows API requests to be made [using a valid access token](/authenticating-api-requests). While it is generally straightforward to generate such a token – all you need to do is log in to the admin interface of EmailEngine and click on the "Generate token" button – this approach still requires some manual work. In some situations, we want things to be completely automated. For example, when setting up a complex Docker-based architecture, we might want to perform some API requests before visiting the web interface; or doing end-to-end automated testing.

We can use the EmailEngine CLI to handle access tokens in these cases.

### Issuing tokens

The first option is to generate a new token via CLI. Make sure to use the same database configuration arguments as your main EmailEngine application has. Otherwise, EmailEngine would insert that token into the wrong database.

```
$ emailengine tokens issue -d "my token" -s "*" --dbs.redis="redis://127.0.0.1:6379/8"
f05d76644ea39c4a2ee33e7bffe55808b716a34b51d67b388c7d60498b0f89bc
```

Where:

* **-d** or **--description** is the title of the token.
* **-s** or **--scope** is the scope of the token. Currently one of `"*"` for all (also the default), `"api"` for API calls, and `"metrics"` for Prometheus metrics.
* **-a** or **--account** is the optional account ID if you want to bound this token against a single account. The request fails if you use the resulting token for anything unrelated to that user account.

EmailEngine returns the requested access token. We can then use it to perform API requests.

```
$ curl http://127.0.0.1/v1/stats -H 'Authorization: Bearer f05d76644ea39c4a2...'
```

> **NB!** when running token-specific CLI commands, you have to make sure that database settings are provided correctly, but you do not have to provide an encryption key in case field encryption is enabled. This is because token data is not encrypted but hashed, and thus the encryption key is never used with these values.

### Exporting tokens

If we do not want to generate a new token each time but re-use the same token, we can first create the token; it does not matter if we do it via CLI or the web interface, and then export that token using the CLI.

```
$ emailengine tokens export -t f05d76644ea39c4a2...
hKJpZNlAMzAxZThjNTFhZjgxM2Q3MzUxNTYzYTFlM2I1NjVkYmEzZWJjMzk4ZjI4OWZjNjgzN...
```

Where:

* **-t** or **--token** is the access token

EmailEngine returns encoded token data that we can either import to another EmailEngine instance via the CLI or use it as a predefined token set on application startup.

### Importing tokens

Importing can be done similarly to exporting; instead of providing the token value, we give the encoded token string.

> **NB!** From this point onwards, we will only be using the exported raw token data `hKJpZNlAMzAxZ...` and not the actual token `f05d76644ea39c4a2...`

```
$ emailengine tokens import -t hKJpZNlAMzAxZThjNTFhZjgxM2Q3MzUxN...
Token was imported
```

Where:

* **-t** or **--token** is the encoded token data you got from the export command

### Set a token on application startup

We can also load the token as a predefined value via the `EENGINE_PREPARED_TOKEN` environment variable:

```
$ export EENGINE_PREPARED_TOKEN=hhKJpZNlAMzAxZThjNTFhZjgxM2Q3MzUxN...
$ emailengine
...
{"level":20,"time":1638811265629,"pid":46918,"hostname":"example.com","msg":"Imported prepared token"}
```

Or use the command-line argument `--preparedToken`:

```
$ emailengine --preparedToken="hhKJpZNlAMzAxZThjNTFhZjgxM2Q3MzUxN..."
...
{"level":20,"time":1638829642382,"pid":49817,"hostname":"example.com","msg":"Imported prepared token"}
```

You can keep both the environment variable and the command-line argument fixed. If the token is already imported, it is skipped and not imported as a duplicate the next time the application starts.

### Usage Instructions for Generating and Using Access Tokens

If you need to prepare access tokens, please follow the steps below:

1. **Download the EmailEngine CLI App**

   Download the EmailEngine CLI app for your operating system from the following link: <https://emailengine.app/#downloads>.

2. **Generate a New Token**

   Run the following command to generate a new token. Ensure that the Redis configuration points to an actual Redis server. This server does not need to be an EmailEngine Redis server; it just needs to be a Redis server where the CLI app can write entries.

   ```bash
   emailengine tokens issue -d "my token" -s "*" --dbs.redis="redis://127.0.0.1:6379/0" > token.txt
   ```

   This command generates a new access token and stores the value in the file `token.txt`. You will need this token later to run API requests.

3. **Export the Token from Redis**

   Next, export the freshly generated token from Redis with the following command:

   ```bash
   emailengine tokens export -t $(cat token.txt) --dbs.redis="redis://127.0.0.1:6379/0" > exported_token.txt
   ```

   This command exports the token as an EmailEngine-formatted token hash into the file `exported_token.txt`. The actual token cannot be recovered from this hash, so it is not private.

4. **Add an Environment Variable to Your Docker Setup**

   In your Docker setup (or however you run EmailEngine), add a new environment variable using the following format:

   ```bash
   EENGINE_PREPARED_TOKEN="value from exported_token.txt"
   ```

5. **Run EmailEngine**

   When starting, EmailEngine will read the token hash from the environment variable and insert it into its database. Therefore, the next time you run EmailEngine, the environment variable will not be required. You should see the following line in the EmailEngine log, confirming that the token has been imported:

   ```json
   {"level":20,"time":1722533887921,"pid":76924,"hostname":"host","msg":"Imported prepared token"}
   ```

6. **Use the Token for API Requests**

   Whenever you run API requests against your EmailEngine instance, use the token value from `token.txt` as the API access token.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
