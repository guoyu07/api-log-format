# API Log Format (ALF)

**ALF** *(API Log Format)* is a new HTTP logging format based on [**HAR**][har-spec] *(HTTP Archive Format)*. 

Read the spec below or [skip to the full json example][example].

### Encoding

A log message is REQUIRED to be saved in `UTF-8` encoding. Other encodings are forbidden.

### Differences from HAR

Due to the nature of HAR originating as a browser based format, some elements are not applicable and will be ignored:

- `log`
  - `browser`
  - `pages`
- `entry`
  - `pageref`
  - `cache`
  - `connection`
  - `request`
    - `redirectURL`
    - `cookies`
    - `postData`

*(in addition to the above list all `comment` properties on all objects are ignored.)*

In addition, the following properties are generated by our official agents, and provide a richer view into HTTP requests in API Analytics:

+ [`serviceToken`][message]
+ [`entry.clientIPAddress`][entry]
+ [`request.content`][request]

## List of objects

### message

This object represents the root of the JSON message. The object contains the following name/value pairs:

```js
{
  "serviceToken": "<service token>",
  "har": {...},
}
```

| Name                | Type     | Required   | Description                                                                                             |
| ------------------- | -------- | ---------- | ------------------------------------------------------------------------------------------------------- |
| **`serviceToken`**  | `String` | `required` | obtain yours by registering for a free trial at [APIAnalytics.com][analytics-url]             |
| **`har`**           | [`[HAR]`][har] | `required` | An object containing the version, creator, and log entries. |

### har

A [**HAR**][har-spec] *(HTTP Archive Format)* object.

```js
{
  "log": {...}
}
```

