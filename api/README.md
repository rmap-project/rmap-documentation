
# API Documentation

_Note:  All documentation should be considered to be in-progress, working documents unless otherwise noted._

The documents in this section describe how to use the RMap API. For a more in-depth description of the RMap data model, view the Data Model documentation.  You can also download a complete summary of all API calls available.

## Live RMap instances

There are two publicly available instances of the RMap software:

#### Test 
_[https://test.rmap-hub.org](https://test.rmap-hub.org)_

The test site is intended for experimental data and test runs. Please use this as needed to try out your DiSCOs before adding them to the live beta version of the system.

#### Live Beta 
_[https://rmap-hub.org](https://rmap-hub.org)_

A live beta version of RMap is hosted by the Sheridan Libraries at Johns Hopkins University. It is open for use The intention is to roll any data added to this instance into a full production instance. As a beta, however, there are backups, but no preservation process for this data. 

## Read-only access
Each of the API paths documented below can be appended to the root RMap API path. Live APIs can be found at the following URLs:

Sandbox: https://test.rmap-hub.org/api

Live beta website: https://rmap-hub.org/api


Only functions that create data require authentication. So, for example, you can read a DiSCO using the following CURL command (pasting the URL part into your browser also works!):
```
curl -H "Accept: text/turtle" -X GET https://test.rmap-hub.org/api/discos/rmap%3Armd18n8xfs
```
Note: Visual representations of some paths are available through the website. If your API path matches the following pattern it can be easily toggled to the visual version by replacing /api/ with /app/ on the test site, or /api/ with /  on the live beta site:

Sandbox API path:
```
https://test.rmap-hub.org/api/{resources|discos|events|agents}/{http_encoded_resource_uri}
```

Sandbox visualization:
```
https://test.rmap-hub.org/app/{resources|discos|events|agents}/{http_encoded_resource_uri}
```

Live beta API path:
```
https://rmap-hub.org/api/{resources|discos|events|agents}/{http_encoded_resource_uri}
```

Live beta visualization:
```
https://rmap-hub.org/{resources|discos|events|agents}/{http_encoded_resource_uri}
```

Here are some sample visualizations that support this simple conversion between the API and GUI paths :
* https://test.rmap-hub.org/app/discos/rmap%3Ak98sf7mdwm
* https://test.rmap-hub.org/app/events/rmap%3Aq2bvq83shb
* https://test.rmap-hub.org/app/agents/rmap%3A0k6djhb7fp
* https://test.rmap-hub.org/app/resources/https%3A%2F%2Fdoi.org%2F10.7265%2Fn5b56gn3

## Write access
To create DiSCOs in RMap, you will need to generate an RMap Agent for yourself.  You can do this by logging into the RMap website using your Google, Twitter, or ORCID account.  Once you've logged in, you will need to follow the two steps on the Welcome page to generate working API keys.  Please note that all data pushed into RMap will be public. 

To create API keys for the test site, log into the following website and follow the steps: [https://test.rmap-hub.org](https://test.rmap-hub.org). The process is identical for [https://rmap-hub.org](https://rmap-hub.org).

Once you have create a key and downloaded the key file, a curl command to create a new DiSCO, for example, might look like this:

```
curl -u $(<rmap.key)
	-X POST
	-d @disco.rdf
	--header "Content-Type:application/rdf+xml"
	https://test.rmap-hub.org/api/discos
```

In this case:
* "rmap.key" is a simple text file containing {key}:{secret}.  You can download this file through the web GUI. 
* "disco.rdf" is a text file containing your DiSCO RDF.  The content type should match the type defined in the header.

In addition to the individual examples found in the API documentation and media types, some example DiSCOs can be found in the [RMap test data](https://github.com/rmap-project/rmap/tree/master/testdata/src/main/resources/discos). Note that some of these are deliberately faulty for testing.  discoA.jsonld, .rdf, or .ttl, and discoBv1-4.rdf are working examples.

## API paths
The documentation for the API paths is split according to the major sub-directories. Below is an overview of what each of these paths contain and links to the detailed documentation.

### /discos 
RMap DiSCOs are the focal component of the RMap data model. DiSCO is short for "Distributed Scholarly Compound Object".  They contain an aggregation of resources and assertions about those resources as a graph.  The DiSCOs API path supports management of, and access to DiSCOs in RMap.

[view documentation](discos/README.md)

### /agents
RMap Agents are people or organizations that can contribute to RMap.  Anyone can sign up to become an RMap Agent by registering through the web application.  

Agents are associated with each RMap DiSCO they create in RMap.  The Agents API path supports retrieval of Agent data including a list of DiSCOs created by specific Agents.

[view documentation](agents/README.md)

### /events
RMap Events capture information about changes to the RMap database.  They also inform the status of a DiSCO or Agent and include a link to the RMap Agent that triggered the Event.  The Event API supports retrieval of specific Event data.

[view documentation](events/README.md)

### /resources
Resources refer to rdf:Resources in the RMap triplestore.  They refer to any URI referenced as either the Subject or Object of an RDF triple. RMap DiSCOs and Agents are composed of triples.  The Resources API allows you to look at what RMap "knows" about a particular Resource.  For example, you may wish to know what triples in RMap contain a particular Resource URI, or what DiSCOs mention it.

[view documentation](resources/README.md)

### /stmts
Stmts refers to rdf:Statements in the RMap triplestore.  The stmts API allows you, for example, to look up a particular triple in RMap and see which Agents have asserted it.

[view documentation](stmts/README.md)

