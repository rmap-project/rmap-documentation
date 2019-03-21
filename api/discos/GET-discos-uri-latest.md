# Retrieve Latest Version of DiSCO
By default this path offers a redirect to the latest version of the DiSCO that was created by the same RMap Agent. This path also behave as a Memento "timegate", which means providing an Accept-Datetime will redirect to the closest matching version. For more information on timegates visit the Memento website.

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
GET /discos/{uri}/latest
```
uri = HTTP encoded URI of DiSCO to retrieve. 

Example URL: _https://test.rmap-hub.org/api/discos/rmap%3A8w9ghx3tv6/latest_

#### Request Headers

| Header | Value |
|---------|------|
| Accept-DateTime | Date of version e.g. Tue, 18 Nov 2015 15:02:01 GMT |

Note that the `Accept` type is only used if the request is automatically forwarded to the target DiSCO.

#### Request Body
none

## Response
#### Response Headers
Below are the possible response headers. Note: Memento-Datetime and Link datetimes will be formatted as follows: `Tue, 18 Nov 2015 15:02:01 GMT` 

| Header | Value |
|---------|------|
| Location* | https://{api-path}/discos/{matching-version-disco-uri}|
| Vary* | Accept-Datetime|
| Link* | &#60;https://{api-path}/discos/{first-version-uri}/latest&#62;;rel="original timegate"|
| Link* | &#60;https://{api-path}/discos/{first-version-uri}/timemap&#62;;rel="timemap"|

\* Appear on every OK response.

#### Response Body
An RDF serialization of the RMap DiSCO requested. The serialization will depend on the `Content-Type` defined. For more information on what a DiSCO looks like, view the [DiSCO Media Type document](disco-media-type.md). Also view the [example](#example) at the bottom of this page.

#### Possible Response Codes

| Code| Description |
|---------|------|
| 302| Match found successfully, redirect |
| 400| Bad request (if Accept-Datetime wrong formatted) |
| 404| Not found |
| 500| Internal server error|
| 503| Service unavailable|

## Behavior
1. RMap searches for the DiSCO ID provided in the URL path. 
2. If no matching DiSCO is found, an HTTP Not Found response is returned with a basic default header.
3. If the DiSCO is found, RMap checks events against all versions of the DiSCO to determine its status.
4. If the DiSCO is found and no Accept-Datetime parameter is provided, RMap checks the Event details to determine which DiSCO is the latest version for the `rmap:Agent` that created the one submitted in the request.
5. If the DiSCO is found and an Accept-Datetime parameter is provided, this initiates a Memento timegate negotiation against all versions of the DiSCO created by the same `rmap:Agent`.
   * If the date is prior to the first version, the first DiSCO version is retrieved.
   * Otherwise, the most recent DiSCO version before the date is retrieved (this may be the last version if the date is later than the most recent).
6. A 302 redirect response is generated where the location is the URI of the DiSCO version that is appropriate to the request.
7. The following information is in the response header:
   * The path the the timegate (which will be the same as in the request)
   * The path to a timemap (see /api/discos/<uri>/timemap
   * “Vary: Accept-Datetime”
8. If any other errors result in this process not being completed successfully, the user will receive the appropriate error message 

## Example

#### Request
```
curl -i 
  --header "Accept-DateTime:Tue, 18 Nov 2018 15:02:01 GMT" 
  https://test.rmap-hub.org/api/discos/rmap%3Ajdfn2z3k6r/latest
```

#### Response
```
HTTP/1.1 302 Found
Location: https://test.rmap-hub.org/api/discos/rmap%3A1c59zw43jh
Vary: Accept-Datetime
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ajdfn2z3k6r/latest>;rel="original timegate"
Link: <https://test.rmap-hub.org/api/discos/rmap%3Ajdfn2z3k6r/timemap>;rel="timemap"

[no content]
```
