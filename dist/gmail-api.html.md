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

# Gmail API

EmailEngine uses the Gmail API as the messaging backend to provide seamless email integration for your app or service.

[YouTube video player](https://www.youtube.com/embed/8u0w5ve3pQQ?si=1rf-PobC1ITxuaTp)

> Click [here](/oauth2-configuration) to see other types of OAuth2 configurations, or follow [this tutorial](https://docs.emailengine.app/setting-up-gmail-api-access/) for a detailed step-by-step guide.

### Setting up Gmail API integration with EmailEngine

Follow these steps to integrate Gmail API with EmailEngine:

1. **Open the Google Cloud Console**\
   Go to the [Google Cloud Console](https://console.cloud.google.com/). This is where you’ll manage all settings related to Google APIs. You’ll create and configure a project here, which allows you to access Google services like the Gmail API for your app.

2. **Create a new project**\
   In the Google Cloud Console, click “Create Project” and give your project a name. A project is a container for all API configurations, credentials, and permissions, so you need a dedicated project for Gmail API access.

3. **Select the created project**\
   Once your project is created, select it from the list to ensure that any changes you make (such as enabling APIs or creating credentials) are applied to this specific project.

4. **Enable Gmail API**\
   Navigate to *APIs & Services* → *Enabled APIs and services*, then search for "Gmail API" and enable it. This will allow your app to interact with Gmail, including sending and receiving emails through the Gmail API.

5. **Enable Cloud Pub/Sub API**\
   Gmail uses Google’s Cloud Pub/Sub to manage email updates. Navigate back to *APIs & Services* and search for "Cloud Pub/Sub", then enable it. This API will allow Gmail to send real-time notifications to your app about new emails and other events.

6. **Set up OAuth consent screen**\
   Navigate to *APIs & Services* → *OAuth consent screen*. Google requires you to set up a consent screen so users are aware of the data your app is accessing. Choose whether your app will be used **Internally** (for users in the same Google Workspace) or **Externally** (for any Gmail user). In this tutorial, we’ll use "Internal" for simplicity.

7. **Fill in the OAuth consent form**\
   Fill in the required app details in the consent screen form. Use your EmailEngine instance's URL as the "Application home page." This helps Google verify your app and notify users of the access being requested.

8. **Configure OAuth scopes**\
   After saving the consent form, you’ll be asked to configure OAuth scopes. Click *Add or remove scopes*, then add the `https://www.googleapis.com/auth/gmail.modify` scope. This allows your app to read, send, and delete Gmail messages. Click *Save and continue*.

9. **Create OAuth credentials**\
   Navigate to *APIs & Services* → *Credentials*, then click *Create Credentials* → *OAuth client ID*. This step allows your app to request access to Gmail accounts on behalf of users, using OAuth2.

10. **Set up OAuth client ID**\
    Select *Web application* as the application type. Under "Authorized JavaScript origins", add your EmailEngine’s URL. Under "Authorized redirect URIs", add your EmailEngine URL with the `/oauth` path (e.g., `https://your-emailengine-url.com/oauth`). This tells Google where to send the authentication response.

11. **Download the credentials**\
    After creating the OAuth client ID, download the credentials in JSON format. This file contains the necessary authentication information for EmailEngine to interact with Gmail.

12. **Set up the webhook service account**\
    Gmail API requires Cloud Pub/Sub to manage email updates. On the *Credentials* page, click *Manage service accounts* to create a service account. This account will handle incoming Pub/Sub messages from Gmail, allowing EmailEngine to receive real-time email updates.

13. **Grant Pub/Sub permissions to the service account**\
    When creating the service account, assign the **Pub/Sub Admin** role to it. This allows the service account to manage Pub/Sub queues and handle Gmail notifications.

14. **Generate service account key**\
    Select the newly created service account and navigate to the *Keys* section. Create a new key in JSON format and download it. This key will be used by EmailEngine to authenticate the service account with Google.

15. **Register the service account in EmailEngine**\
    In EmailEngine, create a new **Gmail Service Account** application. Upload the JSON key file you downloaded from Google Cloud. This links Google’s Pub/Sub service with your email handling backend in EmailEngine, allowing you to receive email updates.

16. **Create a Gmail OAuth2 app in EmailEngine**\
    Create a new **Gmail OAuth2** application in EmailEngine. Use the OAuth2 credentials JSON file you previously downloaded to autofill the details. This connects your app to Gmail through OAuth2.

17. **Assign the service account for webhook handling**\
    Choose the previously created Gmail Service Account as the webhook handler for this Gmail OAuth2 application. This ensures that Gmail API uses Pub/Sub to push email updates to your EmailEngine instance in real-time.

18. **Complete the setup**\
    Click *Register app* in EmailEngine to finalize the integration. Your app is now connected to Gmail via Gmail API, enabling seamless email management and notifications through EmailEngine.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
