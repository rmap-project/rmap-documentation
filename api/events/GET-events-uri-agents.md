# Retrieve Agents Affected by Event
Retrieves a list of URIs for RMap Agents affected by the Event.

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
GET /events/{uri}/agents
```
uri = HTTP encoded URI of Event to retrieve affected Agents for

Example URL: _https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw/agents_

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
| Location | <https://{api-path}/events/{event-uri}> |
| Content-Type * | _Depends on request Accept header:_<br>application/json<br>text/plain |

\* Appears on every OK response.

#### Response Body
Example (application/json):
```
{
"http://purl.org/ontology/rmap#Agent": [
    "rmap:djf96kdfjjk2"
    ]
}
```
Example (text/plain):
```
rmap:djf96kdfjjk2
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 404| Not found |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches for an Event using the URI provided by the client.
2. If no matching Event is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching Event is found and the `rmap:eventTargetType` is a an Agent, all URIs listed as `prov:generated`,  or `rmap:updatedObject` will be returned returned in the body of an OK response.  If the target type is a DiSCO, and empty list is returned.
5. The list of URIs will be in the format defined by the `Accept` header. 
6. If any errors result in this process not being completed successfully, the user will receive the appropriate error message.

## Example

#### Request
```
curl -i 
	--header "Accept:application/json"
	https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw/agents
```

#### Response
```
Location: https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw
Content-Type: application/json;charset=UTF-8

{
"http://purl.org/ontology/rmap#Agent": [
    "rmap:dv41ns25h7"
    ]
}
```