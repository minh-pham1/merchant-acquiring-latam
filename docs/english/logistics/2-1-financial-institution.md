---
tags: [Financial Institution, Logistics, API, LATAM]
---

# Financial Institution

In different occasions a merchant might request services that will require an intervention from one of Fiserv certified Field Services provider and there are some ground rules that requires attention before learning how to use the APIs:

1. There must be an active merchant at the member level to be able to request a new terminal and the identification of such member must be known in advance,
2. There must be an active terminal for the merchant selected at the member level to place a paper roll request and both identifications must be known before placing the request,
3. There must be an active terminal for the merchant selected at the member level to place its withdrawal which means, you can’t, for seasonal periods, request a terminal to be installed at a future date and at the same time, request its withdrawal a few weeks / months later. The withdrawal service order can only be placed once the terminal is installed at the merchant location
4. It is vital for the success of the service order that the merchant directions are confirmed with the business owner or representative before generating the request because they can’t be changed later.
5. Due to the integration method between Fiserv and our certified Field Services Provider, updates take place near real time, helping to provide accurate information
6. To use the APIs described in this document, an enrollment process at this developer portal must be concluded. Such process will generate the credentials required to use as parameters during an API request.

The following diagram helps illustrate timings in which each mentioned API should be used.

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

Main APIs available here to be used by the Financial Institution will be:

|API|	API Owner|	API user|	Purpose|
|---|-------------|---------|-----------|
|New ticket|	Field Services Provider|	Fiserv|	API to enable creation of Service Orders on vendor platform|
|Ticket update|	Field Services Provider|	Fiserv|	API to enable Service Orders update on vendor platform|
|Uninstall|	Fiserv|	Financial institution|	API to enable creation of Service Orders to withdrawal a terminal|
|Supply request|	Fiserv|	Financial institution|	API to enable creation of Service Orders for paper roll delivery|
|Re-schedule|	Fiserv|	Financial Institution and Field Services Provider|	API to enable update on Service Orders (exclusively to postpone due date)|
|Order Update|	Fiserv|	Field Services provider|	API to enable updates on a Service Order assigned to a Field Services vendor|
|Maintenance / Replacement|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a terminal to be replaced|
|Maintenance / Change Technology|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a terminal with a different technology|
|Maintenance / Change Carrier|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting a SimCard exchange|
|Install|	Fiserv|	Financial institution|	API to enable creation of a Service order requesting installation of a new terminal|
|Cancel order|	Fiserv|	Financial institution and Field Services Provider|	API to enable cancellation of a previously opened Service order|
|Ticket List|	Fiserv|	Financial institution|	API to enable query of the Service orders opened on behalf of a Merchant|
|Terminal List|	Fiserv	|Financial Institution|	API to enable query on the terminals assigned to a particular merchant|
|Product|	Fiserv|	Financial Institution|	API to enable confirm terminal model based on a serial number|
|Ticket List details|	Fiserv|	Financial institution and Field Services Provider|	API to enable query Service order details based on a Service order number|

---

## See Also

- [Field Services Provider](?path=docs/english/logistics/2-2-field-services-provider.md )

---
