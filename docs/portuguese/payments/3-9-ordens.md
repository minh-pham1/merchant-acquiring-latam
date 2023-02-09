---
tags: [Getting Started, Payments, Orders]
---

# Ordens

## /orders/{order-id}

A API /orders permite que você publique uma transação secundária usando seu próprio ID de pedido. Em vez de enviar o POST com um return ou postauth requestType e o ```ipgTransactionId```, você pode usar seu valor ```orderId```, aquele que você nos forneceu no objeto Order na transação primária enviada para /pagamentos. Além disso, você pode usar GET para extrair detalhes da transação primária usando a mesma referência ```orderId```.

```json
{
  "method": "get",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments/IPG001001001",
  "query": {},
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
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
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
