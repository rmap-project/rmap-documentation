# RMap DiSCO Design Best Practices

_We welcome suggestions for enhancing or refining these practices. Please comment or contact us at rmap.project@gmail.com_

* [Purpose of Document](#purpose-of-document)
* [Selecting Ontologies](#selecting-ontologies)
* [Selecting and Connecting Identifiers](#selecting-and-connecting-identifiers)
* [Formatting URIs](#formatting-uris)
* [DiSCO Design](#disco-design)

### Purpose of Document

The content of RMap DiSCOs is at the discretion of the data creators. RMap does not currently apply any normalization, or inferencing to the DiSCOs provided. There are also very few required fields and no restrictions on the ontologies you can use to form the body of your DiSCO.

On the one hand this gives data providers the freedom to include exactly what they want to describe using ontologies appropriate to their subject domain, but it also makes it easy to miss opportunities to make connections between things or describe resources in a way that is easy for data users to parse.

This document aims to provide some basic best practices for creating DiSCOs that will connect well in the RMap graph and be fruitful to navigate for humans and computers alike.

### Selecting Ontologies

All ontologies are accepted in the "additional statements" section of an RMap DiSCO, but use of established ontologies with persistent URLs is strongly recommended. Some that we have found particularly useful for creating DiSCOs include the  [SPAR Ontologies](http://www.sparontologies.net/), [DCMI Terms](http://dublincore.org/documents/dcmi-terms/), and several of the [Library of Congress standards](https://www.loc.gov/librarians/standards). We have listed these and others on the [Useful Ontologies guide](useful-ontologies.md). Note that if you are making use of the visualizations, these attempt to assign meaningful icons to `rdf:types`, so types from more popular ontologies are most likely to be recognized and assigned icons. The current default list of recognized types can be found in  [github](https://github.com/rmap-project/rmap/blob/master/webapp/src/main/resources/typemappings.properties), and others can be added over time. We welcome other suggestions for both this default list of type configurations and the Useful Ontologies list.

### Selecting and Connecting Identifiers

RMap is URI agnostic - it will accept any kind of URI that you wish to use, but here are a few useful guidelines for selecting and connecting together identifiers.

*   Where available, commonly used unique persistent identifiers such as DOIs, ORCID IDs, RRIDs, ISNIs, ISBNs, ISSNs, ARKS, PURLs, or Handles are ideal as URIs. They should be formatted according to provider guidelines, or in the absence of full guidelines, using the suggestions in the "Formatting URIs" section below
-   Where there is more than one format of an identifier  **and** you wish to include both formats in your DiSCO (a common example is "doi:10.0000/pid1234" vs "https://doi.org/10.0000/pid1234"), connect the two identifiers using [`owl:sameAs`](https://www.w3.org/TR/owl-ref/#sameAs-def)  e.g. 
	```
	<https://pidexample.org/pid1234> <http://www.w3.org/2002/07/owl#sameAs> <pidexample:pid1234> .
	```
*   Where you have more than one  _type_  of identifier for an object that point to the same resource (e.g. a DOI and and ARK ID), use the most broadly recognized identifier as the primary one that connects to other objects and any additional metadata. In other words that identifier would be the one used as the "aggregated resource" link and as the subject of the triples that describe it. In this instance it is useful to also include the other identifiers in your graph as this increases the opportunity for connections to occur. Depending on the degree to which the identifiers are pointing to the same  _version_  of the resource, these secondary identifiers can be connected using:
    -   [`owl:sameAs`](https://www.w3.org/TR/owl-ref/#sameAs-def) or [`dcterms:identifier`](http://dublincore.org/documents/dcmi-terms/#terms-identifier) if it is referring to the exact same resource. [`owl:sameAs`](https://www.w3.org/TR/owl-ref/#sameAs-def) is a stronger assertion that the same thing is being connected. dcterms:identifier is useful for identifiers that are not URIs e.g.
	```
	<https://doi.org/10.0000/pid1234> <http://www.w3.org/2002/07/owl#sameAs> <ark:/00000/abc5678> .  
	or
	<https://doi.org/10.0000/pid1234> <http://purl.org/dc/terms/identifier> "A.21395893B" .
	```            
*   [`rdfs:seeAlso`](https://www.w3.org/TR/rdf-schema/#ch_seealso) is best used if you are connecting two variations or versions of the same resource, for example, connecting the DOI of an article to a PubMed Central copy of the article or a copy of the article in an open repository e.g. 
	```
	<https://doi.org/10.0000/pid1234> <http://www.w3.org/2000/01/rdf-schema#seeAlso> <https://someopenrepository.edu/articles/abc5678> .
	```            

### Formatting URIs

Small variations in the formatting of URIs such as changes in case, additional forward-slashes, switching between http and https can result in a missed opportunity for a connection to occur in RMap. Many of the commonly used persistent identifier providers publish style guidelines for formatting. In general,  **_unless the provider guidelines instruct you to do otherwise_**, please format URIs as follows:

*   use the URL version of a URI where available
*   use lower case identifiers unless the identity provider specifically  _requires_  mixed or upper case
    *   e.g. PID:1234/InPar → pid:1234/inpar
*   for URLs, a domain name should always be lower case. Some guidelines may specifically instruct you to make the rest of the path mixed or upper case, but where fully lower case paths are acceptable, then they should be used.
    *   e.g. https://PIDexample.org/PID1234 → https://pidexample.org/pid1234
        
*   use https instead of http where available
    
    *   e.g. http://pidexample.org/pid1234 → https://pidexample.org/pid1234
        
*   remove trailing forward slashes from URLs
    *   e.g. https://pidexample.org/pid1234/ → https://pidexample.org/pid1234 
        
*  remove non-essential or user-specific GET parameters, where possible and/or reasonable
    *   e.g. https://pidexample.org/pid1234?uid=0394kdj923jf → https://pidexample.org/pid1234
   

Some common persistent identifier examples:

| ID Type | Instructions | Example | Link |
|------|------|-------|------|
| DOI | Prefix with "https://doi.org/", convert to lower case URL, remove any trailing forward slash, http-encode any special characters after the final forward slash of the DOI. | https://doi.org/10.1234/abc123 | [DOI guidelines](https://www.crossref.org/display-guidelines/) |
| ORCID ID | Prefix with "https://orcid.org/", include dashes after every 4 digits of the ID, remove any trailing forward slash | https://orcid.org/0000-1234-5678-9101 | [ORCID ID guidelines](https://support.orcid.org/knowledgebase/articles/116780-structure-of-the-orcid-identifier) |
| ISBN | Prefix with "urn:isbn:", do not include hyphens, if an X is present at the end of the ISBN, it should remain upper case. <br><br>Some ISBNs have been assigned an "actionable ISBN" which uses the DOI system. DiSCO creators may prefer to identify and use the "ISBN-A" if available. | urn:isbn:0123456789 | [ISBN as URN](https://www.iana.org/assignments/urn-formal/isbn);<br>[ISBN-A factsheet](http://www.doi.org/factsheets/ISBN-A.html)|
| ISNI  | Prefix with "http://isni.org/isni/", followed by ISNI ID without dashes or spaces | http://isni.org/isni/00000123456789 | [ISNI guidelines](http://www.isni.org/how-isni-works#HowItWorks_LinkedData) |
| ISSN | Prefix with "https://issn.org/resource/ISSN/", include the hyphen | https://issn.org/resource/ISSN/1234-1234 | [ISSN linked data guide](http://www.issn.org/wp-content/uploads/2017/12/ISSN-LinkedDataApplicationProfile-v0_9.pdf) |

### DiSCO Design

We recommend the following practices for designing DiSCOs:

*   For each new resource (work, person, organization, location, grant etc) you define in your DiSCO, we recommend two properties:
    *   some kind of label, preferably using a popular ontology e.g. `rdfs:label`, `dcterms:title`, `foaf:name` etc.
    *   an `rdf:type` e.g. `dcmitype:Dataset`, `dcmitype:Text`, `fabio:JournalArticle`, `fabio:Chapter`, `foaf:Person`, `foaf:Organization` etc.

*   When defining two or more resources in the `ore:aggregates` property of a DiSCO, create some RDF that explains the relationship between them e.g. `<article_doi> <x:describes> <dataset_doi>`
*   Where a set of triples describing a Resource are used repeatedly, consider designing a separate DiSCO to hold this repetitive information rather than copying it to every DiSCO. General person, article, or organization information are examples.
*   Avoid using blank nodes where possible. If you have a URI for the resource you are describing, use it as the  _subject_  of the statements about it. If you do use a blank node because an appropriate identifier does not exist for the item you are describing, it will be replaced by a persistent URI minted within RMap. If appropriate, this URI can be referenced in other DiSCOs allowing you to expand on the description and/or avoid creating multiple identifiers for the same resource.
*   Which resources should be listed as the DiSCO's "aggregated resources" (`ore:aggregates` property) is open to some interpretation. In many cases, you may create a DiSCO that describes a single resource such as one article, one person, or one institution, and in these cases there could be one `ore:aggregates` URI listed. In other circumstances a body of work might consist of a project, multiple datasets, and some software, in which case you might list a project URI, the dataset URI, and the software URIs as `ore:aggregates`. Some API requests focus on which resources are related through these ore:aggregates lists, so it is worth considering which resources you would want to be seen as strongly connected in this way.


_As with all guides, we hope this information has been useful and welcome suggestions for improvements to the documentation. Please submit suggestions to rmap.project@gmail.com or add them to the [issues list](https://github.com/rmap-project/rmap-documentation/issues)._
