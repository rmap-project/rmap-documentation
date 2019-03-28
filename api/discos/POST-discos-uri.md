# Update an RMap DiSCO
This path allows API users with valid keys to create a new version of a DiSCO. Agents can update their own DiSCOs, which will inactivate the previous version, or they can provide new versions of DiSCO's created by other Agents. 

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
POST /discos/{uri}
```
uri = HTTP encoded URI of DiSCO to update. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6_

#### Request Headers
| Header | Value |
|---------|------|
| Basic Auth * | Your API key and secret (key:secret)|
|Content-Type *<br>(choose one) | text/turtle <br>application/vnd.rmap-project.disco+rdf+turtle <br>application/rdf+xml <br>application/vnd.rmap-project.disco+rdf+xml <br>application/ld+json <br>application/vnd.rmap-project.disco+ld+json|
 
 \*  Required

#### Request Body
The body should contain a new RMap DiSCO (warning: do not put the previous DiSCO version's ID as the DiSCO URI!). See the [DiSCO media type](disco-media-type.md) for the correct format, and view the [example](#example) below to see a full request/response. Also consider reviewing the [_DiSCO Design Best Practices_](https://github.com/rmap-project/rmap-documentation/blob/master/guides/disco-design-best-practices.md) for some guidance on how to form the ideal RMap DiSCO.

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Location* | https://{api-path}/discos/{new-disco-uri} |
| Link* | &#60;https://{api-path}/events/{disco-update-event-uri}&#62;;rel= "http://www.w3.org/ns/prov#wasGeneratedBy" |
| Link | &#60;https://{api-path}/discos/{previous-version-uri}&#62;;rel="predecessor-version" <br> _note: this is not displayed if the Agent is not the same Agent that created the original DiSCO_|

\* Appear on every Created response.

#### Response Body
The body will vary depending on your ID minter, but should looks something like this:
```
ark:/12345/new-disco-uri
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 201| Successfully created|
| 400| Bad request (DiSCO is not correctly formatted)|
| 401| Unauthorized (agent does not have rights to create a DiSCO) |
| 409| Conflict (Agent attempting to update old version of DiSCO) |
| 413| Request too large|
| 415| Unsupported media type (if something other than a DiSCO is posted)|
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  The user provides a DiSCO as RDF, an API key, and the content-type of the request.
2.  The system checks the API key. Insufficient authorization will result in failed transaction and request to check API key credentials.
3.  Once successfully authenticated, RMap checks whether this is the first DiSCO created by the Agent. 
    * If it is the first DiSCO, and the Agent doesn't already exist in the triplestore, a new `rmap:Agent` graph is created based on information provided at sign up, along with an `rmap:Event` capturing information about the Agent creation.
    * If the Agent already exists, the Agent information is loaded into memory.
4.  RMap verifies that the `Content-Type` provided is a supported type, if not an _Unsupported Media Type_ notification is returned.
5.  If the media type is correct, RMap verifies that the DiSCO is valid according to the `Content-Type` cited. If it is not, a _Bad Request_ response is returned.
6.  If the DiSCO is correctly formed, the system assigns a unique `rmap:DiSCO` URI to the package received.
7.  The graph provided by the client is captured as an `rmap:DiSCO` in the RMap triplestore.
8.  A new `rmap:Event` is generated with one of two event types:
    * `rmap:Update` if the creator of DiSCO being updated is the same as the Agent that is providing an updated DiSCO. In this instance the old DiSCO is referenced as the `rmap:inactivatedObject` and the new DiSCO is listed as the `rmap:derivedObject`.
    * `rmap:Derivation` if the creator of the DiSCO being updated is different from the Agent providing the updated DiSCO. In this instance the original DiSCO is referenced as the `rmap:sourceObject` and the new DiSCO is listed as the `rmap:derivedObject`.
9.  If any errors result in this process not being completed successfully, the entire transaction will be rolled back and the triplestore will be unchanged. The user will receive an appropriate error message.
10. If successful, the client receives a Created HTTP response containing links to the new DiSCO and Event URI.

## Example

#### Request
This example assumes the POSTing rmap:Agent is the same as the Agent that created the original.
```
curl -u $(<rmap.key)
    -X POST
    -d @disco.rdf
    --header "Content-Type:text/turtle"
    https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6
```
rmap.key file:
```
key:secret
```
disco.rdf file:
```
@prefix dc: <http://purl.org/dc/terms/> .
@prefix cito: <http://purl.org/spar/cito/> .
@prefix ore: <http://www.openarchives.org/ore/terms/> .

<> a <http://purl.org/ontology/rmap#DiSCO> ;
	<dc:creator> <http://orcid.org/0000-0002-9354-8328> ;
	<ore:aggregates> <https://doi.org/10.1109/InPar.2012.6339604> ;
	<ore:aggregates> <https://doi.org/10.5281/zenodo.10307> .
<https://doi.org/10.1109/InPar.2012.6339604>
	a <http://purl.org/spar/fabio/JournalArticle> .
<https://doi.org/10.5281/zenodo.10307>
	a <http://purl.org/dc/dcmitype/Software> ;
	<cito:cites> <https://doi.org/10.1109/InPar.2012.6339604> .
```
#### Response
```
HTTP/1.1 201 Created
Location: https://test.rmap-hub.org/api/discos/rmap%3A03aj4d92sv
Link: <https://test.rmap-hub.org/api/events/rmap%3Az2c75hqlc8>;rel="http://www.w3.org/ns/prov#wasGeneratedBy"
Link: <https://{api-path}/discos/rmap%3A8w9ghx3tv6>;rel="predecessor-version" 

rmap:03aj4d92sv
```