| Name          | Type           | Required   | Description                                                     |
| ------------- | -------------- | ---------- | --------------------------------------------------------------- |
| **`log`**     | [`[Log]`](#log) | `required` | An object that represents the root of exported HAR data.        |

### log

An object that represents the root of exported data.

```js
{
  "version": "1.2",
  "creator": {...},
  "entries": [...]
}
```

| Name                | Type     | Required   | Description                                                                                                     |
| ------------------- | -------- | ---------- | --------------------------------------------------------------------------------------------------------------- |
| **`version`**       | `String` | `required` | Version number of the format *(currently 1.2)*                                                                  |
| **`creator`**       | [`Creator`][creator] | `required` | An object containing name and version information of the log creator agent |
| **`entries`**       | [`[Entry]`][entry]  | `required` | An array of objects, each representing one exported (tracked) HTTP request            |

### creator

This object contains information about the log creator agent and contains the following name/value pairs:

```js
"creator": {
  "name": "My HAR client",
  "version": "1.0"
}
```

| Name          | Type     | Required   | Description                                           |
| ------------- | -------- | ---------- | ----------------------------------------------------- |
| **`name`**    | `String` | `required` | The name of the agent that created the log            |
| **`version`** | `String` | `required` | The version number of the agent that created the log  |

### entry

This object represents an array with all exported HTTP requests.

```js
"entries": [
  {
    "startedDateTime": "2015-01-20T18:22:09.052",
    "serverIPAddress": "10.10.10.10",
    "clientIPAddress": "10.10.10.20",
    "time": 50,
    "request": {...},
    "response": {...},
    "timings": {...}
  }
]
```

| Name                  | Type      | Required   | Description                                                                                                                     |
| --------------------- | --------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **`serverIPAddress`** | `String`  | `optional` | IP address of the server                                                                                                        |
| **`clientIPAddress`** | `String`  | `optional` | IP address of the client                                                                                                        |
| **`startedDateTime`** | `String`  | `required` | Date and time stamp for the beginning of the request (ISO 8601 - `YYYY-MM-DDThh:mm:ss.sTZD`)                                      |
| **`time`**            | `Number`  | `optional` | Total elapsed time of the request in milliseconds. This is the sum of all timing metrics available in the [`Timings`][timings] object |
| **`request`**         | [`Request`][request]  | `required` | An object containing request details.                                                    |
| **`response`**        | [`Response`][response]  | `required` | An object containing response details.                                                |
| **`timings`**         | [`Timings`][timings]  | `required` | An object containing timing details about request/response round trip                             |

### request

This object contains detailed info about performed request.

```js
"request": {
  "method": "GET",
  "url": "http://api.domain.com/path/",
  "httpVersion": "HTTP/1.1",
  "queryString" : [...],
  "headers": [...],
  "headersSize" : 50,
  "bodySize" : 20,
  "content": {...}
}
```

| Name              | Type      | Required   | Description                                                                                                             |
| ----------------- | --------- | ---------- | ----------------------------------------------------------------------------------------------------------------------- |
| **`method`**      | `String`  | `required` | Request method                                                                                                          |
| **`url`**         | `String`  | `required` | Absolute URL of the request                                                                                             |
| **`httpVersion`** | `String`  | `required` | Request HTTP Version                                                                                                    |
| **`queryString`** | [`[QueryString]`][querystring] | `optional` | List of query parameter objects                                                                                         |
| **`headers`**     | [`[Headers]`][headers] | `required` | List of header objects                                                                                                  |
| **`headersSize`** | `Number`  | `required` | Total number of bytes from the start of the HTTP request message until (and including) the double CRLF before the body  |
| **`content`**     | [`Content`][content]  | `optional` | An object containing request body details.                                |
| **`bodySize`**    | `Number`  | `optional` | Size of the request body (POST data payload) in bytes.                                                                  |

### response

This object contains detailed info about the response.

```js
"response": {
  "status": 200,
  "statusText": "OK",
  "httpVersion": "HTTP/1.1",
  "headers": [...],
  "headersSize" : 160,
  "content": {...},
  "bodySize" : 850,
}
```

| Name              | Type      | Required   | Description                                                                                                                  |
| ----------------- | --------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **`status`**      | `Number`  | `required` | Response status                                                                                                              |
| **`statusText`**  | `String`  | `required` | Response status description                                                                                                  |
| **`httpVersion`** | `String`  | `required` | Response HTTP Version                                                                                                        |
| **`headers`**     | [`[Headers]`][headers] | `required` | List of header objects                                                                                                       |
| **`headersSize`** | `Number`  | `required` | Total number of bytes from the start of the HTTP response message until (and including) the double CRLF before the body      |
| **`content`**     | [`Content`][content]  | `optional` | An object containing details regarding the response body.                                            |
| **`bodySize`**    | `Number`  | `optional` | Size of the received response body in bytes. Set to zero in case of responses coming from the cache (304) or no body is sent |


### headers

This object contains list of all headers *(used in [request][request] and [response][response] objects)*.

```js
"headers": [
  {
    "name": "Accept-Encoding",
    "value": "gzip,deflate",
  },
  {
    "name": "Accept-Language",
    "value": "en-us,en;q=0.5",
  }
]
```

### queryString

This object contains list of all parameters & values parsed from a query string, if any *(embedded in `request`(#request) object)*.

```js
"queryString": [
  {
    "name": "param1",
    "value": "value1",
  },
  {
    "name": "param1",
    "value": "value1",
  }
]
```

### content

This object describes details about response content *(used in [request][request] and [response][response] objects)*.

```js
"content": {
  "size": 33,
  "compression": 0,
  "mimeType": "text/html; charset=utf-8",
  "encoding": "base64",
  "text": "",
}
```

| Name              | Type      | Required   | Description                                                                                                                                                                       |
| ----------------- | --------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`size`**        | `Number`  | `required` | Length of the returned content in bytes. *Should be equal to `(request|response).bodySize` if there is no compression and bigger when the content has been compressed*            |
| **`compression`** | `Number`  | `optional` | Number of bytes saved. Leave out if info is not available                                                                                                                         |
| **`mimeType`**    | `String`  | `required` | MIME type of the text field. By default it should be `application/octet-stream`. Include the charset attribute of the MIME type is included, if available.                                                                                    |
| **`encoding`**    | `String`  | `optional` | Encoding used for the text field e.g `base64`.                                                                                                                                    |
| **`text`**        | `String|Null`  | `optional` | The body encoded using the format declared in `encoding`. Leave out or `null` if info is not available. Empty string if the information is available, but there is no body or an empty one  |

Note: you should still construct and send `content` object even if not adding the `text` property.


### timings

This object describes various phases within request-response round trip. All times are specified in milliseconds.

```js
"timings": {
  "blocked": 0,
  "dns": 0,
  "connect": 15,
  "send": 20,
  "wait": 38,
  "receive": 12,
  "ssl": 0,
}
```

| Name          | Type     | Required   | Description                                                       |
| ------------- | -------- | ---------- | ----------------------------------------------------------------- |
| **`blocked`** | `Number` | `optional` | Time spent in a queue waiting for a network connection            |
| **`dns`**     | `Number` | `optional` | DNS resolution time. The time required to resolve a host name     |
| **`connect`** | `Number` | `optional` | Time required to create TCP connection.                           |
| **`send`**    | `Number` | `required` | Time required to send HTTP request to the server                  |
| **`wait`**    | `Number` | `required` | Waiting for a response from the server                            |
| **`receive`** | `Number` | `required` | Time required to read entire response from the server (or cache)  |
| **`ssl`**     | `Number` | `optional` | Time required for SSL/TLS negotiation                             |

*The `time` value for the [request object][request] must be equal to the sum of the timings supplied in this section.*

Following must always be true:

```js
entry.time = entry.timings.blocked + entry.timings.dns +
  entry.timings.connect + entry.timings.send + entry.timings.wait +
  entry.timings.receive + entry.timings.ssl;
```

###### Full Example

```js
{
    "serviceToken": "<my service token>",
    "har": {
        "log": {
            "version": "1.2",
            "creator": {
                "name": "My HAR client",
                "version": "1.0"
            },
            "entries": [
                {
                    "startedDateTime": "2015-01-20T18:22:09.052",
                    "serverIPAddress": "10.10.10.10",
                    "clientIPAddress": "10.10.10.20",
                    "time": 50,
                    "request": {
                        "method": "POST",
                        "url": "http://api.domain.com/path/",
                        "httpVersion": "HTTP/1.1",
                        "queryString": [
                            {
                                "name": "foo",
                                "value": "bar"
                            },
                            {
                                "name": "baz",
                                "value": "abc"
                            }
                        ],
                        "headers": [
                            {
                                "name": "Accept",
                                "value": "text/plain"
                            },
                            {
                                "name": "Cookie",
                                "value": "ijafhIAGWF3Awf93f"
                            }
                        ],
                        "headersSize": 44,
                        "bodySize": 14,
                        "content": {
                            "size": 14,
                            "mimeType": "application/json",
                            "text": "{\"foo\": \"bar\"}"
                        }
                    },
                    "response": {
                        "status": 200,
                        "statusText": "OK",
                        "httpVersion": "HTTP/1.1",
                        "headers": [
                            {
                                "name": "Content-Length",
                                "value": "11"
                            },
                            {
                                "name": "Mime-Type",
                                "value": "text/plain"
                            }
                        ],
                        "content": {
                            "size": 11,
                            "mimeType": "text/plain",
                            "text": "hello world"
                        },
                        "bodySize": 11,
                        "headersSize": 41
                    },
                    "timings": {
                        "blocked": 0,
                        "dns": 0,
                        "connect": 15,
                        "send": 20,
                        "wait": 38,
                        "receive": 12,
                        "ssl": 0
                    }
                }
            ]
        }
    }
}
```

[analytics-url]: http://apianalytics.com "API Analytics"
[har-spec]: http://www.softwareishard.com/blog/har-12-spec/ "Har Specification"
[example]: #full-example "Example ALF Object"
[message]: #message "Message Object"
[har]: #har "HAR Object"
[creator]: #creator "Creator Object"
[entry]: #entry "Entry Object"
[request]: #request "Request Object"
[response]: #response "Response Object"
[headers]: #headers "Headers Object"
[querystring]: #querystring "QueryString Object"
[content]: #content "Content Object"
[timings]: #timings "Timings Object"
