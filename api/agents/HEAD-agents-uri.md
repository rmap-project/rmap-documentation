# View RMap Agent Status
Retrieve status information for referenced Agent.

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
HEAD /agents/{uri}
```
uri = HTTP encoded URI of Agent to retrieve. 

Example URL: _https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916_

#### Request Headers
none

#### Request Body
none

## Response
#### Response Headers
Below are the possible response headers. Note that the link for status is "active". So long as an Agent exists, it is active. Currently Agents are never inactivated or deleted in RMap.

| Header | Value |
|---------|------|
| Location * | https://{api-path}/agents/{agent-uri}|
| Link * | &#60;http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus" |

\* Appear on every OK response.

#### Response Body
none

#### Possible Response Codes

| Code| Description |
|---------|------|
| 200| OK |
| 404| Not found |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1. RMap searches for the Agent ID provided in the URL path. 
2. If no matching Agent is found, an HTTP Not Found response is returned with a basic default header.
3.  If the Agent is found, a response header containing a status of "active" is returned in the response header.
4.  If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i -I HEAD https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916
```
#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916
Link: <http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus"

[no content]
```
