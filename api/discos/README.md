# RMap DiSCO API Path (/discos)

Available API calls for the `/discos` path are summarized here. To see the headers, response, parameters, and behavior, please click the link for the API call document.

For more information about the RMap DiSCO object _format_, please visit the [DiSCO media type document](disco-media-type.md). 

| HTTP Method | API Path | Title | Description |
| ------ | -----|-----| ----- |
| OPTIONS | /discos | [Explain RMap DiSCO Service](OPTIONS-discos.md) | Provides a link to the API documentation and a list of available HTTP verbs for the /discos path. |
| HEAD | /discos | [RMap DiSCO Service Status](HEAD-discos.md) | Provides a link to API documentation and a list of available HTTP verbs for the /discos path. |
| POST | /discos | [Create an RMap DiSCO](POST-discos.md) | Creates a new RMap DiSCO. |
| HEAD | /discos/{uri} | [View RMap DiSCO Status](HEAD-discos-uri.md) | Retrieve status and version information for referenced DiSCO. |
| GET | /discos/{uri} | [Retrieve RMap DiSCO](GET-discos-uri.md) | Retrieve DiSCO. For versioning, this path is considered to be a DiSCO "Memento" - a snapshot of the DiSCO in time. |
| GET | /discos/{uri}/latest | [Retrieve Latest Version of DiSCO](GET-discos-uri-latest.md) | Returns redirect response to the path of the latest version of the DiSCO. |
| POST | /discos/{uri} | [Update an RMap DiSCO](POST-discos-uri.md) | Updates an existing DiSCO. Performs differently depending on the updater is the original creator of the DiSCO. |
| POST | /discos/{uri}/inactivate | [Inactivate an RMap DiSCO](POST-discos-uri-inactivate.md) | Sets the status of an existing DiSCO to inactive. Inactive DiSCOs are still publicly available for read access. |
| DELETE | /discos/{uri} | [Soft Delete ("tombstone") an RMap DiSCO](DELETE-discos-uri.md) | Sets the status of an existing DiSCO to tombstoned. Tombstoned DiSCOs are not publicly available for read access, but their data still exists in the RMap triplestore. |
| GET | /discos/{uri}/allversions | [Retrieve DiSCO Versions (any Agent)](GET-discos-uri-allversions.md) | Retrieves a list of DiSCO URIs that are versions and derivatives of the DiSCO referenced. |
| GET | /discos/{uri}/agentversions | [Retrieve DiSCO Versions (same Agent)](GET-discos-uri-agentversions.md) | Retrieves a list of DiSCO URIs that are versions of the DiSCO referenced and created by the same RMap Agent. |
| GET | /discos/{uri}/timemap | [Retrieve DiSCO versions (same Agent) as Memento Timemap](GET-discos-uri-timemap.md) | Retrieves list of DiSCO version API URLs that were created by the same RMap Agent in the Memento Timemap format. |
| GET | /discos/{uri}/events | [Retrieve DiSCO Events](GET-discos-uri-events.md) | Retrieves a list of Event URIs for Events that have affected the DiSCO referenced.|
