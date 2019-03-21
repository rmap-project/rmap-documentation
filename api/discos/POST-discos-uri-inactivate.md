# Inactivate an RMap DiSCO
This path allows an API user to set the status of an existing DiSCO to _inactive_. Inactive DiSCOs are still publicly available for read access, but the inactive flag implies they may be outdated or inaccurate. Only the RMap Agent that created a DiSCO can inactivate it.

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
POST /discos/{uri}/inactivate
```
uri = HTTP encoded URI of DiSCO to inactivate. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6_

#### Request Headers
| Header | Value |
|---------|------|
| Basic Auth * | Your API key and secret (key:secret)|
 
 \*  Required

#### Request Body
none

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Location* | https://{api-path}/discos/{inactivated-disco-uri} |
| Link* | &#60;https://{api-path}/events/{disco-inactivation-event-uri}&#62;;rel="http://purl.org/ontology/rmap#inactivation"|

\* Appear on every OK response.

#### Response Body
none

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 401| Unauthorized (agent does not have rights to create a DiSCO) |
| 404| Not found |
| 410| Gone (DiSCO is tombstoned or deleted) 
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  The user provides an API key and a target DiSCO URI.
2.  The system checks the API key. Insufficient authorization will result in failed transaction and request to check API key credentials.
3.  Once authenticated, RMap searches for the DiSCO using the URI provided by the client
4.  If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header.
5. If a matching DiSCO is found, RMap verifies that the `rmap:Agent` inactivating the DiSCO is the same Agent that created it. If not it will return an Unauthorized response.
6. If the Agent is allowed to change the status, and the status is not already _inactivated_, a new Event is created with the type `rmap:Inactivation`, and indicates the URI of the inactivated DiSCO. 
7. If any errors result in the process not being completed successfully, the entire transaction will be rolled back and the triplestore will be unchanged. The user will receive an appropriate error response.
8. If successful, the client will recieve an OK response with a link to the Event and the DiSCO affected.  

## Example

#### Request
This example assumes the POSTing rmap:Agent is the same as the Agent that created the original.
```
curl -u $(<rmap.key)
    -X POST
    https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/inactivate
```
rmap.key file:
```
key:secret
```
#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6
Link: <https://test.rmap-hub.org/api/events/rmap%3A2r7dhs4hs1>;rel="http://purl.org/ontology/rmap#inactivation"

[no content]
```
