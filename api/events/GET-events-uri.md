# Retrieve RMap Event
Retrieve RMap Event referenced in the URL path. 

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

Example URL: _https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw_

#### Request Headers
| Header | Value |
|---------|------|
| Accept<br>(choose one) | text/turtle _(default)_<br>application/vnd.rmap-project.event+rdf+turtle <br>application/rdf+xml <br>application/vnd.rmap-project.event+rdf+xml <br>application/ld+json <br>application/vnd.rmap-project.event+ld+json|

#### Request Body
none

## Response
#### Response Headers

Below are the possible response headers.

| Header | Value |
|---------|------|
| Location * | https://{api-path}/events/{event-uri}|
|Content-Type* |_Depends on request Accept header:_<br>- text/vnd.rmap-project.event+turtle  _(default)_<br>- text/vnd.rmap-project.event+rdf+xml<br>- text/vnd.rmap-project.event+ld+json |

\* Appears on every OK response.

#### Response Body
An RDF serialization of the RMap Event requested. The serialization will depend on the `Accept` type defined in the Request Header. For more information on what an Event looks like, view the [Event Media Type document](event-media-type.md). Also view the [example](#example) at the bottom of this page.

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 404| Not found |
| 406 | Not acceptable (if incorrect accept type defined) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1. RMap searches for the Event ID provided in the URL path. 
2. If no matching Event is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching Event is found, an RDF serialization of the Event is provided in the body of the response.
4. If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i 
    --header "Accept:application/rdf+xml"
    https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw
```
#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/events/rmap%3A931zcrjsvw
Content-Type: application/vnd.rmap-project.event+rdf+xml; version=1.0

<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
	xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">

<rdf:Description rdf:about="rmap:931zcrjsvw">
	<rdf:type rdf:resource="http://purl.org/ontology/rmap#Event"/>
	<wasAssociatedWith xmlns="http://www.w3.org/ns/prov#" rdf:resource="rmap:jm63xsjj52"/>
	<eventType xmlns="http://purl.org/ontology/rmap#" rdf:resource="http://purl.org/ontology/rmap#creation"/>
	<eventTargetType xmlns="http://purl.org/ontology/rmap#" rdf:resource="http://purl.org/ontology/rmap#DiSCO"/>
	<startedAtTime xmlns="http://www.w3.org/ns/prov#" rdf:datatype="http://www.w3.org/2001/XMLSchema#dateTime">2018-01-19T23:16:33.416Z</startedAtTime>
	<endedAtTime xmlns="http://www.w3.org/ns/prov#" rdf:datatype="http://www.w3.org/2001/XMLSchema#dateTime">2018-01-19T23:16:33.421Z</endedAtTime>
	<generated xmlns="http://www.w3.org/ns/prov#" rdf:resource="rmap:dv41ns25h7"/>
	<lineageProgenitor xmlns="http://purl.org/ontology/rmap#" rdf:resource="rmap:dv41ns25h7"/>
</rdf:Description>

</rdf:RDF>
```