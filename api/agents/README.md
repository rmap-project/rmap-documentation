# RMap Agent API Path (/agents)

Available API calls for the `/agents` path are summarized here. To see the headers, response, parameters, and behavior, please click the link for the API call document.

For more information about the RMap Agent object _format_, please visit the [Agent media type document](agent-media-type.md). 

| HTTP Method | API Path | Title | Description |
| ------ | -----|-----| ----- |
| OPTIONS | /agents| [Explain RMap Agent Service](OPTIONS-agents.md) | Provides a link to the API documentation and a list of available HTTP verbs for the /agents path. |
| HEAD | /agents| [RMap Agent Service Status](HEAD-agents.md) | Provides a link to API documentation and a list of available HTTP verbs for the /agents path. |
| HEAD | /agents/{uri} | [View RMap Agent Status](HEAD-agents-uri.md) | Retrieve status information for referenced Agent. |
| GET | /agents/{uri} | [Retrieve RMap Agent](GET-agents-uri.md) | Retrieve `rmap:Agent` graph. |
| GET | /agents/{uri}/events | [Retrieve Events Initiated by Agent](GET-agents-uri-events.md) | Retrieves a list of Event URIs for Events that were initiated by the Agent.|
| GET | /agents/{uri}/discos | [Retrieve DiSCOs Created by Agent](GET-agents-uri-discos.md) | Retrieves a list of DiSCO URIs for DiSCOs that were created by the Agent referenced.|
