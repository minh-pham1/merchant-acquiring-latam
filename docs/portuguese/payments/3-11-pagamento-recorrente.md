---
tags: [Getting Started, Payments, Recurring Payments]
---

# Pagamentos recorrentes

## /payment-schedule

A API de cronogramas de pagamento permite que você crie e gerencie um cronograma de pagamento recorrente para um consumidor individual. Esta API abrange recursos de criação, atualização, cancelamento e geração de relatórios. Por exemplo, você usaria esta API para configurar pagamentos mensais de assinatura em relação a um método de pagamento se estiver fornecendo produtos ou serviços contínuos a um cliente.

A API de programações de pagamento usa o mesmo cabeçalho e a maioria dos mesmos objetos que a API de pagamentos, portanto, se você já implementou essa API, deve ser fácil estender isso.

## Criando um cronograma de pagamento

```json
{ 
  method: "POST",
  url: 'https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-schedules',
  headers: {
    "content-type": 'application/json',
    "client-request-id": '',
    "api-key": '',
    "timestamp": '',
    "message-signature": ''
  }
  "body"{
    "requestType": "PaymentMethodPaymentSchedulesRequest",
    "startDate": "2020-11-16",
    "numberOfPayments": 10,
    "maximumFailures": 1,
    "invoiceNumber": "96126098",
    "purchaseOrderNumber": "123055342",
    "transactionOrigin": "ECOM",
    "dynamicMerchantName": "MyWebsite",
    "frequency": {
      "every": 1,
      "unit": "MONTH"
    },
    "paymentMethod": {
      "paymentCard": {
        "number": "5426064000424979",
        "expiryDate": {
          "month": "12",
          "year": "20"
        },
        "securityCode": "147",
        "cardFunction": "CREDIT"
      }
    },
    "transactionAmount": {
      "total": "25.24",
      "currency": "GBP"
    },
  }
}
```

Para criar uma programação de pagamento quando o cliente estiver finalizando a compra, capture os detalhes de pagamento do cliente para qualquer método de pagamento que ele esteja usando, como faria ao processar um pagamento único normal. Além das informações de pagamentos padrão, você precisa adicionar mais atributos para configurar com êxito o cronograma de pagamento:

| Atributo              | Explicação                                                                                                                                                                                                                                                                                                                                                  |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```startDate```        | A data de início indica a data do primeiro pagamento. Isso pode ser datado no futuro. Este valor define a linha de base para o cálculo do próximo pagamento, dependente dos valores ```every``` e ```unit``` definidos no objeto ```frequency```.                                                                                                          |
| ```numberofPayments``` | Isso define o número total de instâncias de recebimento de um pagamento na programação.                                                                                                                                                                                                                                                                    |
| ```maximumFailures```  | Isso define o limite de novas tentativas para o pagamento. Se falhar uma vez, você pode configurar nossa plataforma para tentar automaticamente até 999 vezes. O período entre as tentativas é sempre de 1 dia, portanto, você precisa garantir que o máximo de tentativas seja menor que o número de dias entre os pagamentos de acordo com o cronograma. |
| ```every```            | Define o incremento para a execução dos pagamentos recorrentes, em parceria com o valor no campo unidade.                                                                                                                                                                                                                                                  |
| ```unit```             | Define a frequência - ```DAY```, ```WEEK```, ```MONTH``` ou ```YEAR```.                                                                                                                                                                                                                                                                                    |

Portanto, se você definir ```startDate``` como 1º de dezembro de 2020, ```numberofPayments``` como 12, ```every``` como 1 e ```unit``` como MÊS, o último pagamento será efetuado em 1º de novembro de 2021, com um pagamento efetuado no dia 1º de cada mês intermediário.

```json
{
  "requestType": "PaymentMethodPaymentSchedulesRequest",
  "startDate": "2020-11-16",
  "numberOfPayments": 10,
  "maximumFailures": 1,
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "transactionOrigin": "ECOM",
  "dynamicMerchantName": "MyWebsite",
  "frequency": {
    "every": 1,
    "unit": "MONTH"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "5426064000424979",
      "expiryDate": {
        "month": "12",
        "year": "20"
      },
      "securityCode": "147",
      "cardFunction": "CREDIT"
    }
  },
  "transactionAmount": {
    "total": "25.24",
    "currency": "GBP"
  },
  "clientLocale": {
    "language": "EN",
    "country": "UK"
  }
}
```

Você também pode usar programações de pagamento para definir pagamentos únicos com data futura, definindo o número de pagamentos como 1 e a data de início como uma data futura. Contanto que o valor ```every``` seja definido como 1, isso é o mesmo que definir um único pagamento com data futura.

## Usando um pedido de referência para criar uma programação de pagamento

Para criar um cronograma de pagamento usando um único pagamento já enviado, envie o payload conforme abaixo para a API /payment-schedule, mas inclua o atributo ```referenceOrderId``` como o orderId da resposta do pagamento original. Isso é útil quando um cliente compra algo como uma única compra, mas opta por continuar o acesso como uma assinatura do seu produto ou serviço.

```json
{
  "requestType": "ReferencedOrderPaymentSchedulesRequest",
  "referencedOrderId": "R-f9c2c198-b7cc-491a-a711-93d22fd0e589",
  "startDate": "2020-11-16",
  "numberOfPayments": 10,
  "maximumFailures": 1,
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "transactionOrigin": "ECOM",
  "dynamicMerchantName": "MyWebsite",
  "frequency": {
    "every": 1,
    "unit": "MONTH"
  },
  "transactionAmount": {
    "total": "25.24",
    "currency": "GBP"
  },
  "clientLocale": {
    "language": "EN",
    "country": "USA"
  }
}
```

## Atualização de um cronograma de pagamento

Para atualizar um cronograma de pagamento, use PATCH para fazer uma chamada para /payment-schedules/{order-id} onde o valor {order-id} é o valor ```orderId``` da resposta do ``` original PaymentSchedulesRequest``` POST. Use a mesma estrutura de carga útil para configurar o cronograma de pagamento, mas identifique o cronograma de pagamento para atualização especificando o ```orderId``` na resposta do ```PaymentSchedulesRequest``` original em /payment-schedules/{ id do pedido}. Faça atualizações no Cronograma de pagamento, Método de pagamento ou Valor da transação conforme necessário na carga útil da chamada PATCH.

## Cancelando um cronograma de pagamento

Cancele um cronograma de pagamento fazendo uma chamada DELETE para /payment-schedules/{order-id} onde o valor {order-id} é o valor ```orderId``` da resposta do ```PaymentSchedulesRequest`` original ` POST.

## Recuperando dados do Cronograma de Pagamento

Recupere os dados do Cronograma de pagamento fazendo uma chamada GET para /payment-schedules/{order-id} onde o valor {order-id} é o valor ```orderId``` da resposta do ```PaymentSchedulesRequest`` original ` POST.

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
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
