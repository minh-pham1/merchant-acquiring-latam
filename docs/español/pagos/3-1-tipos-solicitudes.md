---
tags: [Getting Started, Payments, Request Types]
---

# Tipos de Solicitudes

Diferentes acciones de pago requieren diferentes valores de tipo de solicitud. La siguiente tabla explica las situaciones en las que es posible que desee utilizar los diferentes valores de tipo de solicitud. Los detalles técnicos de cada uno de estos tipos de solicitud se incluyen en la sección BodyRequest del explorador de API. Todas las llamadas a la API son ```POST```.

| Tipo de pago                            | Tipo de Solicitud                                | Escenario de Pago                                                                                                                                                                                                                                                        |
|-----------------------------------------|--------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pago con Tarjeta Única                  | PaymentCardSaleTransaction                       | Utilice este tipo de solicitud para ejecutar una transacción de pago de cliente normal con una tarjeta de crédito o débito.                                                                                                                                              |
| Crédito                                 | PaymentCardCreditTransaction                     | Utilice este tipo de solicitud para ejecutar una transacción de pago de crédito original a la tarjeta de crédito o débito de un cliente. Esto significa que reembolsará este monto a la tarjeta del cliente sin hacer referencia a ninguna transacción de venta anterior |
| Autorización previa (tomar un depósito) | PaymentCardPreAuthTransaction                    | Use este tipo de solicitud para preautorizar una cantidad contra una tarjeta, para completarla en un momento posterior.                                                                                                                                                  |
| Pago con token                          | PagoTokenSaleTransaction                         | Use este tipo de solicitud para ejecutar una transacción de pago de cliente normal con un token generado previamente.                                                                                                                                                    |
| Reembolso de token                      | PaymentTokenCreditTransaction                    | Use este tipo de solicitud para ejecutar una devolución a la tarjeta de crédito o débito de un cliente utilizando un token generado previamente.                                                                                                                         |
| Autorización previa del token           | PaymentTokenPreAuthTransaction                   | Utilice este tipo de solicitud para preautorizar un monto mediante un token, para completarlo en un momento posterior.                                                                                                                                                   |
| Pagos SEPA                              | SepaSaleTransaction                              | Utilice este tipo de solicitud para aceptar el pago de un cliente a través de SEPA.                                                                                                                                                                                      |
| Venta de monedero                       | WalletSaleTransaction                            | Use este tipo de solicitud para ejecutar una transacción de pago de cliente normal con un método de pago de billetera.                                                                                                                                                   |
| Autorización previa de billetera        | WalletPreAuthTransaction                         | Use este tipo de solicitud para ejecutar una autorización previa mediante un método de pago de billetera                                                                                                                                                                 |

Para Transacciones primarias, la matriz de decisión para definir qué tipo de solicitud usar se presenta a continuación:

![Request Type Decision Matrix!](/assets/images/3-1-decision-matrix.png "Request Type Decision Matrix")

Las diferencias que se requieren en el JSON para cada uno de estos tipos de solicitudes se explican en el resto de esta guía.

---

## Ver más

- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
