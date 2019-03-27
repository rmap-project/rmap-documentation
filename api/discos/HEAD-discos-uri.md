# View RMap DiSCO Status
Retrieve status information for referenced DiSCO. This includes version information.

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
HEAD /discos/{uri}
```
uri = HTTP encoded URI of DiSCO to retrieve. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq_

#### Request Headers
none

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

\* Appear on every OK response.

#### Response Body
none

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK |
| 404| Not found |
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
6. If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i -I HEAD https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq
```
#### Response
```
HTTP/1.1 200 OK
Location: https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq
Link: <http://purl.org/ontology/rmap#active>;rel="http://purl.org/ontology/rmap#hasStatus"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq>;rel="latest-version memento";datetime="Fri, 19 Jan 2018 23:31:31 UTC"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq/events>;rel="http://www.w3.org/ns/prov#has_provenance"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq/latest>;rel="original timegate"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ap8cz8w9xbq/timemap>;rel="timemap"
Memento-Datetime: Fri, 19 Jan 2018 23:31:31 UTC

[no content]
```
