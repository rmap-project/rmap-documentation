# Retrieve Triples Referencing Resource
Retrieve triples from DiSCO or Agent graphs whose subject or object match the Resource URI provided.

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
GET /resources/{uri}
```
uri = HTTP encoded URI  of Resource to retrieve triples for

This path also supports the following querystring parameters:

| Parameter| Description |
|------|----|
| status<br> (choose one) | DiSCO status filter: <br>- all<br>- active (default)<br>- inactive| 
| from | Date from (UTC). Applied to DiSCO creation Event start date. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
|until | Date to (UTC). Applied to DiSCO creation Event start date. Format as _yyyyMMddHHmmss_ or _yyyyMMdd_ <br>e.g. 20150101120000|
|agents| Comma separate list of RMap:Agent URIs, this will filter the triples by the Agent that generated the graph they are contained in.|
| page | Page number as integer of greater than 0. Default is 1.|
| limit | Number of URIs to retrieve. Default is 200. If there are more triples than the limit, the system will automatically paginate and add `page=1` to your query string.

Example URL: _https://test.rmap-hub.org/api/resources/https%3A%2F%2Fdoi.org%2F10.7265%2Fn59p2ztg?agents=rmap:0k6djhb7fp_

#### Request Headers
| Header | Value |
|---------|------|
| Accept <br> (choose one) | - text/turtle _(default)_<br>- application/rdf+xml<br>- application/ld+json|
 
#### Request Body
none

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Link | &#60;https://{api-path}/resources/{resource-uri}?{params-for-next-page}>;rel="next"<br>_(only appears when paginating and not last page)_ |
| Link | &#60;https://{api-path}/resources/{resource-uri}?{params-for-prev-page}>;rel="previous" <br>_(only appears when paginating and not on first page)_ |
|Link| &#60;https://{api-path}/resources/{resource-uri}?{params-for-first-page}>;rel="first" <br>_(only appears when paginating and not on first page)_ |
| Content-Type * | _Depends on request Accept header:_<br>- text/turtle _(default)_<br>- application/rdf+xml<br>- application/ld+json| |

\* Appears on every OK response.

#### Response Body
Example (application/rdf+xml):
```
<ark:/27927/pgg3r5df1cp> <http://www.loc.gov/mods/modsrdf/v1#locationOfResource>
    <http://portico.org> ;
    <http://purl.org/spar/pso/holdsStatusInTime> <rmap:rmd18mddfx> .
 
<rmap:rmd18mddcw> <http://www.openarchives.org/ore/terms/aggregates> <ark:/27927/pgg3r5df1cp> .
 
<ark:/27927/pgg3r5df1cp> <http://purl.org/dc/terms/hasPart> <ark:/27927/pgg3r5cvnv2> ;
    <http://purl.org/dc/terms/isFormatOf> <http://dx.doi.org/10.1109/InPar.2012.6339604> ;
    <http://purl.org/dc/terms/hasPart> <ark:/27927/pgg3r5cvnzq> , <ark:/27927/pgg3r5dwc3q> .
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 303 | See other (provides a link to begin pagination if returned triples are over the limit) | 
| 404| Not found (if Resource is not referenced in an Agent or DiSCO in RMap |
| 406| Not acceptable (if incorrect Accept header defined) |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches the triplestore for triples within an Agent or DiSCO whose subject or object match the Resource URI provided, applying the filters specified in the parameters 
2. If no matching triples are found, an HTTP Not Found response is returned with a basic default header.
3. If matching triples are found and they are under the limit for how many triples can be displayed per page, they are serialized as RDF and returned as an OK response.
4. If there are more matching triples than the limit defines, a redirect response with a link to begin pagination is returned as a 303 response.
5. If any errors result in this process not being completed successfully, the user will receive the appropriate error message.

## Example

#### Request
```
curl -i https://test.rmap-hub.org/api/resources/https%3A%2F%2Fdoi.org%2F10.7265%2Fn59p2ztg?agents=rmap:0k6djhb7fp
```

#### Response
```
HTTP/1.1 200 OK
Content-Type: text/turtle;charset=UTF-8

<https://doi.org/10.7265/n59p2ztg> a <http://purl.org/spar/fabio/Dataset> ;
	<http://purl.org/dc/terms/isVersionOf> <https://doi.org/10.7265/n5b56gn3> ;
	<http://purl.org/dc/terms/title> "NOAA/NSIDC Climate Data Record of Passive Microwave Sea Ice Concentration, Version 3" , "NOAA/NSIDC Climate Data Record of Passive Microwave Sea Ice Concentration" ;
	<http://purl.org/dc/terms/contributor> <https://orcid.org/0000-0002-1825-0100> , <https://orcid.org/0000-0003-4808-4736> , <https://orcid.org/0000-0002-1825-0095> , <https://orcid.org/0000-0002-1825-0097> , <https://orcid.org/0000-0002-1825-0098> , <https://orcid.org/0000-0002-1825-0096> ;
	<http://purl.org/spar/datacite/hasDescription> <https://doi.org/10.5194/essd-5-311-2013> ;
	<http://purl.org/dc/terms/available> "2017" .

<rmap:ttdz08m546> <http://www.openarchives.org/ore/terms/aggregates> <https://doi.org/10.7265/n59p2ztg> .

<rmap:9w0vt4bp3c> <http://www.openarchives.org/ore/terms/aggregates> <https://doi.org/10.7265/n59p2ztg> .

<https://www1.ncdc.noaa.gov/pub/data/sds/cdr/CDRs/Sea_Ice_Concentration/SourceCode_01B-11.tar.gz> <http://www.w3.org/ns/prov#generated> <https://doi.org/10.7265/n59p2ztg> .

<https://doi.org/10.5194/os-13-77-2017> <http://purl.org/spar/cito/usesDataFrom> <https://doi.org/10.7265/n59p2ztg> .
```
If there are too many results to list on a single page, then the request will offer a new link to being pagination. The `until` parameter is added so that results are not added to the list during pagination, which would potentially change the page contents. Note that some clients will automatically follow the `see other` location:
```
HTTP/1.1 303 See other
Location: https://test.rmap-hub.org/api/resources/https%3A%2F%2Fdoi.org%2F10.7265%2Fn59p2ztg?until=20190326161347&agents=rmap:0k6djhb7fp&limit=5&page=1
Content-Type: text/turtle;charset=UTF-8

[no content]
```
If the client automatically redirects, the new `Location` will load with the `Content-Type` listed. Otherwise, the client can manually follow the Location address to begin pagination. At this point the response will be similar to the first simple example.
