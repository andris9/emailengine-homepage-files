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

# Document Store

Syncing emails to ElasticSearch

EmailEngine has an optional feature to sync all emails to a local document store backend. Currently, only ElasticSearch can be used as such a backend.

> EmailEngine supports ElasticSearch 8+. Older versions of ElasticSearch and otherwise compatible software like OpenSearch are not supported.

If the document store syncing is enabled, EmailEngine copies email contents for all new emails (excluding attachments) to ElasticSearch as email documents.

Whenever you request message listings or want to search for messages, EmailEngine would use ElasticSearch as the source of the data instead of the actual IMAP account. This approach has many upsides:

1. ElaticSearch is not rate-limited. You can run as many requests as your servers can handle. With IMAP, you always need to consider that requests against a single email account can be processed serially, not in parallel, and each such request takes time.
2. As the stored email document looks the same as what you'd get from the IMAP server with the message details API request, you can skip EmailEngine entirely and run your requests against ElasticSearch directly. This would enable aggregations and grouping that are not supported by EmailEngine natively.

## Enabling the Document Store

###

1. Navigate to Configuration and Document Store.
2. Fill required connection details
3. Open the hamburger menu from the top right and select "test connection"
4. If the connection test succeeded, mark the "Enable syncing" checkbox and save changes

![Screenshot 2022-12-15 at 14.03.43.png](lib_pTNsKLAHHUZrxQKE/56usbi9sdq6zeq8h.png?w=800\&h=500\&fit=crop)

Once the Document Store is set up and enabled, EmailEngine starts syncing all new emails for existing accounts and all emails for new accounts to ElasticSearch. As it takes time to process each email, it might take a long time to process larger email accounts.

> Existing emails for an account are synced only if the account is added to EmailEngine while the Document Store syncing is enabled.

## Making requests against the Document Store

By default, when making API requests, EmailEngine will still use IMAP as the backend, even if the Document Store has been enabled. To force EmailEngine to use the Document Store, add a GET query argument `documentStore=true` to all API requests.

Document Store is only available for regular read requests, such as listing or searching messages. If you want to fetch attachments or update flags for messages, these requests will always go against the IMAP server.

> Requests against the Document Store do not require setting the path option. Unlike with IMAP, where you can only search for emails from a specific folder, Document Store allows you to search all messages from the entire account.

The following example searches for unseen emails from the entire account (`path` is not set for the request):

```
curl -XPOST -s "http://example.com/v1/account/example/search?documentStore=true" \
    -H "Authorization: Bearer test123" \
    -H "Content-type: application/json" \
    -d '{
  "search": {
    "unseen": true
  }
}'
```

## Custom search terms

With searches against the Document Store, you can use an additional filtering option called `documentQuery`. This is a search object structure that EmailEngine would pass directly to ElasticSearch when running the query. The custom query would be merged with regular search options, so you'd still have to provide the search query object, but you can leave it empty.

The following example searches for unseen emails with attachments. IMAP searches do not allow searching for attachments, but in ElasticSearch, it is possible. Note, though, that the attachment field is stored as a nested document, so the query must be built as a nested query as well. This is why the resulting query looks quite complicated.

All attachments have an `"id"` property, so the query matches any document with `attachment.id` property set.

```
curl -XPOST -s "http://example.com/v1/account/example/search?documentStore=true" \
    -H "Authorization: Bearer test123" \
    -H "Content-type: application/json" \
    -d '{
  "search": {
    "unseen": true
  },
  "documentQuery": {
    "nested": {
      "path": "attachments",
      "query": {
        "bool": {
          "must": [
            {
              "exists": {
                "field": "attachments.id"
              }
            }
          ]
        }
      }
    }
  }
}'
```

> Note that the query is basically a filter. This means you can only use filtering terms but not anything more advanced like aggregations or grouping. You'd have to run your queries against the ElasticsEarch server directly for these types of queries.

## Filtering and pre-processing

In the Document Store configuration page, there is a tab called "Pre-processing". This allows you to filter and modify email contents before these are synced to the Document Store.

Message pre-processing consists of two components.

#### Filtering function

First is the filtering function. This is a tiny program written in Javascript. The function takes the email payload as an argument and returns if the email should be synced or not based on that input.

The following example filter skips all emails for the account "example". These emails are not synced to the Document Store.

```
if(payload.account === "example"){
    return false;
}
return true;
```

#### Mapping function

Second is the mapping function. It takes the email payload and morphs it into the required output structure for syncing. Mapping is mostly useful for adding new fields, or removing PII data from existing fields. This function is also a JavaScript code, just like the filtering function. Whatever the function returns will be synced to the Document Store. Protected fields like `id`, `account` and `path` can not be modified and will be always included as part of the output.

The following example adds a new field called `type` to the synced email.

```
payload.type = 'email';
return payload;
```

> **NB!** If you add additional fields to synced emails, you probably should create a field type mappings for these fields first. Otherwise, ElasticSearch generates the type mapping dynamically itself, and it might not be what you expect.

## Field mappings

###

The field mappings page in the Document Store settings allows you to define additional field type mappings for email documents stored in ElasticSearch.

###

EmailEngine automatically maps types for all the fields it uses. For other fields, for example, the custom fields added with the Document Store pre-processing functions, EmailEngine does not set any type mappings.

###

Sometimes it is fine to let ElasticSearch handle types automatically, as the dynamic mapper is good enough. Not always, though.

![Screenshot 2022-12-15 at 17.05.33.png](lib_pTNsKLAHHUZrxQKE/ct4v6crkagc53w9r.png?w=800\&h=500\&fit=crop)

> **NB!** Beware, once a field mapping has been set, you can't modify it unless you migrate the entire index. The same applies to dynamically mapped fields – if ElasticSearch finds a field value without a type mapping, it will set it dynamically, and you can't modify that type later. So define any mappings you want to use before storing data in ElasticSearch.

EmailEngine allows you to create basic types only. For example, keywords, numbers, etc. For more complex object types, you would have to manage these manually.

You can generate new field mappings using *curl* like this:

```
curl -XPUT -k "https://localhost:9200/emailengine/_mapping?pretty=true" \
  -u "elastic:PASSWORD" \
  -H "Content-type: application/json" \
  -d '{
    "properties": {
      "type": {
        "type": "keyword",
        "ignore_above" : 128
      }
    }
  }'
```

Read more about it [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/explicit-mapping.html).

© 2021-2025 Postal Systems OÜ

[Terms of Service](https://postalsys.com/tos)[Privacy Policy](/privacy-policy)[About](/about)[EmailEngine License](https://emailengine.srv.dev/license.html)

* [](https://twitter.com/emailengine)
* [](https://github.com/postalsys/emailengine)
