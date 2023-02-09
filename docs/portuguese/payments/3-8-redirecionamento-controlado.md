---
tags: [Getting Started, API Reference, Managed Redirect, Credit Card, 3-D Secure]
---

# Redirecionamento Controlado

O recurso Managed Redirect reduz significativamente o esforço de desenvolvimento necessário para uma integração de métodos de pagamento que requerem redirecionamento do consumidor. Uma simples solicitação de API para o Gateway gera uma URL, que é usada na próxima etapa para redirecionar seus clientes e permitir que nossa Página de pagamento hospedada (Conectar) lide com o processo de pagamento em seu nome.

## Cartão de crédito

O documento JSON a seguir representa um exemplo de solicitação para gerar um redirectURL para um pagamento com cartão de crédito:

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "403587******4977",
            "securityCode": "***",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
           }
        }
      }
    }
```

Na resposta do Gateway, você recebe o parâmetro 'redirectURL', que pode passar para seus consumidores para concluir o pagamento.

O seguinte documento JSON representa um exemplo de resposta:

```json
{
   "clientRequestId": "a35e3995-e74a-4e25-ad49-849de6bbabca",
   "apiTraceId": "Ymf1-1ohKkLOqSdsIKi3AQAAA6c",
   "requestStatus": "SUCCESS",
   "orderId": "R-0229343b-aeed-411e-9496-c1fd73dafae5",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-0229343b-aeed-411e-9496-c1fd73dafae5&managedRedirectUrlId=84af185c-5ec9-45ea-952a-ee4497726178",
   "transactionId": "84400086815"
}
```

> 🚧 Observação. Observe que, após o cliente concluir o pagamento, você receberá uma resposta formatada em HTML em vez de uma resposta formatada em JSON.
<!-- theme: warning -->

## 3-D Secure

Para os casos em que você exigiria a inclusão da autenticação 3-D Secure, você deve incluir o objeto 'redirectAttributes' adicional em sua solicitação inicial.

| redirectAttribute Name            | Obrigatório/Opcional | Descrição                                                                                                                                                                                                                                                                                                                                              |
|-----------------------------------|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticateTransaction```     | obrigatório          | Indica se uma autenticação 3-D Secure é solicitada para esta transação. Valores aceitos: • true                                                                                                                                                                                                                                                        |
| ```challengeIndicator```          | obrigatório          | Indica se um desafio é solicitado para esta transação. Se não for enviado na solicitação, um valor padrão '01' será preenchido automaticamente. Valores aceitos: • 01 = Sem preferência • 02 = Nenhum desafio solicitado • 03 = Desafio solicitado (preferência do 3DS Requestor) • 04 = Desafio solicitado (Mandato)                                  |
| ```language```                    | obrigatório          | Valor que representa o idioma do navegador conforme definido no IETF BCP47, exemplo de idiomas usados com frequência: Inglês: en Espanhol: es Francês: fr Alemão: de Português: pt Hindi: hi                                                                                                                                                           |
| ```threeDSEmvCoMessageCategory``` | opcional             | Identifica a categoria da mensagem para um caso de uso específico. Se não for enviado na solicitação, um valor padrão '01' será preenchido automaticamente. Valores aceitos: • 01 = Autenticação de Pagamento (valor padrão) • 02 = Autenticação de Não Pagamento                                                                                      |
| ```mobileMode```                  | opcional             | Booleano que representa a solicitação de utilização do modo móvel das páginas de pagamento hospedadas. Valores aceitos: • true • false                                                                                                                                                                                                                 |
| ```browserJavaScriptEnabled```    | opcional             | Booleano que representa a capacidade do navegador do titular do cartão de executar JavaScript. Valores aceitos: • true • false                                                                                                                                                                                                                         |
| ```threeDSTransactionType```      | opcional             | Identifica o tipo de transação que está sendo autenticada. Se não for enviado na solicitação, um valor padrão '01' será preenchido automaticamente. Valores aceitos: • 01 = Compra de Mercadorias/Serviços (valor padrão) • 03 = Aceitação de Cheque • 10 = Financiamento em Conta • 11 = Transação Quase-Cash • 28 = Ativação e Carregamento Pré-pago |
| ```skipTRA```                     | opcional             | Booleano que representa a capacidade de ignorar a recomendação de resultado da Análise de Risco de Transação em um nível de transação. Disponível apenas para lojas habilitadas com serviço TRA. Valores aceitos: • true • false                                                                                                                       |
| ```override3dsCountryExclusion``` | opcional             | Booleano que substitui a configuração de exclusão de país do Dynamic 3-D Secure. Valores aceitos: • true • false|

O seguinte documento JSON representa um exemplo de uma transação incluindo um conjunto mínimo de 'redirectAttributes' obrigatórios:

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "520474******2745",
            "securityCode": "XXX",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
            }
        }
    },
    "redirectAttributes": {
        "language": "en",
        "challengeIndicator": "01",
        "authenticateTransaction": true
    }
}
```

O seguinte documento JSON representa um exemplo de resposta que você recebe do Gateway:

```json
{
   "clientRequestId": "9410557e-e451-4ed8-b37e-0f268a63f060",
   "apiTraceId": "YmuZy8URv@sTmvxBgZjBdQAAAr0",
   "requestStatus": "SUCCESS",
   "orderId": "R-432da132-f610-4714-91a3-3500a02c5863",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-432da132-f610-4714-91a3-3500a02c5863&managedRedirectUrlId=9c55f38b-bd57-455a-92ab-90b580306e0f",
   "transactionId": "84400249138"
}
```

### Pagamentos Locais

Se sua conta estiver inscrita no modelo de pagamentos locais coletivos, você pode enviar o método de pagamento individual em seu ```GenericManagedRedirectRequest``` para nosso Gateway.

### POST /ipgrestapi/v2/services/managed-redirect

```json
{
  "requestType": "GenericManagedRedirectRequest",
  "storeId": "120995000",
  "transactionAmount": {
    "total": "15",
    "currency": "EUR"
    },
  "transactionType": "SALE",
  "paymentMethod": {
      "paymentMethodType": "IDEAL_APM"
    }
}
```

Na resposta você receberá um ```redirectURLparameter```, que poderá passar para o seu consumidor para finalizar o pagamento.

O seguinte documento JSON representa um exemplo da resposta:

```json
{
   "clientRequestId": "67db6b7e-37d8-4dbd-8b8f-28749a9b0770",
   "apiTraceId": "Yufk72hKKznZUrdK7lqvnwAAA3U",
   "requestStatus": "SUCCESS",
   "orderId": "R-8e6b5d51-842a-4e8d-9231-99450be41f59",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=120995000&oid=R-8e6b5d51-842a-4e8d-9231-99450be41f59&managedRedirectUrlId=f29084b5-78da-41d8-8ea5-870926f2f516",
   "transactionId": "84405921374"
}
```

Os seguintes métodos de pagamento foram ativados para a solução Managed Redirect:

- ```ALIPAY```
- ```BCMC_APM```
- ```EPS```
- ```GIROPAY_APM```
- ```IDEAL_APM```
- ```MYBANK```
- ```PAYSAFECARD```
- ```PRZELEWY24```
- ```SOFORT_APM```
- ```TRUSTLY```

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
- [Tipos de pagamento](?path=docs/portuguese/payments/3-3-tipos-pagamento.md)
- [Post-authorisation & Returns](?path=docs/portuguese/payments/3-4-post-auth.md)
- [3-D Secure](?path=docs/portuguese/payments/3-5-3d-secure.md)
- [Verificação de cartão](?path=docs/portuguese/payments/3-6-verificação-cartão.md)
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
