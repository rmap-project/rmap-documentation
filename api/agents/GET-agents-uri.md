# Retrieve RMap Agent
Retrieve RMap Agent referenced in the URL path. 

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
GET /agents/{uri}
```
uri = HTTP encoded URI of Agent to retrieve. 

Example URL: _https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916_

#### Request Headers
| Header | Value |
|---------|------|
| Accept<br>(choose one) | text/turtle _(default)_<br>application/vnd.rmap-project.agent+rdf+turtle <br>application/rdf+xml <br>application/vnd.rmap-project.agent+rdf+xml <br>application/ld+json <br>application/vnd.rmap-project.agent+ld+json|

#### Request Body
none

## Response
#### Response Headers

Below are the possible response headers. Note that the link for status is "active". So long as an Agent exists, it is active. Currently Agents are never inactivated or deleted in RMap.

| Header | Value |
|---------|------|
| Location * | https://{api-path}/agents/{agent-uri}|
| Link * | &#60;http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus"|
|Content-Type* |_Depends on request Accept header:_<br>- text/vnd.rmap-project.agent+turtle  _(default)_<br>- text/vnd.rmap-project.agent+rdf+xml<br>- text/vnd.rmap-project.agent+ld+json |

\* Appears on every OK response.

#### Response Body
An RDF serialization of the RMap Agent requested. The serialization will depend on the `Accept` type defined in the Request Header. For more information on what an Agent looks like, view the [Agent Media Type document](agent-media-type.md). Also view the [example](#example) at the bottom of this page.

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 404| Not found |
| 406 | Not acceptable (if incorrect accept type defined) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1. RMap searches for the Agent ID provided in the URL path. 
2. If no matching Agent is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching Agent is found, the status is returned  as "active" and an RDF serialization of the Agent is provided in the body of the response.
4. If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i 
    --header "Accept:application/rdf+xml"
    https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916
```

#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/agents/rmap%3At1g1jwt916
Link: <http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus"
Content-Type: application/vnd.rmap-project.agent+rdf+xml; version=1.0

<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
	xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">

<rdf:Description rdf:about="rmap:t1g1jwt916">
	<rdf:type rdf:resource="http://purl.org/ontology/rmap#Agent"/>
	<name xmlns="http://xmlns.com/foaf/0.1/">RMap IEEE Loader</name>
	<identityProvider xmlns="http://purl.org/ontology/rmap#" rdf:resource="https://dev.rmap-hub.org#Administrator"/>
	<userAuthId xmlns="http://purl.org/ontology/rmap#" rdf:resource="rmap:/authid/8e57baa6dd134f65b7a5bbd16ffd8e82ded84053fbefe36278df2835681ce46"/>
</rdf:Description>

</rdf:RDF>
```
