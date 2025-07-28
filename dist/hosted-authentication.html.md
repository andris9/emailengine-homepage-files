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

# Hosted Authentication

Let EmailEngine handle the email account setup process for your users.

While you can always add new email accounts directly via [EmailEngine's API](https://api.emailengine.app/#operation/postV1Account), you also have the option to let EmailEngine take care of the entire setup process. This approach simplifies user interactions and reduces complexity in your application.

With hosted authentication, you direct your users to EmailEngine’s web-based form, where they provide their email server credentials. After successful configuration, EmailEngine stores the account settings and redirects the user back to your application, ready for use.

> **Note:** If you prefer not to use the hosted authentication form but need to manage OAuth2 flows, consider the alternative [authentication server feature](https://docs.emailengine.app/using-an-authentication-server/).

## Example: IMAP Account Setup

Here’s a breakdown of the steps a user follows when setting up an IMAP account via EmailEngine’s hosted form.

![](lib_pTNsKLAHHUZrxQKE/v21k64jzcxzryrpt.png?w=400\&h=200\&fit=crop)

### 1. User Selects Account Type

The form will present options to connect either to an IMAP server or to OAuth2 providers like Gmail and Outlook, depending on what you've configured.

![](lib_pTNsKLAHHUZrxQKE/yfhjxd9r55eyn0lc.png?w=400\&h=200\&fit=crop)

### 2. User Enters General Information

The user provides their general information, such as name and email address. This helps EmailEngine associate the credentials with the correct account.

![](lib_pTNsKLAHHUZrxQKE/vz0s0afjnr3ibfoy.png?w=400\&h=200\&fit=crop)

### 3. Configuring Server Settings

In most cases, EmailEngine will automatically detect and configure the server settings. However, users can adjust these if necessary.

## How Does It Work?

Your application generates a signed URL that links to your EmailEngine installation. This URL includes basic account details (such as account ID and user name) that EmailEngine will use during the setup process.

Once your user is redirected to this URL, EmailEngine handles the rest by displaying the appropriate forms. After successful configuration, the user is redirected back to your application’s designated URL, and you can begin making API calls to interact with their email account.

> **Important:** To display options for Gmail or Outlook accounts, you need to configure these services ahead of time. Follow the specific guides for [Outlook OAuth2](https://docs.emailengine.app/setting-up-oauth2-with-outlook/) and [Gmail OAuth2](https://docs.emailengine.app/setting-up-gmail-oauth2-for-imap-api/) integration.

## Generating a Redirect URL via API

The most efficient way to generate a link to the hosted authentication form is by using EmailEngine's API. Here's an example:

```bash
curl -X 'POST' \
  'https://ee.example.com/v1/authentication/form' \
  -H 'Content-Type: application/json' \
  -d '{
  "account": "example",
  "name": "My Email Account",
  "email": "user@example.com",
  "redirectUrl": "https://myapp/account/settings.php"
}'
```

This API request returns a JSON object containing the `url` field:

```json
{
  "url": "https://ee.example.com/accounts/new?data=eyJhY2NvdW50IjoiZXhh...L0W_BkFH5HW6Krwmr7c"
}
```

You should then redirect your user to this URL (`url` value) to display the authentication form.

### Redirect URL

Once authentication is successful, EmailEngine redirects the user back to the `redirectUrl` you provided. It appends two query parameters to this URL:

1. **account**: The account ID of the registered account. If you provided an `account` value in the original request, this ID will match it; otherwise, EmailEngine will generate one.
2. **state**: Indicates whether the account is **new** ("new") or if an existing account was updated ("existing"). If an existing account was used, its settings are updated, and no new account is created.

## Internal Usage by EmailEngine

EmailEngine itself uses this hosted authentication process for adding new accounts via the admin dashboard, which ensures consistency and reliability in handling email account credentials.

![](lib_pTNsKLAHHUZrxQKE/9h55bdqybw0l5xs3.gif)

## Customizing the Authentication Form

You can personalize the authentication form (e.g., by adding your logo) by editing the header template used for public pages.

![](lib_pTNsKLAHHUZrxQKE/o9gkwlsx96ak24zk.gif)

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
