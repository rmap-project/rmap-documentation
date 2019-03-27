# RMap DiSCO Media Type
This document describes the media type for version 1.0 of the the Distributed Scholarly Compound Object (DiSCO) resource. It introduces a set of conventions for constructing a valid DiSCO from RDF as well as the semantics of the classes and properties used.

* [General Description](#general-description)
* [Media Type](#media-type)
* [Required Namespaces](#required-namespaces)
* [Elements](#elements)
* [Example DiSCOs](#example-discos)
* [RMap API Usage of this Media Type](#rmap-api-usage-of-this-media-type)

## General Description
A DiSCO describes an aggregation of related scholarly resources using linked data. Scholarly resources in a DiSCO  can include both agents of scholarly works (e.g. researchers, publishers, funders) and the works themselves (e.g. dataset, software, article). Valid rmap:DiSCOs cannot be disjointed graphs. All DiSCO graphs have one root node of type rmap:DiSCO that only has outbound arcs.

## Media Type
<table>
<tr><td>type</td><td>application</td></tr>
<tr><td>Subtype</td><td>vnd.rmap-project.disco+rdf+turtle<br>vnd.rmap-project.disco+rdf+ld+json<br>vnd.rmap-project.disco+rdf+xml</td></tr>
<tr><td>Parameters</td><td>version</td>
</table>

###  Example media types
To specify a specific version of the media representation in the GET request header:
`Accept: application/vnd.rmap-project.disco+rdf_xml;version=1.0`

To specify the default/latest media representation in a POST request:
`Content-Type: application/vnd.rmap-project.disco+rdf+xml`
or 
`Content-Type: application/rdf+xml`

## Required Namespaces
The first 3 namespaces in the following are used for the required fields of an RMap DiSCO, the other two are used for optional fields. 
|Namespace |URI | Description |
|-------------|------|--------|
|rdf * |[http://www.w3.org/1999/02/22-rdf-syntax-ns#](http://www.w3.org/1999/02/22-rdf-syntax-ns) | Resource Description Framework. [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/). |
| rmap * | [http://purl.org/ontology/rmap#](http://purl.org/ontology/rmap) | Vocabulary to describe resources in RMap. |
| ore * | [http://www.openarchives.org/ore/terms/](http://www.openarchives.org/ore/terms/) | OAI-ORE elements ([http://www.openarchives.org/ore/](http://www.openarchives.org/ore/)) |
| dcterms | [http://purl.org/dc/terms](http://purl.org/dc/terms/) | Dublin Core elements. [DC Terms](http://dublincore.org/documents/dcmi-terms/). |
| prov | [http://www.w3.org/ns/prov#](http://www.w3.org/ns/prov) | Provenance ontology |

\* _Used in all DiSCOs for required fields_

##  Elements
| Element Name | Occurrence | Description
|-------------|--------------|---------|
| rdf:type | 1 | Required. Will always be rmap:DiSCO |
| ore:aggregates | 1+ | Property of a DiSCO. Used to specify an aggregated resource. The aggregated resource MUST have a URI. (revenue model oppty: naming service for resources w/o a URI) |
| dcterms:creator | 0-1 | This is distinct from the service/system agent that uses the API to create the DiSCO. This is the individual(s) or organization that assembled the set of relationships and asserted them as a DiSCO. Allows ownership of DiSCOs to be more specific than the system that created them. |
| dcterms:description | 0-1 | Optional short description of the DiSCO. Meant for human consumption. | 
| prov:wasGeneratedBy | 0-1 | Optional link to a document describing the harvester. This document can be generated in a DiSCO and referenced as provenance information |
| Additional RDF about each ore:aggregates resource | 0* | Additional RDF data about each resource listed in ore:aggregates can be provided. There are no limitations on what triples are used to describe the aggregated resources, provided it is (1) valid RDF (2) forms a connected graph with the aggregated resources. <br> <br> Though not required, it is recommended that each description of the aggregated resource include, where possible, a type, title, and its relationship to the other aggregated resources. |

## Example DiSCOs
### application/rdf+xml
```
<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
    xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    xmlns:rmap="http://purl.org/ontology/rmap#"
    xmlns:dc="http://purl.org/dc/elements/1.1/"
    xmlns:dcterms="http://purl.org/dc/terms/"
    xmlns:ore="http://www.openarchives.org/ore/terms/"
    xmlns:foaf="http://xmlns.com/foaf/0.1/"
    xmlns:prov="http://www.w3.org/ns/prov#">
     
    <rdf:Description rdf:about="ark:/00000/03j9uf983h8fh8s">
       <rdf:type rdf:resource="http://purl.org/ontology/rmap#DiSCO"/>
    <dcterms:creator rdf:resource="http://orcid.org/0000-0000-0000-0000"/>
       <dcterms:description>
            This is an example DiSCO aggregating different file formats for an
            article on IEEE Xplore as well as multimedia content related to the
            article.
       </dcterms:description>
       <!-- Optional provenance reference - can also create a provenance document in a DiSCO
       and reference it here-->
       <prov:wasGeneratedBy rdf:resource="ark:/00000/uriOfProvDesc"/>
       <!-- Aggregation section -->
       <ore:aggregates rdf:resource="http://dx.doi.org/10.1109/ACCESS.2014.2332453"/>
       <ore:aggregates rdf:resource="http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip"/>
    </rdf:Description>
 
    <!-- Statements describing the first ore:aggregates resources-->
    <rdf:Description rdf:about="http://dx.doi.org/10.1109/ACCESS.2014.2332453">
        <rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
    <dc:title>Toward Scalable Systems for Big Data Analytics: A Technology Tutorial</dc:title>
    <dc:creator>Tat-Seng Chua</dc:creator>
    <dc:creator>Xuelong Li</dc:creator>
    <dc:creator>Yonggang Wen</dc:creator>
    <dc:subject>data acquisition</dc:subject>
    <dc:subject>Big data analytics</dc:subject>
    <dc:subject>Hadoop</dc:subject>
    </rdf:Description>
 
    <!-- Statements describing the second ore:aggregates resources-->
    <rdf:Description rdf:about="http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip">
    <dc:hasPart rdf:resource="http://ieeexplore.ieee.org/123/html/123-mm.zip#README.docx"/>
    <dc:hasPart rdf:resource="http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi"/>
    <dc:format>application/zip</dc:format>
    </rdf:Description>
 
    <!-- More statements to describe some of the components in the graph-->
    <rdf:Description rdf:about="http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi">
    <dc:extent>194KB</dc:extent>
    <dc:format>video/x-msvideo</dc:format>
    </rdf:Description>
</rdf:RDF>
```
### text/turtle
```
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix rmap: <http://purl.org/ontology/rmap#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix ore: <http://www.openarchives.org/ore/terms/>.
@prefix prov: <http://www.w3.org/ns/prov#>.
 
<ark:/00000/03j9uf983h8fh8s>
  a <http://purl.org/ontology/rmap#DiSCO> ;
  dcterms:creator <http://orcid.org/0000-0000-0000-0000> ;
  dcterms:description "This is an example DiSCO aggregating different file formats for an
            article on IEEE Xplore as well as multimedia content related to the
            article." ;      
  <!-- Optional provenance reference-->
  prov:wasGeneratedBy <ark:/00000/uriOfProvDesc"> ;
  <!-- Aggregation section -->
  ore:aggregates <http://dx.doi.org/10.1109/ACCESS.2014.2332453>,
                  <http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip> .
 
<!-- Statements describing the second ore:aggregates resources-->
<http://dx.doi.org/10.1109/ACCESS.2014.2332453>
  a <http://purl.org/spar/fabio/JournalArticle> ;
  dc:title "Toward Scalable Systems for Big Data Analytics: A Technology Tutorial" ;
  dc:creator "Tat-Seng Chua", "Xuelong Li", "Yonggang Wen" ;
  dc:subject "data acquisition", "Big data analytics", "Hadoop" .
 
<!-- Statements describing the second ore:aggregates resources-->
<http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip>
  dc:hasPart <http://ieeexplore.ieee.org/123/html/123-mm.zip#README.docx>,
             <http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi> ;
  dc:format "application/zip" .
 
<!-- More statements to describe some of the components in the graph-->
<http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi>
  dc:extent "194KB" ;
  dc:format "video/x-msvideo" .
```

## RMap API Usage of this Media Type
See the [full API documentation](README.md) for a detailed description of each API operation.

### Link Headers and Relations
The following link relations may be returned in response to a DiSCO request:
| Relation | Description |
|--------|----------|
| predecessor-version | The resource indicated by this link represents a DiSCO that is the immediate predecessor of the DiSCO. See:  [https://tools.ietf.org/html/rfc5829#section-3.5](https://tools.ietf.org/html/rfc5829#section-3.5) |
| successor-version| The resource indicated by this link represents a DiSCO that is the immediate successor of the DiSCO under consideration. See:  [https://tools.ietf.org/html/rfc5829#section-3.6](https://tools.ietf.org/html/rfc5829#section-3.6) |
| latest-version| The resource indicated by this link represents the latest version of the DiSCO See:  [https://tools.ietf.org/html/rfc5829#section-3.2](https://tools.ietf.org/html/rfc5829#section-3.2)
|[http://www.w3.org/ns/prov#wasGeneratedBy](http://www.w3.org/ns/prov#wasGeneratedBy) | The resource indicated by this link represents the rmap:Event that generated the DiSCO |
| [http://www.w3.org/ns/prov#has_provenance](http://www.w3.org/ns/prov#has_provenance) | The resource indicated by this link represents a list of rmap:Events that affected the DiSCO |
| [http://purl.org/ontology/rmap#hasStatus](http://purl.org/ontology/rmap#hasStatus) | The resource indicated by this link defines the current status of the DiSCO|

### Example API Interactions
#### Create a DiSCO via POST HTTP Request
##### application/rdf+xml
```
POST http://rmap-hub.org/api/discos HTTP/1.1
Content-type: application/rdf+xml
  
<!--body-->
<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
    xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    xmlns:rmap="http://purl.org/ontology/rmap#"
    xmlns:ore="http://www.openarchives.org/ore/terms/"
    xmlns:dc="http://purl.org/dc/elements/1.1/"
    xmlns:dcterms="http://purl.org/dc/terms/"
    xmlns:foaf="http://xmlns.com/foaf/0.1/">
     
    <rmap:DiSCO>
    <dcterms:creator rdf:resource="http://orcid.org/0000-0000-0000-0000"/>
       <dcterms:description>
            This is an example DiSCO aggregating different file formats for an
            article on IEEE Xplore as well as multimedia content related to the
            article.
       </dcterms:description>
       <prov:wasGeneratedBy rdf:resource="ark:/00000/uriOfProvDesc"/>
       <ore:aggregates rdf:resource="http://dx.doi.org/10.1109/ACCESS.2014.2332453"/>
       <ore:aggregates rdf:resource="http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip"/>
    </rmap:DiSCO>
 
    <rdf:Description rdf:about="http://dx.doi.org/10.1109/ACCESS.2014.2332453">
        <rdf:type rdf:resource="http://purl.org/spar/fabio/JournalArticle"/>
        <dc:title>Toward Scalable Systems for Big Data Analytics:A Technology Tutorial</dc:title>
        <dc:creator>Tat-Seng Chua</dc:creator>
        <dc:creator>Xuelong Li</dc:creator>
        <dc:creator>Yonggang Wen</dc:creator>
        <dc:subject>data acquisition</dc:subject>
        <dc:subject>Big data analytics</dc:subject>
        <dc:subject>Hadoop</dc:subject>
    </rdf:Description>
 
    <rdf:Description rdf:about="http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip">
        <dc:hasPart rdf:resource="http://ieeexplore.ieee.org/123/html/123-mm.zip#README.docx"/>
        <dc:hasPart rdf:resource="http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi"/>
        <dc:format>application/zip</dc:format>
    </rdf:Description>
 
    <rdf:Description rdf:about="http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi">
    <dc:extent>194KB</dc:extent>
    <dc:format>video/x-msvideo</dc:format>
    </rdf:Description>
</rdf:RDF>
```
##### text/turtle
```
POST http://rmap-hub.org/api/discos HTTP/1.1
Content-Type: text/turtle
  
<!--body-->
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix rmap: <http://purl.org/ontology/rmap#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix ore: <http://www.openarchives.org/ore/terms/>.
@prefix prov: <http://www.w3.org/ns/prov#>.
 
<>
  a <http://purl.org/ontology/rmap#DiSCO> ;
  dcterms:creator <http://orcid.org/0000-0000-0000-0000> ;
  dcterms:description "This is an example DiSCO aggregating different file formats for an
            article on IEEE Xplore as well as multimedia content related to the
            article." ;      
  <!-- Optional provenance reference-->
  prov:wasGeneratedBy <ark:/00000/uriOfProvDesc"> ;
  <!-- Aggregation section -->
  ore:aggregates <http://dx.doi.org/10.1109/ACCESS.2014.2332453>,
                  <http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip> .
 
<!-- Statements describing the second ore:aggregates resources-->
<http://dx.doi.org/10.1109/ACCESS.2014.2332453>
  a <http://purl.org/spar/fabio/JournalArticle> ;
  dc:title "Toward Scalable Systems for Big Data Analytics: A Technology Tutorial" ;
  dc:creator "Tat-Seng Chua", "Xuelong Li", "Yonggang Wen" ;
  dc:subject "data acquisition", "Big data analytics", "Hadoop" .
 
<!-- Statements describing the second ore:aggregates resources-->
<http://ieeexplore.ieee.org/ielx7/123/html/6842585-mm.zip>
  dc:hasPart <http://ieeexplore.ieee.org/123/html/123-mm.zip#README.docx>,
             <http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi> ;
  dc:format "application/zip" .
 
<!-- More statements to describe some of the components in the graph-->
<http://ieeexplore.ieee.org/123/html/123-mm.zip#dataintro.avi>
  dc:extent "194KB" ;
  dc:format "video/x-msvideo" .
```
#### Successful Create Response from Server 
```
HTTP/1.1 201 Created
Date: Tue, 18 Nov 2014 11:11:11 GMT
Content-length: nnn
Content-type: application/vnd.rmap-project.disco+rdf+xml; version=1.0
Location: http://rmap-hub.org/api/discos/ark%3A%2F00000%2Fdf8wekijfdslaks
Link: <http://rmap-hub.org/api/events/ark%3A%2F00000%2F2ekdfk32kdj>;
           rel="http://www.w3.org/ns/prov#wasGeneratedBy"
 
<!--body-->
ark:/00000/df8wekijfdslaks
```

#### Read DiSCO GET Request
 ```
GET /discos/ark%3A%2F00000%2F03j9uf983h8fh8s HTTP/1.1
Accept: application/vnd.rmap-project.disco+rdf+xml
```
#### Successful Read DiSCO Response (inactive DiSCO)
```
HTTP/1.1 200 OK
Date: Tue, 18 Nov 2014 11:11:12 GMT
Content-length: nnn
Content-type: application/vnd.rmap-project.disco+rdf+xml; version=1.0
Location: http://rmap-hub.org/api/discos/ark%3A%2F00000%2Fdf8wekijfdsl
Link: <http://rmap-hub.org/api/discos/ark%3A%2F00000%2Fdkf9823ikd>;rel=“latest-version”,
      <http://rmap-hub.org/api/discos/ark%3A%2F00000%2Fkj234k23ds>;rel=“predecessor-version”,
      <http://rmap-hub.org/api/discos/ark%3A%2F00000%2Fdkf9823ikd>;rel=“successor-version”,
 
      <http://rmap-hub.org/api/discos/ark%3A%2F00000%2Faskdfskdfj/events>;
           rel=“http://www.w3.org/ns/prov#has_provenance”,
 
      <http://purl.org/ontology/rmap#inactive>;
            rel=“http://purl.org/ontology/rmap#hasStatus”
 
<!--body-->
<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
    xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    xmlns:rmap="http://purl.org/ontology/rmap#"
    xmlns:ore="http://www.openarchives.org/ore/terms/"
    xmlns:dcterms="http://purl.org/dc/terms/">
     
    <rdf:Description rdf:about="ark:/00000/03j9uf983h8fh8s">
       <rdf:type rdf:resource="http://purl.org/ontology/rmap#DiSCO"/>
     <dcterms:creator rdf:resource="http://orcid.org/0000-0000-0000-0000"/>
        <dcterms:description>
            This is an example DiSCO aggregating different file formats for an
            article on IEEE Xplore as well as multimedia content related to the
            article. With some added love.
        </dcterms:description>
         
        <!-- Aggregation section -->
        <ore:aggregates rdf:resource="http://dx.doi.org/10.1109/ACCESS.2014.2332453"/>
        <ore:aggregates rdf:resource="http://ieeexplore.ieee.org/ielx7/6287639/6705689/6842585/html/mm/6842585-mm.zip"/>
        <ore:aggregates rdf:resource="http://example.org/awesome-data-set"/>
 
    </rmap:Description>
</rdf:RDF>  
```