---
tags: [Logistics, API, LATAM]
---

# Logistics

## Latam Merchant Logistics APIs

At the acquirer business, field services providers play an important role to enable business to run, by deploying new terminals, paper rolls, providing maintenance to broken terminals, exchanging SimCards carriers and removing equipment’s from merchants whenever required. To trigger a service order to an appropriate field services provider Fiserv made available a set of APIs, described at this document, which will be divided in two main sections, being the first one dedicated for the financial institutions to provide merchant support and the second part designed to assist a new service provider when integrating with Fiserv.

---

<!--
type: tab
titles: Financial Institution, Field Services Provider
-->

In different occasions a merchant might request services that will require an intervention from one of Fiserv certified Field Services provider and there are some ground rules that requires attention before learning how to use the APIs:
(i)	There must be an active merchant at the member level to be able to request a new terminal and the identification of such member must be known in advance,
(ii)	There must be an active terminal for the merchant selected at the member level to place a paper roll request and both identifications must be known before placing the request,
(iii)	There must be an active terminal for the merchant selected at the member level to place its withdrawal which means, you can’t, for seasonal periods, request a terminal to be installed at a future date and at the same time, request its withdrawal a few weeks / months later. The withdrawal service order can only be placed once the terminal is installed at the merchant location
(iv)	It is vital for the success of the service order that the merchant directions are confirmed with the business owner or representative before generating the request because they can’t be changed later.
(v)	Due to the integration method between Fiserv and our certified Field Services Provider, updates take place near real time, helping to provide accurate information
(vi)	To use the APIs described in this document, an enrollment process at this developer portal must be concluded. Such process will generate the credentials required to use as parameters during an API request.

The following diagram helps illustrate timings in which each mentioned API should be used.

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

Main APIs available here to be used by the Financial Institution will be:

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

<!--
type: tab
-->

Fiserv does not extend to its field services vendors access to its ticketing tool system (TMP), once it was not designed to elaborate routes, maintain mandatory documentation for ticket closure or cancellation. Fiserv expects its field services partners to utilize their internal platforms and integrate a specific set of controls to ensure visibility of the service orders assigned to the vendor and to ensure tracking of the assets from Fiserv or it’s associated financial institutions in possession of the field services provider.

There will be two ways in each such integration will take place, being the first one through APIs designed to (i) incorporate new tickets from Fiserv into vendor, (ii) to exchange statuses updates from both ends, (iii) to log service order completion or its cancellation based on a set of pre-defined choices, (iv) to communicate receival of new terminals, accessories, etc.

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
The second method will be via XML files exchange, once per day, in which Fiserv expect from its Field Services vendor to communicate every asset position change occurred in the previous day, incorporating (i) terminals or SimCards moving from centralized stock to advanced location and vice versa, (ii) terminals or SimCards moving from advanced location to merchants and vice versa, (iii) terminals or SimCards moving from internal triage to repair and vice versa.


<!-- type: tab-end -->

---
