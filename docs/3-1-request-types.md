# Request Types

Different payment actions require different requestType values. The table below explains the situations in which you might want to use the different requestType values. The technical detail for each of these requestTypes is included in the BodyRequest section of the API explorer. All API calls are POST.

|Payment Type|	requestType|	Payment Scenario|
| ----------| ------------ | ---------------- |
|Single Card Payment|	PaymentCardSaleTransaction	|Use this requestType to execute a normal customer payment transaction with a credit or debit card.|
|Credit|	PaymentCardCreditTransaction	  |Use this requestType to execute an original credit payment transaction to a customer’s credit or debit card. This means you’ll refund this amount to the customer’s card without a reference to any previous Sale transaction|
|Pre-auth (taking a deposit)	|PaymentCardPreAuthTransaction|	Use this requestType to pre-authorise an amount against a card, for completion at a later point.|
|Payment with a token	|PaymentTokenSaleTransaction|	Use this requestType to execute a normal customer payment transaction with a token generated previously.|
|Token Refund	|PaymentTokenCreditTransaction|	Use this requestType to execute a return to a customer’s credit or debit card using a token generated previously.|
|Token pre-auth	|PaymentTokenPreAuthTransaction	|Use this requestType to pre-authorise an amount using a token, for completion at a later point.|
|SEPA Payments	|SepaSaleTransaction	|Use this requestType to take payment from a customer via SEPA.|
|Wallet Sale	|WalletSaleTransaction|	Use this requestType to execute a normal customer payment transaction with a wallet payment method.|
|Wallet Pre-Auth	|WalletPreAuthTransaction	|Use this requestType to execute a Pre-Authorisation using a Wallet Payment Method|

For Primary Transactions, the decision matrix for defining which requestType to use is laid out below:

![Request Type Decision Matrix!](/assets/images/decision-matrix.png "Request Type Decision Matrix")

The differences that are required in the JSON for each of these request types are explained through the rest of this guide.
