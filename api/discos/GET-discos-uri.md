# Retrieve RMap DiSCO
Retrieve RMap DiSCO referenced in the URL path. For versioning, this path is considered to be a DiSCO "Memento" - a snapshot of the DiSCO in time. The header contains version information based on this standard. For more information, visit the  [Memento website](http://mementoweb.org/guide/).

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
GET /discos/{uri}
```
uri = HTTP encoded URI of DiSCO to retrieve. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A1c59zw43jh_

#### Request Headers
| Header | Value |
|---------|------|
| Accept<br>(choose one) | text/turtle _(default)_<br>application/vnd.rmap-project.disco+rdf+turtle <br>application/rdf+xml <br>application/vnd.rmap-project.disco+rdf+xml <br>application/ld+json <br>application/vnd.rmap-project.disco+ld+json|

#### Request Body
none

## Response
#### Response Headers
Below are the possible response headers. Note: Memento-Datetime and Link datetimes will be formatted as follows: `Tue, 18 Nov 2015 15:02:01 GMT` 

| Header | Value |
|---------|------|
| Location* | https://{api-path}/discos/{disco-uri}|
| Memento-Datetime| {disco-created-date}|
| Link* | &#60;https://{api-path}/discos/{latest-version-uri}>;rel="latest-version memento"; datetime="{latest-version-created-date}"|
| Link | &#60;https://{api-path}/discos/{previous-version-uri}>;rel="predecessor-version memento";datetime="{previous-version-created-date}"
| Link | &#60;https://{api-path}/discos/{next-version-uri}>;rel="successor-version memento";datetime="{next-version-created-date}"|
| Link* | &#60;https://{api-path}/discos/{disco-uri}/events>;rel="http://www.w3.org/ns/prov#has_provenance"|
| Link* | &#60;http://purl.org/ontology/rmap#{disco-status}>;rel="http://purl.org/ontology/rmap#hasStatus"|
| Link* | &#60;https://{api-path}/discos/{first-version-uri}/latest>;rel="original timegate"|
| Link* | &#60;https://{api-path}/discos/{first-version-uri}/timemap>;rel="timemap"|
|Content-Type* |_Depends on request Accept header:_<br>- text/vnd.rmap-project.disco+turtle  _(default)_<br>- text/vnd.rmap-project.disco+rdf+xml<br>- text/vnd.rmap-project.disco+ld+json |

\* Appear on every OK response.

#### Response Body
An RDF serialization of the RMap DiSCO requested. The serialization will depend on the `Accept` type defined in the Request Header. For more information on what a DiSCO looks like, view the [DiSCO Media Type document](disco-media-type.md). Also view the [example](#example) at the bottom of this page.

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 404| Not found |
| 406 | Not acceptable (if incorrect accept type defined) |
| 410| Gone (for deleted DiSCOs, full response header still shown) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1. RMap searches for the DiSCO ID provided in the URL path. 
2. If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header.
3.  If the DiSCO is found, RMap checks events against all versions of the DiSCO to determine its status.
4. If the DiSCO is Deleted or Tombstoned, an HTTP Gone response is returned 
5. If the DiSCO is not Deleted or Tombstoned, an RDF serialization (determined by the “Accept” parameter in the request) of the DiSCO is returned in the HTTP body, and the following information is in the response header:
	* The API URL for the DiSCO
	* Links to the next, previous, and latest versions of the DiSCO (where relevant) and a link to the Memento Timemap and Timegate
	* The current DiSCO status
	* A URL to the list of related events
7. If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i 
    --header "Accept:application/rdf+xml"
    https://test.rmap-hub.org/api/discos/rmap%3A9w0vt4bp3c
```
#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/discos/rmap%3A1c59zw43jh
Link: <http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus"
Link: <https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6>;rel="predecessor-version memento";datetime="Fri, 19 Jan 2018 23:45:58 GMT"
Link: <https://test.rmap-hub.org/api/discos/rmap%3A1c59zw43jh>;rel="latest-version memento";datetime="Fri, 19 Jan 2018 23:46:10 GMT"
Link: <https://test.rmap-hub.org/api/discos/rmap%3A1c59zw43jh/events>;rel="http://www.w3.org/ns/prov#has_provenance"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ajdfn2z3k6r/latest>;rel="original timegate"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ajdfn2z3k6r/timemap>;rel="timemap"
Memento-Datetime: Fri, 19 Jan 2018 23:46:10 GMT
Content-Type: application/vnd.rmap-project.disco+rdf+xml; version=1.0

<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
	xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#">

<rdf:Description rdf:about="rmap:1c59zw43jh">
	<rdf:type rdf:resource="http://purl.org/ontology/rmap#DiSCO"/>
	<creator xmlns="http://purl.org/dc/terms/" rdf:resource="https://rmap-hub.org/citation-harvester/1.0"/>
	<description xmlns="http://purl.org/dc/terms/">Citations DiSCO</description>
	<aggregates xmlns="http://www.openarchives.org/ore/terms/" rdf:resource="https://doi.org/10.5194/essd-7-137-2015"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.7289/v5n014g9">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/Dataset"/>
	<title xmlns="http://purl.org/dc/terms/">NOAA Climate Data Record (CDR) of Northern Hemisphere (NH) Snow Cover Extent (SCE), Version 1</title>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0093">
	<name xmlns="http://xmlns.com/foaf/0.1/">D. A. Robinson</name>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<creator xmlns="http://purl.org/dc/terms/" rdf:resource="https://orcid.org/0000-0002-1925-0092"/>
</rdf:Description>

<rdf:Description rdf:about="https://climate.rutgers.edu/snowcover/">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/Dataset"/>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0092">
	<rdf:type rdf:resource="http://xmlns.com/foaf/0.1/Person"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<cites xmlns="http://purl.org/spar/cito/" rdf:resource="https://doi.org/10.1029/2003gl017337"/>
	<title xmlns="http://purl.org/dc/terms/">A long-term Northern Hemisphere snow cover extent data record for climate studies and monitoring</title>
	<usesDataFrom xmlns="http://purl.org/spar/cito/" rdf:resource="https://climate.rutgers.edu/snowcover/"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.1029/2003gl017337">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0091">
	<name xmlns="http://xmlns.com/foaf/0.1/">T. W. Estilow</name>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<available xmlns="http://purl.org/dc/terms/">2015</available>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.1029/2012eO440006">
	<title xmlns="http://purl.org/dc/terms/">A maturity model for assessing the completeness of climate data records</title>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<cites xmlns="http://purl.org/spar/cito/" rdf:resource="https://doi.org/10.1029/2012eO440006"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-5-311-2013">
	<title xmlns="http://purl.org/dc/terms/">A long-term and reproducible passive microwave sea ice concentration data record for climate studies and monitoring</title>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.1029/2012eO440006">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<creator xmlns="http://purl.org/dc/terms/" rdf:resource="https://orcid.org/0000-0002-1925-0093"/>
	<cites xmlns="http://purl.org/spar/cito/" rdf:resource="https://doi.org/10.5194/essd-5-311-2013"/>
	<creator xmlns="http://purl.org/dc/terms/" rdf:resource="https://orcid.org/0000-0002-1925-0091"/>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0091">
	<rdf:type rdf:resource="http://xmlns.com/foaf/0.1/Person"/>
</rdf:Description>

<rdf:Description rdf:about="https://climate.rutgers.edu/snowcover/">
	<title xmlns="http://purl.org/dc/terms/">Rutgers University Climate Lab, Global Snow Lab: Snow Extent CDR</title>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0093">
	<rdf:type rdf:resource="http://xmlns.com/foaf/0.1/Person"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.1029/2003gl017337">
	<title xmlns="http://purl.org/dc/terms/">The role of snow cover in the warming of arctic permafrost</title>
</rdf:Description>

<rdf:Description rdf:about="https://orcid.org/0000-0002-1925-0092">
	<name xmlns="http://xmlns.com/foaf/0.1/">A. H. Young</name>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-7-137-2015">
	<usesDataFrom xmlns="http://purl.org/spar/cito/" rdf:resource="https://doi.org/10.7289/v5n014g9"/>
</rdf:Description>

<rdf:Description rdf:about="https://doi.org/10.5194/essd-5-311-2013">
	<rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
</rdf:Description>

</rdf:RDF>
```
