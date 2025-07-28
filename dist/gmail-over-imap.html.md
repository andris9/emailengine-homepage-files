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

# Gmail over IMAP

Use OAuth2 to authenticate IMAP and SMTP accounts. This configuration is ideal if you need the IMAP proxy feature provided by EmailEngine.

[YouTube video player](https://www.youtube.com/embed/U5lAe3U08BU?si=Mpog1iou_SktbslC)

> Click [here](/oauth2-configuration) to see other types of OAuth2 configurations or follow [this tutorial](https://docs.emailengine.app/setting-up-gmail-oauth2-for-imap-api/) for a detailed step-by-step guide.

### Setting up Gmail IMAP integration with EmailEngine

Follow these steps to configure Gmail IMAP with EmailEngine using OAuth2 authentication:

1. **Open the Google Cloud Console**\
   Go to the [Google Cloud Console](https://console.cloud.google.com/). This is where you manage your Google APIs and services, necessary for enabling OAuth2 authentication for Gmail IMAP access.

2. **Create a new project**\
   Click "Create Project" to set up a new Google Cloud project. Each project serves as a container for managing settings like OAuth credentials and API access.

3. **Select the created project**\
   Once your project is created, select it to ensure that the configurations and changes you make apply to this project.

4. **Enable Gmail API**\
   Navigate to *APIs & Services* → *Enabled APIs and services*. Search for "Gmail API" and enable it. Even though we are configuring IMAP, the Gmail API must be enabled to manage OAuth2 access.

5. **Set up OAuth consent screen**\
   Navigate to *APIs & Services* → *OAuth consent screen*. Google requires a consent screen to notify users of the data your app will access. Choose whether the app will be **Internal** (for users in the same Google Workspace) or **External** (for any Gmail user). In this tutorial, we use "Internal."

6. **Fill in the OAuth consent form**\
   Fill in the necessary details about your app on the consent form. For example, use the URL of your EmailEngine instance as the "Application home page." This allows Google to recognize your app and provides users with transparency on the data it will access.

7. **Configure OAuth scopes**\
   Once the consent form is filled out, click *Save and continue* to get to the scope configuration screen. Click *Add or remove scopes* and add the following scope:

   * `https://mail.google.com`\
     This scope allows your app to access Gmail through IMAP and SMTP. Click *Save and continue*.

8. **Create OAuth credentials**\
   Navigate to *APIs & Services* → *Credentials*, then click *Create Credentials* → *OAuth client ID*. These credentials allow your app to authenticate users and access Gmail IMAP and SMTP services.

9. **Set up OAuth client ID**\
   Select *Web application* as the application type. Under "Authorized JavaScript origins", add your EmailEngine URL. Under "Authorized redirect URIs", add your EmailEngine URL with the `/oauth` path (e.g., `https://your-emailengine-url.com/oauth`). This enables the redirect after users authenticate.

10. **Download the credentials**\
    After the client ID is created, download the OAuth credentials as a JSON file. This file contains the necessary authentication data for integrating with Gmail IMAP.

11. **Create a new Gmail OAuth2 app in EmailEngine**\
    Open EmailEngine and create a new **Gmail OAuth2** application. This will set up the integration between EmailEngine and Gmail over IMAP.

12. **Upload the JSON credentials**\
    In EmailEngine, select the downloaded JSON file to autofill configuration details such as client ID and secret. This links EmailEngine with Google’s OAuth system.

13. **Choose IMAP and SMTP as the base scope**\
    When setting up the OAuth2 app in EmailEngine, choose **IMAP and SMTP** as the base scope. This ensures that EmailEngine will authenticate and work over IMAP for email retrieval and SMTP for sending.

14. **Complete the registration**\
    Click *Register app* in EmailEngine to complete the integration process. Your app is now ready to use Gmail IMAP through OAuth2 for secure email management.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
