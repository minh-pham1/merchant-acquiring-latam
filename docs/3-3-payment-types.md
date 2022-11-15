# Payment Types

*This page details some of the common forms of payment which are referenced in many of our APIs.*

There are many different forms of 'payment instrument'; the item or mechanism a party uses to pay another party. The main categories for these within our APIs are:

- **Card** - a physical or digital card entity, these will sit under a 'card scheme' such as Visa and contain information like a card number and expiry date, the data required and stored in this case follows a specific set of rules
- **Cash** - the old fashioned way, very little data about the instrument can be stored in this case
- **Alternative** - an alternative form of payment, such as digital wallets like Google Pay or PayPal, or tokenized card data, the data stored here varies depending on the service used

## Card Brands

See the following table for some detail on the combined set of card brands / schemes referenced across our APIs. Note that not all APIs will necessarily support all card schemes.

|Our Code|	Name	|Description|
|--------|-------|------------|
|```VISA```	|Visa	|Most common card scheme supported almost everywhere|
|```MC```	|MasterCard	|Second most common card scheme supported almost everywhere|
|```MAESTRO```	|Maestro	|Brand of debit cards owned by Mastercard, will be phased out of use in [Europe starting in 2023](https://en.wikipedia.org/wiki/Maestro_(debit_card))|
|```DINERS```	|Diners Club	|Card brand owned by Discover|
|```CUP```	|China UnionPay	|National bank card network in China|
|```JCB```	|JCB	|Internation payment brand based in Japan, works with Discover and AMEX|
|```AMEX```	|American Express	|Closed-loop payment scheme|
|```PP```	|Polcard Prepaid	|A type of prepaid card used in Poland|
|```PRIV```	|Private Label	|A card that has been private labeled by another company, usually has another underlying scheme|
|```CARTBLANC```	|Carte Bancaire	|Local card network in France|
|```GIRO```	|Giro Card	|Local card network in Germany|
|```PAGO```	|Pago	|Local card network in Italy|
|```VPAY```	|vPay	|SEPA debit card, often co-branded with local networks like Giro in Germany|
|```RUPAY```|	RuPay	|Global payment network primarily based in India|

## Alternative Payment Methods

Examples of alternative payment methods (sometimes abbreviated to APM's) are as follows:

|Our Code	|Name	|Description|
|--------|-------|------------|
|```BLIK```	|blik	|Mobile / instant payment system used in Poland|
|```SEPADD```|	SEPA Direct Debit|	An instant payment solution following the SEPA Direct Debit mandate|
|```ELV```	|ELV (Elektronisches Lastschriftverfahren)	|Electronic direct debit payment method that is supported by banks in Germany|
|```POSTFIN```|	|Direct banking payment method	|
|```IDEAL```	|iDeal	|Online banking SEPA payment method|
|```SOFORT```	|Sofort	|Instance payment solution|
|```PAYPAL```	|PayPal	|Most widely used digital wallet|
|```PBL```	|Pay-by-link	M|ethod used for providing payment links to customers for invoicing|
|```VIPPS```	|Vipps	|Mobile payment method used in Norway|
|```EASYCASH```	|EasyCash	|Mobile / digital wallet|
|```MOBILEPAY```	|Mobile Pay	|Mobile / digital wallet|
|```TWINT```	|TWINT	|Mobile / digital wallet|
|```SWISH```	|Swish	|Mobile / digital wallet used in Sweden|
|```ALIPAY```	|AliPay	|Mobile / digital wallet based in China|
|```WECHATPAY```|	|Mobile / digital wallet based in China|
