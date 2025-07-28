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

# Outlook and MS365

Use OAuth2 to authenticate Outlook and MS365 IMAP and SMTP accounts for secure email access through EmailEngine.

[YouTube video player](https://www.youtube.com/embed/0vBpyhttCpo?si=sruh5bkOc1BDXNi2)

> Click [here](/oauth2-configuration) to see other types of OAuth2 configurations, or follow [this tutorial](https://docs.emailengine.app/setting-up-oauth2-with-outlook/) for a detailed step-by-step guide.

### Setting up MS Graph API integration with EmailEngine

Follow these steps to integrate Outlook and MS365 email accounts with EmailEngine using OAuth2 and Microsoft Graph API:

1. **Open Azure Portal**\
   Go to the [Azure Portal](https://portal.azure.com/), where you will manage all settings and configurations for your Microsoft Graph API integration.

2. **Navigate to Microsoft Entra ID → App Registrations**\
   This is where you will register your app in Azure. App registration allows your app to access Microsoft services like MS Graph for email accounts.

3. **Create a new app registration**\
   Registering a new application creates a unique identity for your app, enabling it to access MS Graph API securely and interact with users' email accounts.

4. **Select a suitable Supported accounts type**\
   Choose the appropriate account type based on your needs. To allow broader access, choose *Accounts in any org and personal accounts*, which supports both Microsoft 365 and personal Outlook accounts.

5. **Set the Redirect URI**\
   Select *Web* as the platform and use your EmailEngine URL with the `/oauth` path (e.g., `https://your-emailengine-url.com/oauth`). This allows Azure to send the OAuth2 response back to EmailEngine after successful authentication.

6. **Copy the Application (client) ID**\
   In the application's overview page, find the *Application (client) ID*. This ID will be used as the *Azure Application Id* in EmailEngine’s OAuth form when configuring the Outlook integration.

7. **Add API Permissions**\
   Navigate to *API Permissions* and click *Add a permission*. This step grants your app the necessary access to interact with users' Outlook and MS365 email accounts.

8. **Enable Microsoft Graph Permissions**

   * Select *Microsoft Graph* → *Delegated permissions*.

   * Search for and enable the following permissions to allow your app to interact with the user's email:

   * If you want to use **IMAP and SMPT** as the base scopes:

     * *IMAP.AccessAsUser.All* (for IMAP access)
     * *SMTP.Send* (for sending emails via SMTP)
     * *offline\_access* (for long-term access to accounts)

   * If you want to use **MS Graph API** as the base scope:

     * *User.Read* (for reading account properties)
     * *Mail.ReadWrite* (for reading emails)
     * *Mail.Send* (for sending emails)
     * *offline\_access* (for long-term access to accounts)

9. **Create a client secret**\
   Navigate to *Certificates & Secrets* and create a new client secret. The client secret is used by your app to authenticate with Azure. Choose an expiration period you are comfortable with. Note that once the secret expires, you'll need to generate a new one and update it in EmailEngine.

10. **Copy the client secret value**\
    Once the client secret is generated, make sure to copy the *Value* of the secret (not the *Secret ID*). This value will be used as the *Client Secret* in EmailEngine's OAuth configuration.

11. **Create a new Outlook application in EmailEngine**\
    Open EmailEngine and create a new **Outlook OAuth2** application. This will establish the connection between EmailEngine and the Microsoft Graph API.

12. **Use the Application ID in EmailEngine**\
    In the OAuth2 application form in EmailEngine, use the Application ID (from Azure’s dashboard) as the *Azure Application Id*.

13. **Use the client secret in EmailEngine**\
    In the EmailEngine form, use the client secret value (the one you copied earlier) as the *Client Secret*. This allows EmailEngine to authenticate with Azure and access Outlook email accounts.

14. **Set the supported account types**\
    In EmailEngine, set the *Supported account types* value. The option *Common* corresponds to the *Accounts in any org and personal accounts* option you selected during the Azure app registration process.

15. **Register the app in EmailEngine**\
    Click *Register app* in EmailEngine to complete the integration. Your app is now configured to use the Microsoft Graph API for accessing Outlook and MS365 email accounts via IMAP and SMTP.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
