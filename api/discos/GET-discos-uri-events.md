# Retrieve DiSCO Events
Retrieves a list of `rmap:Event` URIs for Events that have affected the DiSCO referenced.

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
GET /discos/{uri}/events
```
uri = HTTP encoded URI of DiSCO to get the Events for. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/events_

#### Request Headers
| Header | Value |
|---------|------|
| Accept <br> (choose one) | application/json _(default)_<br>text/plain|
 
#### Request Body
none

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Location * | https://{api-path}/discos/{disco-uri} |
| Content-Type * | _Depends on request Accept header:_<br>application/json<br>text/plain |

\* Appears on every OK response.

#### Response Body
Example (application/json):
```
{
"http://purl.org/ontology/rmap#Event": [
    "rmap:547d7wmg6g",
    "rmap:dncjsxm6jk"
    ]
}
```
Example (text/plain):
```
rmap:547d7wmg6g
rmap:dncjsxm6jk
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 404| Not found |
| 406| Not acceptable (if incorrect Accept header defined) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches for a DiSCO using the URI provided by the client.
2. If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching DiSCO is found, RMap notes the System Agent that created that DiSCO.
4. If a matching DiSCO is found, RMap finds all Events which reference the DiSCO, regardless of the Agent associated with the event.
5. If any errors result in this process not being completed successfully, the user will receive the appropriate error message
6.  If completed successfully RMap returns an HTTP OK response with a list of Event URIs in the HTTP response body, formatted as JSON or plain text.

## Example

#### Request
```
curl -i https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/events
```

#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7
Content-Type: application/json;charset=UTF-8

{
"http://purl.org/ontology/rmap#Event": [
    "rmap:547d7wmg6g",
    "rmap:dncjsxm6jk"
    ]
}
```
