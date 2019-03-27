# RMap Agent Service Status
Provides link to API documentation and a list of available HTTP verbs for the Agent path.

* [Request](#request)
  * [HTTP Method & Path](#http-method--path)
  * [Request Headers](#request-headers)
  * [Request Body](#request-body)
* [Response](#response)
  * [Response Headers](#response-headers)
  * [Response Body](#response-body)
  * [Possible Response Codes](#possible-response-codes)
* [Behavior](#behavior)
* [Example](#example)

## Request

#### HTTP Method & Path
```
HEAD /agents
```
Example URL: _https://test.rmap-hub.org/api/agents_

#### Request Headers
none

#### Request Body
none

## Response
#### Response Headers

| Header | Value |
|---------|------|
| Allow| HEAD, OPTIONS, GET|
| Link| &#60;https://link_to_documentation&#62;; rel="[http://purl.org/dc/terms/description](http://purl.org/dc/terms/description)"|

#### Response Body
none

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.   If the system is working correctly, an OK response will be returned with a list of available HTTP methods in the header, and a link to the technical documentation as specified in the  `rmapapi.documentationPath`  property. The response will return no body.

## Example

#### Request
```
curl -i -I HEAD https://test.rmap-hub.org/api/agents
```
#### Response
```
HTTP/1.1 200 OK
Allow: HEAD,OPTIONS,GET
Link: <https://github.com/rmap-project/rmap-documentation>;rel="http://purl.org/dc/elements/1.1/description"

[no content]
```