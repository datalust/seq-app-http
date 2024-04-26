# Seq.App.HttpRequest [![Build status](https://ci.appveyor.com/api/projects/status/63ki29bjjgk8htn3/branch/dev?svg=true)](https://ci.appveyor.com/project/datalust/seq-app-httprequest/branch/dev) [![NuGet Package](https://img.shields.io/nuget/vpre/seq.app.httprequest)](https://nuget.org/packages/seq.app.httprequest)

Send events and notifications from Seq to a remote HTTP/REST/Webhook endpoint. Requires Seq 2021.4 or better.

## Getting started

Install the app under _Settings > Apps_. The app package id is `Seq.App.HttpRequest`.

Visit the Seq documentation for [detailed information about installing and configuring Seq Apps](https://docs.datalust.co/docs/installing-seq-apps).

## Configuration

Instances of the app support the following properties.

| Property | Description | Default |
| --- | --- | --- |
| **URL** | The target URL. This is a template based on event properties, for example, `https://api.example.com/notify?to={Email}`. Placeholders in templates will be URI-encoded. | |
| **Method** | The HTTP method to use. | `POST` |
| **Body** | The request body to send. | |
| **Body is a template** | Whether to treat the body as a template based on event properties, for example `New request from: {Email}`, or to send the body as-is. | `false` |
| **Media Type** | Media type describing the body. | |
| **Authentication Header** | An optional `Name: Value` header, stored as sensitive data, for authentication purposes. | |
| **Other Headers** | Additional headers to send with the request, one per line in `Name: Value` format. | |
| **Extended Error Diagnostics** | Whether or not to include outbound request bodies, URLs, etc., and response bodies when requests fail. | `false` |

### Template language

The URL and body settings are templates that use braces to insert data from the event or notification that is being received by the app.

 * Event and notification properties are inserted using `{Name}`
 * Built-in properties use [_CLEF_ names](https://github.com/serilog/serilog-formatting-compact#reified-properties), for example, the timestamp is `@t`, message is `@m`, and level is `@l`
 * Braces are escaped by doubling: `{{` for an opening brace, and `}}` for closing
 * Substitutions can use most Seq query language expressions: `{1 + 2}`, `{Round(Price, 2)}`, etc.

To send a well-formed JSON payload based on event properties, construct a Seq object literal, and place it between spaced braces. For example:

```
{ {
   Timestamp: @t,
   Source: 'Seq',
   Contact: { Name: CompanyName, Type: 'Company' }
} }
```

This example will result in request bodies resembling:

```json
{
  "Timestamp": "2021-09-15T06:33:21.432",
  "Source": "Seq",
  "Contact": {
    "Name": "Datalust Pty Ltd",
    "Type": "Company"
  }
}
```

Templates support repetition, conditionals, and many other features. For a more complete language reference, see [the _Serilog.Expressions_ README](https://github.com/serilog/serilog-expressions#language-reference).

## Development

To **run the app locally**, without installing the package into Seq, use `Run.ps1` in the repository root. This requires `seqcli` on the path.

This will stream events from a Seq instance at `http://localhost:5341`, and send HTTP requests to `http://localhost:5050`. Modify the script to
specify different app settings.

To easily **inspect your generated HTTP requests**, check out [Webhook.site](https://webhook.site).

## Acknowledgements

Templating support is based on code from [_Serilog.Expressions_](https://github.com/serilog/serilog-expressions).
