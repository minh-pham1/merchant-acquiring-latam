---
tags: [Getting Started, Payments, Payment URL]
---

# Link de Pagamento

## /payment-url

O URL de pagamento é uma funcionalidade que permite que você forneça um link para seus clientes (por exemplo, em uma fatura por e-mail, mensagem do WhatsApp, SMS, código QR etc. método de pagamento preferido, sempre que for conveniente para eles.

Isso é especialmente útil em cenários onde as mercadorias são pagas após a entrega, onde nenhuma mercadoria é enviada (por exemplo, pagamento final para viagens reservadas meses atrás) ou para pagamento de contas mensais.

Você também pode implementar essa funcionalidade para compras malsucedidas em que a transação de pagamento original foi recusada, para que você possa dar proativamente ao cliente uma segunda chance de fazer a compra.

O Portal fornece

- A capacidade de solicitar um URL de pagamento (link) para um valor específico por meio da API

- Uma página de pagamento hospedada onde o cliente pode selecionar o método de pagamento preferido (com base nos métodos de pagamento ativados para sua conta) e efetuar o pagamento

- Uma página de resultado hospedada que informa ao cliente se o pagamento foi bem-sucedido ou não, incluindo um botão Repetir onde o cliente pode escolher um método de pagamento diferente caso a transação não tenha sido bem-sucedida

## Criando um URL de pagamento

Use POST para fazer uma chamada para /payment-url e especificar os valores para a carga conforme definido na tabela a seguir.

| **Atributo**                     | **Explicação**                                                                                                                                                                                                                                        |
|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```transactionType```            | Este atributo informa à nossa plataforma que tipo de transação executar quando o cliente concluir o pagamento por meio da URL. Para receber um pagamento, use SALE. Para criar uma pré-autorização, use PREAUTH. Para creditar o cliente, use CREDIT. |
| ```transactionNotificationURL``` | Defina a URL para a qual você gostaria que a notificação fosse postada quando o pagamento for concluído pelo cliente.                                                                                                                                 |
| ```expiration```                 | Esta é a data em que o URL expirará e não poderá mais ser usado pelo cliente.                                                                                                                                                                         |
| ```authenticateTransaction```    | Definido como TRUE para que a transação seja autenticada pelo 3-D Secure (isso acontecerá em nossa página hospedada, não por meio do fluxo 3-D Secure conduzido pela API REST).                                                                       |
| ```dynamicMerchantName```        | Isso define o nome do comerciante que aparecerá no extrato do cartão do cliente, para que você possa defini-lo como quiser que apareça lá.                                                                                                            |
| ```invoiceNumber```              | Coloque aqui o número da sua fatura.                                                                                                                                                                                                                  |
| ```purchaseOrderNumber```        | Coloque aqui o número da ordem de compra do cliente.                                                                                                                                                                                                  |
| ```hostedPaymentPageText```      | Isso define o texto que aparecerá na página de pagamento hospedada que o cliente usa para efetuar o pagamento.                                                                                                                                        |

O exemplo abaixo cria uma URL para um pagamento de venda a ser enviado a um cliente.

```json
{
  "transactionAmount": {
    "total": "42.42",
    "currency": "EUR"
  },
  "transactionType": "SALE",
  "transactionNotificationURL": "https://mywebshop/Customer",
  "expiration": "4102358400",
  "authenticateTransaction": true,
  "dynamicMerchantName": "MyWebsite",
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "hostedPaymentPageText": "Dear Customer, please pay our invoice 123456. "
}
```

O exemplo de gerador abaixo mostra os detalhes associados à API /payments-url.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-url",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
  "body": {
    "transactionAmount": {
      "total": "42.42",
      "currency": "USD"
    },
    "transactionType": "SALE",
    "transactionNotificationURL": "https://mywebshop/notification",
    "expiration": "4102358400",
    "authenticateTransaction": true,
    "dynamicMerchantName": "MyWebsite",
    "invoiceNumber": "96126098",
    "purchaseOrderNumber": "123055342",
    "hostedPaymentPageText": "Dear Customer, please pay our invoice 123456."
  }
}
```

## Outras funções de URL de pagamento

Para excluir uma URL de pagamento, adicione seu ```storeId``` e o ```transactionId``` da resposta da solicitação de criação da URL de pagamento (```ipgTransactionId```) ao cabeçalho /payment-url e use o método DELETE para chamar a API /payment-url.

Você pode recuperar os dados associados a uma URL de pagamento enviando um GET para /paymentURL. Adicione ```storeId``` e ```transactionId``` da resposta da solicitação de criação do URL de pagamento (```ipgTransactionId```) ao cabeçalho /payment-url para recuperar os detalhes de um URL de pagamento específico, ou defina ```storeId```, ```fromDate``` e ```toDate``` para chamar uma resposta com todas as URLs de pagamento e seus detalhes criados dentro de um intervalo de tempo específico.

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
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
