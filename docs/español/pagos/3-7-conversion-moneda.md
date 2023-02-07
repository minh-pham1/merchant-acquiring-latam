---
tags: [Getting Started, Payments, Currency Conversion, Types, Dynamic Pricing]
---

# Conversión de Moneda

Utilice nuestros servicios de conversión de divisas para ofrecer a sus clientes la posibilidad de pagar en diferentes divisas, normalmente en su propia divisa nacional cuando realicen pedidos desde el extranjero.

## Tipos de conversión de moneda

Ofrecemos 2 tipos de conversión de divisas, uno en el que solicitará la divisa y el tipo de cambio óptimos a nuestro proveedor de tipos de cambio, quien luego determinará en qué divisa se proporcionará un precio y un tipo de cambio (una solicitud de tipo de cambio de DCC), y otro en el que se le proporcionará una lista de monedas y precios. Luego puede elegir ciertos precios y monedas para presentar al cliente, o el cliente puede seleccionar la moneda en la que le gustaría pagar (una solicitud de tasa de cambio de precios dinámicos).

## Uso de la conversión de divisas

Para utilizar nuestros servicios de conversión de divisas, utilice el método POST para ``/exchange-rates`` para solicitar un precio y tipo de cambio de nuestros proveedores de tipo de cambio. Al igual que con nuestros otros servicios, este recurso es polimórfico. Esto significa que deberá enviar un tipo de solicitud para indicarle al recurso de tipos de cambio cómo tratar su solicitud.

| **Tipo de conversión de moneda** |            **requestType**           |                                                                       **Escenario de conversión de moneda**                                                                       |
| -------------------------------- | -------------------------------------| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Solicitud de tipo de cambio      | DCCExchangeRateRequest               | Use este tipo de solicitud para solicitar una moneda, un precio y una tasa de cambio para una transacción específica, aplicable al cliente para el que está solicitando una tasa. |
| Solicitud de precios dinámicos   | DynamicPricingExchangeRateRequest    | Use este tipo de solicitud para solicitar todas las monedas y tasas disponibles para una transacción.                                                                             |

## Payloads

### Solicitud de tipo de cambio

Para solicitar una tarifa específica para un cliente, use un método POST contra el recurso ```/exchange-rates``` según la carga útil de muestra a continuación:

```json
{
  "requestType": "DCCExchangeRateRequest",
  "baseAmount": "12.32",
  "bin": "411111"
}
```

El valor BIN es importante ya que le dice al proveedor de tipos de cambio la moneda local de la tarjeta del cliente, lo que les permite proporcionar una tasa y un precio adaptados a ese cliente. El valor BIN son los primeros 6 dígitos del número de tarjeta del cliente.

La respuesta del recurso ```/exchange-rates``` se presentará de la siguiente manera:

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

Para usar esta tasa en una transacción, incluya el objeto ```currencyConversion``` en la solicitud POST ```/pagos``` de la siguiente manera, usando ```inquiryRateId``` de la respuesta a ``` /exchange-rates``` Solicitud POST. Utilice el valor y la moneda de la respuesta POST ```/exchange-rates``` en el objeto ```transactionAmount```, y establezca ```conversionType``` en "DCC".

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

### Precio dinamico

Para solicitar todas las tasas y monedas disponibles para su transacción, realice una solicitud de método POST utilizando ```dynamicPricingExchangeRateRequest``` requestType.

```json
{
  "requestType": "DynamicPricingExchangeRateRequest",
  "baseAmount": "12.32",
  "foreignCurrency": "USD"
}
```

Si tiene éxito, se generará una respuesta de la siguiente manera, incluido el monto y el código de moneda ISO correspondiente a la moneda a la que se ha convertido el monto de la transacción.

```json
{
  "conversionType": "DynamicPricing",
  "inquiryRateId": "49151",
  "foreignCurrency": "978",
  "foreignAmount": "22.52"
}
```

Para usar esta tasa en una transacción, incluya el objeto ```currencyConversion``` en la solicitud POST ```/pagos``` de la siguiente manera, usando ```inquiryRateId``` de la respuesta a ``` /exchange-rates``` Solicitud POST. Utilice el valor y la moneda de la respuesta POST ```/exchange-rates``` en el objeto ```transactionAmount```, y establezca ```conversionType``` en "DynamicPricing".

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

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
