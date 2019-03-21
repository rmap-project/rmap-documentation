# Retrieve DiSCO Versions (same Agent)
Retrieves a list of DiSCO URIs that are versions of the DiSCO referenced and created by the same RMap Agent.

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
GET/discos/{uri}/agentversions
```
uri = HTTP encoded URI of DiSCO to get the versions of. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/agentversions_

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
"http://purl.org/ontology/rmap#DiSCO": [
	"rmap:djf92kdjjk2",
	"rmap:ksjdfas72834"
	]
}
```
Example (text/plain):
```
rmap:djf92kdjjk2
rmap:ksjdfas72834
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 404| Not found |
| 406 | Not acceptable (if incorrect Accept header defined) |
| 410| Gone (DiSCO is tombstoned or deleted) 
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches for a DiSCO using the URI provided by the client.
2.  If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header
3.  If a matching DiSCO is found, RMap finds all DiSCOs labelled as versions of the DiSCO provided that were also created by the same Agent as the one referenced. It does this by:
	* Identifying the Event that created the DiSCO, and retrieving the `Event/rmap:lineageProgenitor` 
	* Retrieving a list of DiSCO's whose `Event/prov:generated` Event references the same lineageProgenitor. 
4. If any errors result in the process not being completed successfully, the user will receive an appropriate error response.
5. If successful, RMap returns an HTTP OK response with a list of DiSCO URIs (including the one submitted) in the HTTP response body.  

## Example

#### Request
```
curl -i 
    --header "Accept:application/json"
    https://test.rmap-hub.org/api/discos/rmap%3A9w0vtef2d5/agentversions
```

#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/discos/rmap%3A9w0vtef2d5
Content-Type: application/json;charset=UTF-8

{
"http://purl.org/ontology/rmap#DiSCO": [
	"rmap:rkdjk3kdk20",
	"rmap:rdk20aknq65"
	]
}
```
