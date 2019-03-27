# Resource API Path (/resources)

Available API calls for the `/resources` path are summarized here. To see the headers, response, parameters, and behavior, please click the link for the API call document.

| HTTP Method | API Path | Title | Description |
| ------ | -----|-----| ----- |
| OPTIONS | /resources| [Explain RMap Event Service](OPTIONS-resources.md) | Provides a link to the API documentation and a list of available HTTP verbs for the /resources path. |
| HEAD | /resources| [RMap Agent Event Status](HEAD-resources.md) | Provides a link to API documentation and a list of available HTTP verbs for the /resources path. |
| GET | /resources/{uri} | [Retrieve Triples Referencing Resource](GET-resources-uri.md) | Retrieves triples from DiSCO or Agent graphs whose subject or object match the Resource URI provided.|
| GET | /resources/{uri}/discos | [Retrieve RMap DiSCOs Containing Resource](GET-resources-uri-discos.md) | Retrieves a list of URIs for DiSCOs that contain at least one triple whose subject or object match the Resource URI provided..|
| GET | /resources/{uri}/agents | [Retrieve RMap Agents that Created Graph Referencing Resource](GET-resources-uri-agents.md) | Retrieves a list of URIs for RMap Agents that have generated a DiSCO or Agent containing at least one triple whose subject or object match the Resource URI provided.|
| GET | /resources/{uri}/events | [Retrieve Events Related to Resource](GET-resources-uri-events.md) | Retrieves a list of URIs for RMap Events that generated a DiSCO or Agent containing the Resource.|
