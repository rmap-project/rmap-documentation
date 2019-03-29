# RMap Event Media Type

This document describes the media types for version 1.0 of the the RMap Event resource.

* [General Description](#general-description)
* [Media Type](#media-type)
* [Required Namespaces](#required-namespaces)
* [Elements](#elements)
* [Example Events](#example-events)
* [RMap API Usage of this Media Type](#rmap-api-usage-of-this-media-type)

## General Description
Every change to the RMap triplestore is captured as an RMap Event. Events record the provenance of DiSCOs and Agents - all updates and status changes are recorded.  

## Media Type
<table>
<tr><td>type</td><td>application</td></tr>
<tr><td>Subtype</td><td>vnd.rmap-project.event+rdf+turtle<br>vnd.rmap-project.event+rdf+ld+json<br>vnd.rmap-project.event+rdf+xml</td></tr>
<tr><td>Parameters</td><td>version</td>
</table>

###  Example media type
To specify a specific version of the media representation in the GET request header:
```
Accept: application/vnd.rmap-project.event+rdf+xml; version=1.0
```
## Required Namespaces
The following namespaces are used in the Event model.

|Namespace |URI | Description |
|-------------|------|--------|
|rdf * |[http://www.w3.org/1999/02/22-rdf-syntax-ns#](http://www.w3.org/1999/02/22-rdf-syntax-ns) | Resource Description Framework. [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/). |
|rdfs * | [http://www.w3.org/2000/01/rdf-schema#](http://www.w3.org/2000/01/rdf-schema) | RDF Schema. A data modeling vocabulary for RDF data.  [RDF Schema](http://www.w3.org/TR/rdf-schema/).|
| rmap * | [http://purl.org/ontology/rmap#](http://purl.org/ontology/rmap) | Vocabulary to describe resources in RMap. |
| dcterms |[http://purl.org/dc/terms](http://purl.org/dc/terms/) | Dublin Core elements.  [DC Terms](http://dublincore.org/documents/dcmi-terms/). |
| prov * | [http://www.w3.org/ns/prov#](http://www.w3.org/ns/prov) | The PROV Ontology for provenance.  [PROV-O](http://www.w3.org/TR/prov-o/). |

\* _Used in all Events for required fields_

##  Elements

Note: the elements whose occurrence is "0*" can have zero or more occurrences in principle, but in the current implementation there is always 0 or 1 of each of these. 

| Element Name | Occurrence | Description
|-------------|--------------|---------|
| rdf:type | 1 | Required. Will always be rmap:Event |
| rmap:eventTargetType | 1 | The entity type that is the primary target of the change. Only two entities can be acted on directly: <br>- rmap:DiSCO<br>- rmap:Agent |
| rmap:eventType | 1 | The type of activity that generated the event: <br> - rmap:creation<br>-   rmap:update<br> - rmap:inactivation<br> - rmap:deletion<br> - rmap:tombstone<br> - rmap:replace
| prov:used | 0 or 1 | Optional URI of the API Key used for this event. If enabled through the GUI, this allows monitoring of activity per key rather than just rmap:Agent. |
| prov:wasAssociatedWith | 1 | System rmap:Agent that created the event. |
| prov:startedAtTime | 1 | DateTime event started | 
| prov:endedAtTime | 1 | DateTime event ended | 
| dcterms:description | 1 | Short description of the Event. Meant for human consumption.
| prov:generated | 0* | Points to URIs of all objects created by this event | 
| rmap:derivedObject | 0* | Points to URI of new version of object |
| rmap:inactivatedObject | 0* | Points to URI of object inactivated by this event |
| rmap:deletedObject | 0* | Points to URIs of all objects deleted by this event | 
| rmap:tombstonedObject | 0* | Points to URI of object tombstoned by this event | 
| rmap:sourceObject | 0* | Points to URI of object that a new object was derived from (used in DiSCO derivation only) |
| rmap:updatedObject | 0* | Points to URI of an object that has been altered via update (used in Agent update only) |
| rmap:lineageProgenitor | 0 or 1 | References the first version of the DiSCO in the current lineage (use for DiSCOs only) |

## Example Events
### Create DiSCO Event 
#### application/rdf+xml
```
<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
    xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
    xmlns:rmap="http://purl.org/ontology/rmap#"
    xmlns:dcterms="http://purl.org/dc/terms/"
    xmlns:prov="http://www.w3.org/ns/prov#">
 
    <rdf:Description rdf:about="ark:/29297/aksjdflkajfdlldf">
       <rdf:type rdf:resource="http://purl.org/ontology/rmap#Event"/>
       <rmap:eventTargetType rdf:resource="http://purl.org/ontology/rmap#DiSCO"/>
       <rmap:eventType rdf:resource="http://purl.org/ontology/rmap#Creation"/>
 
       <!--system agent-->
    <prov:wasAssociatedWith rdf:resource="ark:/29297/akdsfai23kdsd"/>
 
       <!-- optional key reference to track API key use -->
       <prov:used rdf:resource="http://rmap-hub.org/keyid/sfjksjfdkjdf"/> 
 
       <prov:startedAtTime>2015-01-07T12:00:01Z</prov:startedAtTime>
       <prov:endedAtTime>2015-01-07T12:00:12</prov:endedAtTime>
       <dcterms:description>A new DiSCO was created</dcterms:description>
 
       <!--disco created-->
       <prov:generated rdf:resource="ark:/29297/akjdfalkjf2urjfad"/>
       
       <rmap:lineageProgenitor rdf:resource="ark:/29297/akjdfalkjf2urjfad"/>
 
    </rdf:Description>
</rdf:RDF>
```

#### text/turtle
```
@prefix rmap: <http://purl.org/ontology/rmap#> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix dc: <http://purl.org/dc/terms/> .
 
<ark:/29297/aksjdflkajfdlldf>
  a <http://purl.org/ontology/rmap#Event> ;
  rmap:eventTargetType <http://purl.org/ontology/rmap#Disco> ;
  rmap:eventType <http://purl.org/ontology/rmap#created> ;
  prov:wasAssociatedWith <ark:/29297/akdsfai23kdsd> ;
 
  <!-- optional key reference to track API key use -->
  prov:used <http://rmap-hub.org/keyid/sfjksjfdkjdf> ;
 
  prov:startedAtTime "2015-01-07T12:00:01Z" ;
  prov:endedAtTime "2015-01-07T12:00:12" ;
  dc:description "A new DiSCO was created" ;
  prov:generated <ark:/29297/akjdfalkjf2urjfad> ;
  rmap:lineageProgenitor <ark:/29297/akjdfalkjf2urjfad> .
```

### Update DiSCO Event
#### text/turtle
```
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix dc: <http://purl.org/dc/terms/> .
 
<ark:/29297/aksjdflkajfdlldf>
  a <http://purl.org/ontology/rmap#Event> ;
  rmap:eventTargetType <http://purl.org/ontology/rmap#DiSCO> ;
  rmap:eventType <http://purl.org/ontology/rmap#Update> ;
  prov:wasAssociatedWith <ark:/29297/akdsfai23kdsd> ;
  prov:used <http://rmap-hub.org/keyid/sfjksjfdkjdf> ;
  prov:startedAtTime "2015-01-07T12:00:01Z" ;
  prov:endedAtTime "2015-01-07T12:00:12" ;
  dc:description "An updated version of a DiSCO was created" ;
 
  rmap:inactivatedObject <ark:/29297/sdfjkwjekrjk> ;
  rmap:derivedObject <ark:/29297/akjdfalkjf2urjfad> ;
  prov:generated <ark:/29297/akjdfalkjf2urjfad> .
  
  rmap:lineageProgenitor <ark:/29297/sdfjkwjekrjk> .
```
### Tombstone DiSCO Event
#### text/turtle
```
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rmap: <http://purl.org/ontology/rmap#> .
 
<ark:/29297/aksjdflkajfdlldf>
  a <http://purl.org/ontology/rmap#Event> ;
  rmap:eventTargetType <http://purl.org/ontology/rmap#DiSCO> ;
  rmap:eventType <http://purl.org/ontology/rmap#tombstone> ;
  dcterms:description "A DiSCO was tombstoned" ;
  prov:wasAssociatedWith <ark:/29297/akdsfai23kdsd> ;
  prov:used <http://rmap-hub.org/keyid/sfjksjfdkjdf> ;
  prov:endedAtTime "2015-01-07T12:00:12" ;
  prov:startedAtTime "2015-01-07T12:00:01Z" ;
 
  rmap:tombstonedObject <ark:/29297/akjdfalkjf2urjfad> .
  
  rmap:lineageProgenitor <ark:/29297/akjdfalkjf2urjfad> .
```
### Delete DiSCO Event
#### text/turtle
```
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rmap: <http://purl.org/ontology/rmap#> .
@prefix xml: <http://www.w3.org/XML/1998/namespace> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
 
<ark:/29297/aksjdflkajfdlldf> a <rmap:Event> ;
  rmap:eventTargetType <http://purl.org/ontology/rmap#DiSCO> ;
  rmap:eventType <http://purl.org/ontology/rmap#Deletion> ;
  dcterms:description "A DiSCO was deleted" ;
  prov:wasAssociatedWith <ark:/29297/akdsfai23kdsd> ;
  prov:used <http://rmap-hub.org/keyid/sfjksjfdkjdf> ;
  prov:endedAtTime "2015-01-07T12:00:12" ;
  prov:startedAtTime "2015-01-07T12:00:01Z" ;
 
  rmap:deletedObject <ark:/29297/akjdfalkjf2urjfad> .
  
  rmap:lineageProgenitor <ark:/29297/akjdfalkjf2urjfad> .
```
### Derive DiSCO Event
#### text/turtle
```
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix prov: <http://www.w3.org/ns/prov#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rmap: <http://purl.org/ontology/rmap#> .
@prefix xml: <http://www.w3.org/XML/1998/namespace> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
 
<ark:/29297/aksjdflkajfdlldf> a <rmap:Event> ;
  rmap:eventTargetType <http://purl.org/ontology/rmap#DiSCO> ;
  rmap:eventType <http://purl.org/ontology/rmap#Derivation> ;
  dcterms:description "An updated version of a DiSCO was created" ;
  prov:wasAssociatedWith <ark:/29297/akdsfai23kdsd> ;
  prov:used <http://rmap-hub.org/keyid/sfjksjfdkjdf> ;
  prov:endedAtTime "2015-01-07T12:00:12" ;
  prov:startedAtTime "2015-01-07T12:00:01Z" ;
 
  rmap:sourceObject <ark:/29297/akjdfalkjf2kjkjljkh> ;
  rmap:derivedObject <ark:/29297/akjdfalkjf2urjfad> ;
  prov:generated <ark:/29297/akjdfalkjf2urjfad> .
    
  rmap:lineageProgenitor <ark:/29297/akjdfalkjf2urjfad> .
```

## RMap API Usage of this Media Type

See the [full API documentation](README.md) for a detailed description of each API operation.
