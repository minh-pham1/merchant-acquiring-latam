# Prerequisite - Offers configuration

> Before you begin onboarding your merchants you’ll want to ensure you are boarded with Fiserv yourself - contact the Developer Portal team to start this journey.

## Offers and configuration

One part of getting you on board is the configuration of ‘offers’ and the setting of these offers is essential for the onboarding process to work. The configuration is created in another tool provided by Fiserv (Offer Management System). This is a place where all Alliance and Partner specific offers are maintained. The initial setup is normally done by Fiserv based on a preceding agreement with the Alliance or Partner. The access to this tool can also be granted to users of those Alliance and Partner so they can perform updates to existing configuration according to their business needs.

> All the information in subsequent sections assumes this precondition has been met.
<!-- theme: warning -->

There are two kinds of offers that can be set up:

|EQUIPMENT OFFERS	| PROCESSING/ACQUIRING OFFERS	|
|---------------|-----------------------------|
|Where a combination of the equipment is set that can be offered to merchants, the cost of that equipment and features it supports.|Where rates related to transaction processing and other account processing fees, such as a joining fee are set.|
|Ecommerce is also configured within the Equipment offer, it is not just restricted to physical equipment such as terminals or peripherals.|

1. Only one of either type of offer is required as minimum set up, dependant on your business needs.
2. Both can be configured and used together.
3. Offers can also be set so they are only applicable to a configured range of criteria.
4. Out of the box this criteria includes MCC, Average Transaction Value, Annual Turnover and Address options.

## Access

You need to have an **API key** linked with the Alliance or Partner you plan to onboard the merchants for.|

The API key must be provided as the value of the `appKey` header field of each API call.

Alliance specific values
Before you start constructing the json payload, please make sure you have the values for the following objects:

`allianceCode`
`partnerCode` (only in case you are going to integrate a Partner)
`accountType`
`accountTypeSysId`
`customFieldCode`

Values for those objects are always Alliance/Partner specific and need to be pre-agreed with Fiserv or obtained before you submit your first boarding requests. Whether you need to have all of the above listed values depends on the set of services you will activate for the merchants and on the type of the business that you are.

> Make sure you are also familiar with the basics of the access and offer management configuration is in place before submitting your first boarding requests.
<!-- theme: warning -->
