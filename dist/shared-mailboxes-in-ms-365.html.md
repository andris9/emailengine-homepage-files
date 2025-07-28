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

# Accessing Shared Mailboxes in MS365

EmailEngine supports accessing Microsoft 365 (MS365) shared mailboxes, allowing your applications to interact with these mailboxes via a RESTful API.

## Introduction to Shared Mailboxes

Microsoft 365 allows you to create **shared mailboxes** that are not bound to a specific user. Instead, one or multiple users can access these mailboxes using their own credentials to send and receive emails on behalf of the shared mailbox.

EmailEngine enables you to interact with these shared mailboxes through its API by leveraging OAuth2 authentication. This means you can programmatically access shared mailboxes without needing separate credentials for each one.

## Prerequisites

* **Main User Account**: You must have the main user account (with access to the shared mailboxes) added to EmailEngine.
* **OAuth2 Application**: An OAuth2 application configured in both Azure and EmailEngine.
* **Permissions**: Ensure that the main account has the necessary permissions to access the shared mailboxes.

## Adding the Main User Account

Before accessing shared mailboxes, add the main user's Microsoft 365 account to EmailEngine using the standard account creation process. This account should have access to all the shared mailboxes you intend to use.

Refer to the [EmailEngine API documentation](https://api.emailengine.app/#operation/postV1Account) for detailed instructions on adding a new account.

## Adding Shared Mailboxes

To add a shared mailbox to EmailEngine, you'll use an API call that specifies the shared mailbox and instructs EmailEngine to use the main account's credentials.

### API Call Payload

Here's how to structure your API call:

* **`account`**: A unique identifier for the shared mailbox in EmailEngine (e.g., `"shared"`).
* **`name`**: A friendly name for the mailbox (e.g., `"Shared Account"`).
* **`email`**: The email address of the shared mailbox (e.g., `"shared@example.com"`).
* **`oauth2.auth.delegatedUser`**: The email address or MS365 user ID of the shared mailbox.
* **`oauth2.auth.delegatedAccount`**: The EmailEngine account ID of the main user (e.g., `"my-account"`).

#### Request — **POST `/v1/account`**

```json
{
  "account": "shared",                     // optional; null ⇒ auto-generated
  "name": "Shared Account",
  "email": "shared@example.com",
  "oauth2": {
    "auth": {
      "delegatedUser": "shared@example.com",
      "delegatedAccount": "my-account"
    }
  }
}
```

In this example, EmailEngine connects to Microsoft 365 servers and authenticates as `"shared@example.com"` using the OAuth2 tokens from the main account `"my-account"`.

## Using IMAP and SMTP

If your main account uses **IMAP and SMTP** protocols, you can access shared mailboxes without additional configuration.

* **IMAP Access**: EmailEngine accesses the shared mailbox's emails using IMAP.
* **SMTP Limitations**: Shared mailboxes without a full MS365 subscription lack SMTP access.

### Handling Sent Emails

When sending emails from a shared mailbox:

* **SMTP Authentication**: EmailEngine uses the SMTP credentials of the main account.

* **From Address**: The "From" address is set to the shared mailbox's email.

* **Sent Items Duplication**:

  * A copy of the sent email is saved in the shared mailbox's "Sent Items" folder.
  * Another copy is saved in the main account's "Sent Items" folder.
  * **Result**: Duplicate emails in "Sent Items" folders.

## Using Microsoft Graph API

If you prefer to use the **Microsoft Graph API**, additional configuration is required.

### Step 1: Update API Scopes in Azure

Add the following scopes to your Azure OAuth2 application:

1. **Access Azure Portal**: Log in to [Azure Portal](https://portal.azure.com/) and navigate to your app.

2. **API Permissions**: Go to **API Permissions** > **Add a permission** > **Microsoft Graph** > **Delegated permissions**.

3. **Add Scopes**:

   * `User.ReadBasic.All`
   * `Mail.ReadWrite.Shared`
   * `Mail.Send.Shared`

### Step 2: Update Scopes in EmailEngine

1. **Navigate to OAuth2 Application**: In EmailEngine, go to your OAuth2 application's settings.

2. **Edit Additional Scopes**:

   * Find the **Additional Scopes** section.

   * Add the following scopes:

     ```
     User.ReadBasic.All
     Mail.ReadWrite.Shared
     Mail.Send.Shared
     ```

3. **Save Changes**: Click **Save** to apply the new scopes.

### Step 3: Refresh OAuth2 Grant for Main Account

To use the new permissions, update the OAuth2 grant for the main account.

* **Option 1**: **Re-add the Main Account**
  * Delete and re-add the main account in EmailEngine.

* **Option 2**: **Generate a New Authentication URL**

  1. **Create Authentication Link**:

     * Use the [Authentication Form API](https://api.emailengine.app/#operation/postV1AuthenticationForm).
     * Set `account` to your main account's ID (e.g., `"my-account"`).
     * Set `type` to your OAuth2 application's ID in EmailEngine.

  2. **User Authentication**:

     * Provide the link to the main account user.
     * The user must open the link and grant the new permissions.

### Step 4: Add or Reconnect Shared Mailboxes

With updated permissions:

* **Add New Shared Mailboxes**: Use the [Account API](https://api.emailengine.app/#operation/postV1Account) as previously described.
* **Reconnect Existing Mailboxes**: If shared mailboxes were added before, use the [Reconnect API](https://api.emailengine.app/#operation/putV1AccountAccountReconnect) to refresh their access.

## Registering a Microsoft 365 shared mailbox *without* adding the user’s personal account

If you only need the shared mailbox, create **one** EmailEngine account for it and let an authorised user grant consent during OAuth 2.0 login.

#### Request — **POST `/v1/account`**

```json
{
  "account": "shared",                     // optional; null ⇒ auto-generated
  "name":   "Shared mailbox",
  "email":  "shared@example.com",

  "oauth2": {
    "provider":   "AAABkwrwyA8AAAAm",      // ID of the MS365 OAuth2 provider in EmailEngine
    "authorize":  true,                    // ask EmailEngine to return an auth URL
    "redirectUrl":"https://emailengine.app/", // user is sent here after consent

    "auth": {
      "delegatedUser": "shared@example.com" // address or objectId of the shared mailbox
    }
  }
}
```

#### Response

```json
{
  "redirect": "https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=e887..."
}
```

Send the `redirect` URL to a user who **already has Full Access** to the shared mailbox.\
After they sign in and accept the scopes, EmailEngine stores the tokens and the new *shared* account appears in the account listing. A separate personal-mailbox account is **not** required.

##### Field notes

* **`account`** – unique EmailEngine ID; set `null` to auto-generate.
* **`email`** – email address of the shared mailbox. Usually identical to `oauth2.auth.delegatedUser` unless you provide an objectId in that field.
* **`oauth2.auth.delegatedUser`** – the shared mailbox’s address or objectId, *not* the credentials of the user who signs in.
* The signing-in user’s own mailbox is never added to EmailEngine; their login is used solely to authorise the shared mailbox.

Once consent is complete, call any EmailEngine REST endpoint against the `"shared"` account exactly as you would for a normal mailbox.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
