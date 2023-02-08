---
tags: [Getting Started, Payments, Card Verification]
---

# Verificação de cartão

## /card-verification 

Use o serviço de verificação de cartão para checar se um cartão pode ser usado para cobrança antes de que o cliente o utilize para o pagamento. Use a estrutura abaixo usando o método POST para a API /card-verification e nós vamos executar uma autorização com valor zero para esse cartão para validar se ele não é fraudulento, presente em black lists, expirado ou bloqueado. Essencialmente ele nos permite checar se o cartão e a conta estão liberados para processar pagamentos.

```json
{ 
  method: "POST",
  url: "https://prod.api.firstdata.com/ipp/payments-gateway/v2/card-verification",
  headers: {
    "content-type": "application/json",
    "client-request-id": "",
    "api-key": "",
    "timestamp": "",
    "message-signature": ""
  }
  "body": {
    "paymentCard": {
      "number": "4035874000424977",
      "expiryDate": {
        "month": "12", 
        "year": "20"
      },
      "securityCode": "977"
    },
    "billingAddress": {
      "address1": "5565 Glenridge Conn",
      "city": "Atlanta",
      "postalCode": "30342",
      "country": "USA"
    }
  }
}
```

Abaixo segue um exemplo de consulta de verificação de cartão (corpo da requisição apenas):

```json
{
  "paymentCard": {
    "number": "4035874000424977",
    "expiryDate": {
      "month": "12",
      "year": "20"
    },
    "securityCode": "977"
  },
  "billingAddress": {
    "address1": "5565 Glenridge Conn",
    "city": "Atlanta",
    "postalCode": "30342",
    "country": "USA"
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
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
