# Retrieve DiSCOs Created by Agent
Retrieves a list of `rmap:DiSCO` URIs for DiSCOs that were created by the Agent referenced.

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
GET /agents/{uri}/discos
```
uri = HTTP encoded URI of Agent to retrieve DiSCOs for. 

This path also supports the following querystring parameters:

| Parameter| Description |
|------|----|
| status<br> (choose one) | DiSCO status filter: <br>- all<br>- active (default)<br>- inactive| 
| from | Date from (UTC). Applied to DiSCO creation Event start date. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
|until | Date to (UTC). Applied to DiSCO creation Event start date. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
| page | Page number as integer of greater than 0. Default is 1.|
| limit | Number of URIs to retrieve. Default is 200. If there are more URIs than the limit, the system will automatically paginate and add `page=1` to your query string.

Example URL: _https://test.rmap-hub.org/api/agents/rmap%3Armd18m7mj4/discos?status=all_

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
| Location | &#60;https://{api-path}/agents/{agent-uri}/discos?{params}>
| Link | &#60;https://{api-path}/agents/{agent-uri}/discos?{params-for-next-page}>;rel="next"<br>_(only appears when paginating and not last page)_ |
| Link | &#60;https://{api-path}/agents/{agent-uri}/discos?{params-for-prev-page}>;rel="previous" <br>_(only appears when paginating and not on first page)_ |
|Link| &#60;https://{api-path}/agents/{agent-uri}/discos?{params-for-first-page}>;rel="first" <br>_(only appears when paginating and not on first page)_ |
| Content-Type * | _Depends on request Accept header:_<br>application/json<br>text/plain |

\* Appears on every OK response.

#### Response Body
Example (application/json):
```
{
"http://purl.org/ontology/rmap#DiSCO": [
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
| 303 | See other (if there are more results than the limit specified) |
| 404| Not found |
| 406| Not acceptable (if incorrect Accept header defined) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches for an Agent using the URI provided by the client.
2. If no matching Agent is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching Agent is found, RMap finds all DiSCOs that cite the Agent as the `prov:wasAssociatedWith` value of their creation Event.
4. If any matching DiSCOs are found, an OK response is returned with a list in the format defined by the `Accept` header. If no matching DiSCOs are found, an empty list is returned.
5. If any errors result in this process not being completed successfully, the user will receive the appropriate error message.

## Example

#### Request
```
curl -i https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916/discos?status=all
```

#### Response
A simple response:
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
"http://purl.org/ontology/rmap#DiSCO": [
    "rmap:547d7wmg6g",
    "rmap:dncjsxm6jk"
    ]
}
```
If there are too many results to list on a single page, then the request will offer a new link to being pagination. Note that some clients will automatically follow the `see other` location:
```
HTTP/1.1 303 See other
Location: https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916/discos?from=20150101120000&until=20190325221459&limit=200&page=1
Content-Type: application/json;charset=UTF-8

[no content]
```
If the client automatically redirects, the new `Location` will load with the `Content-Type` listed. Otherwise, the client can manually follow the Location address to begin pagination. At this point the response will be similar to the first simple example.
