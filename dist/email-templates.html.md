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

# Email Templates

Sending emails using templating and mail-merge

Email templating allows you to prepare and reuse email content efficiently. You can manage templates either by using the [Templates API](https://api.emailengine.app/#tag/templates) or via the administration interface (look for the **Email templates** link on the side menu).

![Email Templates Interface](https://cldup.com/okV3xd67hr.png)

### Basic Usage

When sending emails using the [Submission API endpoint](https://api.emailengine.app/#operation/postV1AccountAccountSubmit), do not set the `"subject"`, `"html"`, or `"text"` properties. Instead, set the `"template"` property with the template ID value. EmailEngine will use the contents of that template to compile the email message.

**Example:**

```bash
curl "http://your-emailengine-instance.local/v1/account/example/submit" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "to": [
      {
        "name": "Recipient Name",
        "address": "recipient@example.com"
      }
    ],
    "template": "AAABgUIbuG0AAAAE",
    "render": {
      "params": {
        "key": "value"
      }
    }
  }'
```

You can include any other valid properties for submission, such as `"reference"` or `"replyTo"`.

## Handlebars Templating

EmailEngine supports Handlebars syntax in templates. To utilize this feature, set the `"render"` property with the necessary replacement parameters.

**Template HTML Content:**

```html
<p>Hello {{params.name}}!</p>
```

**Render Parameters:**

```json
"render": {
  "params": {
    "name": "John"
  }
}
```

### Available Variables

* **Custom Parameters:** Use `{{params.key_name}}` for keys defined in the `render.params` object.
* **Account Variables:** Use `{{account.key_name}}` for sender account-specific keys, such as `{{account.name}}` or `{{account.email}}`. These are predefined and always available.
* **Service Variables:** Use `{{service.key_name}}` for EmailEngine-specific values, for example, `{{service.url}}` for the base URL of your EmailEngine instance.

### Default Values

If a parameter is missing, you can specify a default value:

```html
<p>Hello {{insert params.name "default=Customer"}}!</p>
```

* **With `params.name` defined:**
  ```html
  <p>Hello John!</p>
  ```
* **Without `params.name`:**
  ```html
  <p>Hello Customer!</p>
  ```

### Handling HTML Content

By default, HTML content in replacement parameters is sanitized. To insert unsanitized HTML content, use triple curly braces.

**Sanitized Content:**

```handlebars
{{params.key_name}}
```

For example, the parameter value `{"key_name": "<h1>Hello</h1>"}` will be inserted to the HTML section of the email as `&lt;h1&gt;Hello&lt;/h1&gt;`

**Unsanitized Content:**

```handlebars
{{{params.key_name}}}
```

This inserts the parameter value into the template without any sanitization. The parameter value `{"key_name": "<h1>Hello</h1>"}` will be inserted as `<h1>Hello</h1>`.

***

EmailEngine supports the SendGrid-flavored Handlebars syntax as defined [here](https://docs.sendgrid.com/for-developers/sending-email/using-handlebars).

## Mail-Merge

You can send multiple emails with a single API call using the mail-merge feature. Each recipient receives a separate email with only their address in the `To:` header, allowing for recipient-specific templating variables.

**Note:** When using mail-merge, do not include `"to"`, `"cc"`, or `"bcc"` fields.

**Defining Recipients with `mailMerge`:**

```bash
curl "http://your-emailengine-instance.local/v1/account/example/submit" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "template": "AAABgUIbuG0AAAAE",
    "mailMerge": [
      {
        "to": {
          "name": "Recipient Name 1",
          "address": "recipient1@example.com"
        },
        "params": {
          "name": "Recipient Name 1"
        }
      },
      {
        "to": {
          "name": "Recipient Name 2",
          "address": "recipient2@example.com"
        },
        "params": {
          "name": "Recipient Name 2"
        }
      }
    ]
  }'
```

Each recipient will receive a customized email with their specific information inserted into the template.

## Email Lists

Mail-merge emails can be sent as list emails by setting a `listId` property in the email structure. The `listId` should resemble a domain name. Recipients will recognize emails with the same `listId` as part of the same mailing list.

If `listId` is set, EmailEngine adds common mailing list headers, including a one-click unsubscribe link. Email clients use these headers to provide an easy "unsubscribe" option.

Read more about virtual mailing lists [here](/virtual-mailing-lists).

***

**Note:** Replace `http://your-emailengine-instance.local` with the actual URL of your EmailEngine instance.

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
