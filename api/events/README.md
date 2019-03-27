# RMap Event API Path (/events)

Available API calls for the `/events` path are summarized here. To see the headers, response, parameters, and behavior, please click the link for the API call document.

For more information about the RMap Event object _format_, please visit the [Event media type document](event-media-type.md). 

| HTTP Method | API Path | Title | Description |
| ------ | -----|-----| ----- |
| OPTIONS | /events| [Explain RMap Event Service](OPTIONS-events.md) | Provides a link to the API documentation and a list of available HTTP verbs for the /events path. |
| HEAD | /events| [RMap Agent Event Status](HEAD-events.md) | Provides a link to API documentation and a list of available HTTP verbs for the /events path. |
| GET | /events/{uri} | [Retrieve RMap Event](GET-events-uri.md) | Retrieve `rmap:Event` graph. |
| GET | /events/{uri}/rmapobjs| [Retrieve RMap Objects Affected by Event](GET-events-uri-rmapobjs.md) | Retrieves a list of RMap Object URIs affect by the Event e.g. through a creation, derivation or other kind of activity.|
| GET | /events/{uri}/discos | [Retrieve RMap DiSCOs Affected by Event](GET-events-uri-discos.md) | Retrieves a list of RMap DiSCOs affect by the Event e.g. through a creation, derivation or other kind of activity.|
| GET | /events/{uri}/agents | [Retrieve RMap Agents Affected by Event](GET-events-uri-agents.md) | Retrieves a list of RMap Agents affect by the Event e.g. through a creation, derivation or other kind of activity.|
