---
tags: [Field Services Provider, Logistics, API, LATAM]
---

# Field Services Provider

Fiserv does not extend to its field services vendors access to its ticketing tool system (TMP), once it was not designed to elaborate routes, maintain mandatory documentation for ticket closure or cancellation. Fiserv expects its field services partners to utilize their internal platforms and integrate a specific set of controls to ensure visibility of the service orders assigned to the vendor and to ensure tracking of the assets from Fiserv or it’s associated financial institutions in possession of the field services provider.

There will be two ways in each such integration will take place, being the first one through APIs designed to 1. incorporate new tickets from Fiserv into vendor, 2. to exchange statuses updates from both ends, 3. to log service order completion or its cancellation based on a set of pre-defined choices, 4. to communicate receival of new terminals, accessories, etc.

Main APIs available here to be used by the field services provider will be:

|API|	API Owner|	API user|	Purpose|
|---|-------------|---------|-----------|
|New ticket|	Field Services provider|	Fiserv|	API to enable creation of Service Orders on vendor platform|
|Ticket update|	Field Services Provider|	Fiserv|	API to enable Service Orders update on vendor platform|
|Uninstall|	Fiserv|	Financial institution|	API to enable creation of Service Orders to withdrawal a terminal|
|Supply request|	Fiserv|	Financial institution|	API to enable creation of Service Orders for paper roll delivery|
|Re-schedule|	Fiserv|	Financial Institution and Field Services provider|	API to enable update on Service Orders (exclusively to postpone due date)|
|Order Update|	Fiserv|	Field Services provider|	API to enable updates on a Service Order assigned to a Field Services vendor|
|Maintenance / Replacement|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a terminal to be replaced|
|Maintenance / Change Technology|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a terminal with a different technology|
|Maintenance / Change Carrier|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a SimCard exchange|
|Install|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting installation of a new terminal|
|Cancel order|	Fiserv|	Financial institution and Field Services provider|	API to enable cancellation of a previously opened Service order|
|Ticket List|	Fiserv|	Financial institution|	API to enable query of the Service orders opened on behalf of a Merchant|
|Terminal List|	Fiserv	|Financial Institution|	API to enable query on the terminals assigned to a particular merchant|
|Product|	Fiserv|	Financial Institution|	API to enable confirm terminal model based on a serial number|
|Ticket List details|	Fiserv|	Financial institution and Field Services provider|	API to enable query Service order details based on a Service order number|

Pre-requisite for the APIs consumption is the acquisition of a private certificate, that will be address in further details in this content.

The second method will be via XML files exchange, once per day, in which Fiserv expect from its Field Services vendor to communicate every asset position change occurred in the previous day, incorporating 1. terminals or SimCards moving from centralized stock to advanced location and vice versa, 2. terminals or SimCards moving from advanced location to merchants and vice versa, 3. terminals or SimCards moving from internal triage to repair and vice versa.
