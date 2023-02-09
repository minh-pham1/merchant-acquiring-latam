---
tags: [Getting Started, Payments, Currency Conversion, Types, Dynamic Pricing]
---

# Conversão de moeda

Use nosso serviço de conversão de moeda para oferecer aos seus clientes a facilidade de pagar em diversas moedas, tipicamente na sua moeda local quando fazendo compras em outros países.

## Tipos de conversão de moedas

Nós proporcionamos 2 tipos de conversão de moeda, a primeira na qual você pede a melhor cotação e taxa do nosso provedor de serviços, que então vai determinar a taxa de conversão e preço (taxa de conversão DCC), e a segunda na qual você rceberá uma lista de moedas e preços. Você pode escolher certos preços e moedas para oferecer ao cliente, ou o cliente pode selecionar a moeda que ele quer realizar o pagamento (requisição de taxa de conversão dinâmica).

## Usando a conversão de moeda

Para usar nosso serviço de conversão de moeda, use o método POST para ``/exchange-rates`` afim de pedir o preço e a taxa de conversão dos nosso provedores de serviço. Assim como nossos outros serviços, esse recurso é polimórfico. Isso significa que você pode submeter uma requisição para saber como seguir com seu pedido.

| **Tipo de conversão de moeda**  | **requestType**                   | **Cenário de conversão de moeda**                                                                                                                                  |
|---------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Requisição de taxa de conversão | DCCExchangeRateRequest            | Use este requestType para pedir uma taxa de conversão de moeda e preço para uma transação específica, aplicável para o cliente que você está realizando a cotação. |
| Requisição de preço dinâmico    | DynamicPricingExchangeRateRequest | Use esse requestType para pedir a relação de moedas e taxas disponíveis para essa transação.                                                                       |

## Payloads

### Requisição de taxa de conversão

Para pedir uma taxa de conversão específica para um cliente, faça um POST para o recurso ```/exchange-rates``` de acordo ao exemplo abaixo:

```json
{
  "requestType": "DCCExchangeRateRequest",
  "baseAmount": "12.32",
  "bin": "411111"
}
```

O valor BIN é importante porque isso configura a taxa de conversão a ser entregue ao portador do cartão, habilitando a seleção de preço e taxa de conversão modelada para aquele cliente. O campo BIN é composto dos 6 primeiros dígitos do cartão do portador.

A resposta do recurso ```/exchange-rates``` será apresentada da seguinte forma:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0bd552c12342d3448-b-ea-1142-12938318-7",
  "requestTime": 1592912760,
  "exchangeRateDetails": {
    "inquiryRateId": 49150,
    "foreignCurrency": "NOK",
    "foreignAmount": 130.33,
    "exchangeRate": 10.2968,
    "dccOffered": "true",
    "exchangeRateSourceTimestamp": "2015-06-23T13:46:00.000+02:00",
    "expirationTimestamp": "2015-06-23T13:46:00.000+02:00",
    "marginRatePercentage": "3",
    "exchangeRateSourceName": "REUTERS WHOLESALE INTERBANK"
  }
}
```
Para usar essa taxa em uma transação, inclua o objeto ```currencyConversion``` na requisição de POST de ```/payments``` como a seguir, usando o campo ```inquiryRateId``` da resposta do POST ```/exchange-rates``` feito previamente. Use o valor e a moeda da resposta do POST ```/exchange-rates``` no objeto ```transactionAmount```, e ajuste o ```conversionType``` para "DCC".

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "130.33",
    "currency": "NOK"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "5424180279791732",
      "securityCode": "977",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    }
  }
  "currencyConversion": {
    "conversionType": "Dcc",
    "inquiryRateId": "49150",
    "dccApplied": true
  }
}
```

### Preço dinâmico

Para pedir todas as taxas e moedas disponíveis para sua transação, use uma requisição POST usando o requestType ```dynamicPricingExchangeRateRequest```.

```json
{
  "requestType": "DynamicPricingExchangeRateRequest",
  "baseAmount": "12.32",
  "foreignCurrency": "USD"
}
```

Com sucesso, a resposta será gerada como a seguir, incluindo o total e o código ISO de conversão de moeda para a moeda da qual o total da transação foi convertido.

```json
{
  "conversionType": "DynamicPricing",
  "inquiryRateId": "49151",
  "foreignCurrency": "978",
  "foreignAmount": "22.52"
}
```
Para usar essa taxa em uma transação, inclua o objeto ```currencyConversion``` na requisição de POST de ```/payments``` como a seguir, usando o campo ```inquiryRateId``` da resposta do POST ```/exchange-rates``` feito previamente. Use o valor e a moeda da resposta do POST ```/exchange-rates``` no objeto ```transactionAmount```, e ajuste o ```conversionType``` para ```DynamicPricing```.

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "22.52",
    "currency": "EUR"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "5424180279791732",
      "securityCode": "977",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    }
  }
  "currencyConversion": {
    "conversionType": "DynamicPricing",
    "inquiryRateId": "49151",
    "dccApplied": true
  }
}
```

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
- [Tipos de pagamento](?path=docs/portuguese/payments/3-3-tipos-pagamento.md)
- [Post-authorisation & Returns](?path=docs/portuguese/payments/3-4-post-auth.md)
- [3-D Secure](?path=docs/portuguese/payments/3-5-3d-secure.md)
- [Verificação de cartão](?path=docs/portuguese/payments/3-6-verificação-cartão.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
