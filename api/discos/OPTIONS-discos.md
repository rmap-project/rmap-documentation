# Explain RMap DiSCO Service
This path provides a link to the API documentation, a list of available HTTP verbs for the DiSCO path, plus additional information about the service.

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
OPTIONS /discos
```
Example URL: _https://test.rmap-hub.org/api/discos_
#### Request Headers
none

#### Request Body
none

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Allow| HEAD, OPTIONS, GET, POST, DELETE|
| Link| &#60;https://link_to_documentation&#62;; rel="[http://purl.org/dc/terms/description](http://purl.org/dc/terms/description)"|

#### Response Body
`{"description":"https://link_to_documentation"}`

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  If the system is working correctly, an OK response will be returned with a list of available HTTP methods in the header, a link to the technical documentation as specified in the  `rmapapi.documentationPath`  property, and information about the API as JSON in the body.

## Example

#### Request
```
curl -i -X OPTIONS https://test.rmap-hub.org/api/discos
```
#### Response
```
HTTP/1.1 200 OK
Allow: HEAD,OPTIONS,GET,POST,DELETE
Link: <https://github.com/rmap-project/rmap-documentation/>;rel="http://purl.org/dc/elements/1.1/description"

{"description":"https://github.com/rmap-project/rmap-documentation"}
```