---
tags: [Getting Started, Payments, Post-authorisation & Returns]
---

# Post-authorisation & Returns

## /payments/{transaction-id}

Use uma transação secundária para anular, reembolsar ou confirmar uma pré-autorização realizada em uma transação previa. O parâmetro ```transactionId```, gerado para a transação original que requer uma ação secundária precisa ser informado para cada um desses tipos de requisição.

Para cancelar uma transação (no mesmo dia), use o tipo de requisição ```voidTransaction```.

Para reembolsar uma transação (no dia seguinte), use o tipo de requisição ```returnTransaction```. 

Para confirmar uma pré-autorização usando uma transação de Confirmação de Autorização, indique a transação original neste campo de parâmetro e então faça o POST usando o esquema ```PostAuth```.

Uma versão atualizada do diagrama de matriz de decisões indicada previamente está demonstrada abaixo, com os tipos de requisição de transação secundária agora incluídos.

![Decision Matrix!](/assets/images/3-4-decision-matrix.png "Decision Matrix")

indica os links para os modelos esquemas de “requestTypes” e o método para o seu uso. Em todas estas transações, o atributo “transaction-id” precisa ser informado com o dado retornado pela mensagem de resposta “200 response” no campo ```ipgTransactionId``` da transação original.

## API Endpoint

Você vai encontrar o endpoint aqui /payments/{transaction-id}

Para descobrir o status de uma transação que você já submeteu, execute uma chamada GET para o endpoint ```/payments/{transaction-id}```. O gateway vai devolver os detalhes e o status da transação que você submeteu.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments/1001-1001-1001-1001",
  "query": {},
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  }
  "body": {
  "requestType": "ReturnTransaction",
    "transactionAmount": {
      "total": 3,
      "currency": "USD"
    }
  }
}
```

Os tipos de “requestTypes” disponíveis estão listados abaixo, com uma explicação da finalidade de cada um.

| requestType                  | Método | Descrição                                                                                                                      |
|------------------------------|--------|---------------------------------------------------------------------------------------------------------|
| ```VoidTransaction```        | POST   | Este requestType te permite cancelar / anular uma transação que você submeteu previamente no mesmo dia. |
| ```VoidPreAuthTransaction``` | POST   | Este requestType te permite cancelar / anular uma transação de pré-autorização.                         |
| ```PostAuthTransaction```    | POST   | Este requestType te permite confirmar uma transação de pré-autorização.                                 |
| ```ReturnTransaction```      | POST   | Este requestType te permite realizar um reembolso sobre uma transação realizada em dias anteriores.     |
| ```Transaction Inquiry```    | GET    | Este requestType te permite consultar os detalhes e status de uma transação submetida previamente.      |

## Cenários adicionais de pagamento

Há um grande número de cenários de negócio que requerem a combinação de diferentes chamadas para um mesmo ou diferentes end points. O exemplo abaixo demonstra a maneira na qual diferentes requestTypes e chamadas devem ser feitas para a API de Pagamentos para gerar diferentes resultados.

### Aumentando ou diminuindo uma pré-autorização

Para aumentar ou diminuir o valor de uma transação de pré-autorização, submeta outro pedido de pré-autorização para o mesmo portador do cartão preenchendo o campo ```orderId``` com o valor enviado na resposta do pedido original de pré-autorização.

To increase or decrease the value of a pre-authorisation transaction, submit another pre-auth for the same cardholder referencing the ```orderId``` field value from the reponse associated with the original Pre-Auth transaction:

```json
{
  "requestType": "PaymentCardPreAuthTransaction",
  "transactionAmount": {
    "total": "17.00",
    "currency": "EUR"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "4149011500000147",
      "securityCode": "147",
      "expiryDate": {
        "month": "12",
        "year": "20"
      }
    }
  },
  "order": {
    "orderId": "{{lastOrderId}}"
  }
}
```

A transação original de pré-autorização será então acrescida ou diminuída de acordo ao seu pedido.

## Confirmando e Cancelando pré-autorizações

Para confirmar uma pré-autorização, chame uma transação POST de “postAuth”, então chame uma transação secundária para o end point /payments/{transaction-id} mencionando o campo ```orderId``` da transação original de pré-autorização no campo {transaction-id}. 

O objeto “splitShipment” permite múltiplas confirmações de autorização em cenários nos quais há múltiplas confirmações contra uma única pré-autorização original.

```json
{
  "requestType": "PostAuthTransaction",
  "transactionAmount": {
    "total": "12.04",
    "currency": "USD"
  },
  "splitShipment": {
    "totalCount": 1,
    "finalShipment": true
  }
}
```

Para cancelar uma confirmação de autorização, ou seja, reabrir a pré-autorização, execute uma transação secundária com requestType “voidTransaction” ajustando o valor do campo ```orderId``` com a resposta da transação de confirmação de pré-autorização “postAuth”, do campo {transaction-id}.

Para cancelar a transação de pré-autorização, executa uma transação secundária com requestType “voidPreAuthTransaction” ajustando o valor do campo ```orderId``` com a resposta da transação de pré-autorização, do campo {transaction-id}.

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
- [Tipos de pagamento](?path=docs/portuguese/payments/3-3-tipos-pagamento.md)
- [3-D Secure](?path=docs/portuguese/payments/3-5-3d-secure.md)
- [Verificação de cartão](?path=docs/portuguese/payments/3-6-verificação-cartão.md)
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
