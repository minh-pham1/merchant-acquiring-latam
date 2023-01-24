---
tags: [Getting Started, Payments, Payment Types, Card Brands]
---

# Formas de pagos

*Esta página detalla algunas de las formas de pago comunes a las que se hace referencia en muchas de nuestras APIs.*

Hay muchas formas diferentes de 'instrumentos de pago'; el elemento o mecanismo que utiliza una parte para pagar a otra parte. Las categorías principales para estos dentro de nuestras API son:

- **Tarjeta** - una entidad de tarjeta física o digital, se ubicarán bajo un "esquema de tarjeta" como Visa y contiene información como un número de tarjeta y fecha de vencimiento, los datos requeridos y almacenados en este caso siguen un conjunto específico de reglas.
- **Efectivo** - a la antigua usanza, en este caso se pueden almacenar muy pocos datos sobre el instrumento.
- **Alternativo** - una forma de pago alternativa, como billeteras digitales como Google Pay o PayPal, o datos de tarjetas tokenizados, los datos almacenados aquí varían según el servicio utilizado.

## Marcas de tarjetas

Consulte la siguiente tabla para obtener algunos detalles sobre el conjunto combinado de marcas/esquemas de tarjetas a los que se hace referencia en nuestras API. Tenga en cuenta que no todas las API necesariamente admitirán todos los esquemas de tarjetas.

|Nuestro Código   | Nombre           | Descripción                                                                                                                                                            |
|-----------------|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```VISA```      | Visa             | El esquema de tarjeta más común es compatible en casi todas partes                                                                                                     |
| ```MC```        | MasterCard       | El segundo esquema de tarjeta más común es compatible en casi todas partes                                                                                             |
| ```MAESTRO```   | Maestro          | La marca de tarjetas de débito propiedad de Mastercard, dejará de usarse gradualmente en [Europe starting in 2023](https://en.wikipedia.org/wiki/Maestro_(debit_card)) |
| ```DINERS```    | Diners Club      | Marca de tarjeta propiedad de Discover                                                                                                                                 |
| ```CUP```       | China UnionPay   | Red nacional de tarjetas bancarias en China                                                                                                                            |
| ```JCB```       | JCB              | Marca de pago internacional con sede en Japón, trabaja con Discover y AMEX                                                                                             |
| ```AMEX```      | American Express | Esquema de pago de ciclo cerrado                                                                                                                                       |
| ```PP```        | Polcard Prepaid  | Un tipo de tarjeta prepago utilizada en Polonia                                                                                                                        |
| ```PRIV```      | Private Label    | Una tarjeta que ha sido etiquetada de forma privada por otra compañía, por lo general tiene otro esquema subyacente                                                    |
| ```CARTBLANC``` | Carte Bancaire   | Red de tarjetas locales en Francia                                                                                                                                     |
| ```GIRO```      | Giro Card        | Red de tarjetas locales en Alemania                                                                                                                                    |
| ```PAGO```      | Pago             | Red de tarjetas locales en Italia                                                                                                                                      |
| ```VPAY```      | vPay             | Tarjeta de débito SEPA, a menudo de marca compartida con redes locales como Giro en Alemania                                                                           |
| ```RUPAY```     | RuPay            | Red de pago global con sede principalmente en India                                                                                                                    |

## Alternative Payment Methods

Ejemplos de métodos de pago alternativos (a veces abreviados como APM) son los siguientes:

| Our Code        | Name                                      | Description                                                                           |
|-----------------|-------------------------------------------|---------------------------------------------------------------------------------------|
| ```BLIK```      | blik                                      | Sistema de pago móvil/instantánea utilizado en Polonia                                |
| ```SEPADD```    | SEPA Direct Debit                         | Una solución de pago instantáneo siguiendo el mandato de Débito Directo SEPA          |
| ```ELV```       | ELV (Elektronisches Lastschriftverfahren) | Método de pago de domiciliación electrónica admitido por bancos en Alemania           |
| ```POSTFIN```   |                                           | Método de pago bancario directo                                                       |
| ```IDEAL```     | iDeal                                     |Banca en línea Método de pago SEPA                                                     |
| ```SOFORT```    | Sofort                                    | Solución de pago de instancia                                                         |
| ```PAYPAL```    | PayPal                                    | Monedero digital más utilizado                                                        |
| ```PBL```       | Pay-by-link                               | Método utilizado para proporcionar enlaces de pago a los clientes para la facturación |
| ```VIPPS```     | Vipps                                     | Método de pago móvil utilizado en Noruega                                             |
| ```EASYCASH```  | EasyCash                                  | Monedero móvil/digital                                                                |
| ```MOBILEPAY``` | Mobile Pay                                | Monedero móvil/digital                                                                |
| ```TWINT```     | TWINT                                     | Monedero móvil/digital                                                                |
| ```SWISH```     | Swish                                     | Billetera móvil/digital utilizada en Suecia                                           |
| ```ALIPAY```    | AliPay                                    | Monedero móvil/digital con sede en China                                              |
| ```WECHATPAY``` |                                           | Monedero móvil/digital con sede en China                                              |


---

## See Also

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Post-authorisation & Returns](?path=docs/3-4-post-auth.md)
- [3-D Secure](?path=docs/3-5-3d-secure.md)
- [Card Verification](?path=docs/3-6-card-verification.md)
- [Currency Conversion](?path=docs/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/3-8-managed-redirect.md)
- [Orders](?path=docs/3-9-orders.md)
- [Payment URL](?path=docs/3-10-payment-url.md)
- [Recurring Payments](?path=docs/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/3-12-tokenisation.md)
- [Error codes](?path=docs/3-13-error-codes.md)

---
