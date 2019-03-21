# Retrieve DiSCO Versions (same Agent) as  _Memento Timemap_
Retrieves list of DiSCO version API URLs that were created by the same RMap Agent in the  _Memento Timemap_ format.  For more information on Memento, visit the [Memento website](http://mementoweb.org/guide/).

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
GET /discos/{uri}/timemap
```
uri = HTTP encoded URI of DiSCO to get the versions of. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/timemap_

#### Request Headers
none
 
#### Request Body
none

## Response
#### Response Headers
| Header | Value |
|---------|------|
| Content-Type * | application/link-format |

\* Appears on every OK response.

#### Response Body
Example:
```
<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7/latest>;rel="original",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7/timemap>;rel="self";
    type="application/link-format",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18mddcw>;rel="memento latest-version";
    datetime="Wed, 29 Jul 2015 17:47:18 UTC",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7>;rel="memento";
    datetime="Fri, 24 Jul 2015 19:35:06 UTC",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18mdd8b>;rel="memento";
    datetime="Wed, 29 Jul 2015 17:40:24 UTC"
```

#### Possible Response Codes
| Code| Description |
|---------|------|
| 200| OK|
| 404| Not found |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1.  RMap searches for a DiSCO using the URI provided by the client.
2. If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header.
3. If a matching DiSCO is found, RMap notes the System Agent that created that DiSCO.
4. RMap finds all DiSCOs labelled as versions of the DiSCO submitted that were created by the same System Agent as the one submitted.
5. RMap formats the version list as a Memento timemap. To read more about the Timemap format, see the [Memento website](http://mementoweb.org/guide/).
6. If any errors result in this process not being completed successfully, the user will receive the appropriate error message
7. Otherwise, the client receives an OK response with a content type “application/link-format”
8. The body of the response contains a list of links formatted in the same way as the list of links in a Response Header. These links include:
	* URL and date for all DiSCO versions (“memento”s)
	* A link to the timemap page (“self”)
	* A link to the /discos/{uri}/latest path for the DiSCO

## Example

#### Request
```
curl -i https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7/timemap
```

#### Response
```
HTTP/1.1 200 OK
Content-Type: application/link-format

<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7/latest>;rel="original",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7/timemap>;rel="self";
    type="application/link-format",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18mddcw>;rel="memento latest-version";
    datetime="Wed, 29 Jul 2015 17:47:18 UTC",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18m7mr7>;rel="memento";
    datetime="Fri, 24 Jul 2015 19:35:06 UTC",
<https://test.rmap-hub.org/api/discos/rmap%3Armd18mdd8b>;rel="memento";
    datetime="Wed, 29 Jul 2015 17:40:24 UTC"
```
