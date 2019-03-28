# Retrieve RMap Agents that Made Assertion
Retrieve a list of URIs for RMap Agents that made a specific assertion.

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
GET /stmts/{s}/{p}/{o}/agents
```
* s= HTTP encoded Subject part of statement
* p= HTTP encoded Predicate part of statement
* o= HTTP encoded Object  part of statement

This path also supports the following querystring parameters:

| Parameter| Description |
|------|----|
|status| Status of DiSCO or Agent that contains the Statement<br>- all<br>- active _(default)_<br>- inactive |
| from | Date from (UTC). Applied to start date of the creation Event for the object that the Statement is contained in. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
|until | Date to (UTC). Applied to start date of the creation Event for the object that the Statement is contained in. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
|agents| Comma separated list of RMap Agent URIs. This will filter by the Agent that generated the graph the Statement is contained in.|
| page | Page number as integer of greater than 0. Default is 1.|
| limit | Number of URIs to retrieve. Default is 200. If there are more URIs than the limit, the system will automatically paginate and add `page=1` to your query string.

Example URL using this Statement:
```
<https://osf.io/rxgmb/> <http://purl.org/dc/terms/identifier> <ark:/c7605/osf.io/rxgmb> .
```
URL:<br>
 _https://test.rmap-hub.org/api/stmts/https%3A%2F%2Fosf.io%2Frxgmb%2F/http%3A%2F%2Fpurl.org%2Fdc%2Fterms%2Fidentifier/ark%3A%2Fc7605%2Fosf.io%2Frxgmb/agents?status=all_

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
| Link | &#60;https://{api-path}/stmts/{s}/{p}/{o}/agents?{params-for-next-page}>;rel="next"<br>_(only appears when paginating and not last page)_ |
| Link | &#60;https://{api-path}/stmts/{s}/{p}/{o}/agents?{params-for-prev-page}>;rel="previous" <br>_(only appears when paginating and not on first page)_ |
|Link| &#60;https://{api-path}/stmts/{s}/{p}/{o}/agents?{params-for-first-page}>;rel="first" <br>_(only appears when paginating and not on first page)_ |
| Content-Type * | _Depends on request Accept header:_<br>application/json<br>text/plain |

\* Appears on every OK response.

#### Response Body
Example (application/json):
```
{
"http://purl.org/ontology/rmap#Agent": [
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
1.  RMap searches for triples whose subject or object match the Resource URI provided
2. The system compiles a list of URIs of `rmap:DiSCO` and `rmap:Agent` that contain a statement matching the subject, predicate, and object provided. Additional filters are applied according to the parameters provided.
3. If no RMap Objects contain the Statement provided after filters are applied, an HTTP Not Found response is returned with a basic default error.
4. If there are matching Objects, the Agent that created that object is retrieved. A list of all asserting Agents are returned in an HTTP OK response with URIs serialized in the format defined by the `Accept` header.
5. If any errors result in this process not being completed successfully, the user will receive the appropriate error message.

## Example

#### Request
```
curl -i https://test.rmap-hub.org/api/stmts/https%3A%2F%2Fdoi.org%2F10.5194%2Fos-13-77-2017/http%3A%2F%2Fpurl.org%2Fspar%2Fcito%2Fcites/https%3A%2F%2Fdoi.org%2F10.1029%2F2005gl022751/agents?status=all
```

#### Response
Response as application/json:
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
    "http://purl.org/ontology/rmap#DiSCO": [
        "rmap:0k6djhb7fp"
    ]
}
```
If there are too many results to list on a single page, then the request will offer a new link to being pagination. Note that some clients will automatically follow the `see other` location:
```
HTTP/1.1 303 See other
Location: https://test.rmap-hub.org/api/stmts/https%3A%2F%2Fdoi.org%2F10.5194%2Fos-13-77-2017/http%3A%2F%2Fpurl.org%2Fspar%2Fcito%2Fcites/https%3A%2F%2Fdoi.org%2F10.1029%2F2005gl022751/agents?from=20150101120000&until=20190325221459&limit=200&page=1
Content-Type: application/json;charset=UTF-8

[no content]
```
If the client automatically redirects, the new `Location` will load with the `Content-Type` listed. Otherwise, the client can manually follow the Location address to begin pagination. At this point the response will be similar to the first simple example.
