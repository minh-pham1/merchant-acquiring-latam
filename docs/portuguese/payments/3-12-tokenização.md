---
tags: [Getting Started, Payments, Tokenisation]
---

# Tokenização

## Fichas

Os tokens são úteis ao armazenar os detalhes dos instrumentos de pagamento de um cliente para proteger e acelerar o checkout posteriormente. Ao coletar os métodos de pagamento de um cliente, você pode solicitar um token, que pode ser usado no lugar dos detalhes do cartão do cliente para executar pagamentos futuros. Você precisará armazenar o token em sua plataforma, mapeado para os detalhes da conta do cliente.

Os tokens são úteis porque reduzem o risco de ataques bem-sucedidos aos seus e aos nossos sistemas de pagamento. A tokenização também reduz o requisito de PCI para você, pois significa que você não precisa armazenar detalhes do cartão em seus sistemas, o que deve reduzir seus custos associados à conformidade com PCI.

Para gerar um Token para uso futuro ao mesmo tempo em que envia um pagamento, use o objeto createToken para configurar um Token para uso múltiplo (definir reutilizável como True). Além disso, você pode criar seu próprio token para o comerciante, enviá-lo para nós dentro do objeto e definir regras para recusar pagamentos com detalhes de pagamento duplicados.

```json
  "createToken": {
    "value": "optional - define your own token",
    "reusable": true | false,
    "declineDuplicates": true | false
   }
```

Depois que o token é criado e você o armazena nos detalhes da conta do cliente, você pode usá-lo para executar pagamentos para o cliente. Para usar um instrumento de pagamento tokenizado, use os requestTypes PaymentToken* relevantes. Se você fornecer um valor de token, nós o armazenaremos, caso contrário, geraremos um valor de token e o repassaremos para você na resposta.

Quando um cliente está fazendo check-out e você já tokenizou seus detalhes de pagamento (e você armazenou o token com o registro da conta do cliente em seus sistemas), você pode solicitar detalhes do token para permitir que o cliente confirme que deseja pagar com o instrumento de pagamento armazenado. Para fazer isso, use o terminal GET, fornecendo o ```tokenid``` para receber uma resposta PaymentTokenDetails. Nossa sugestão é que você utilize o valor ```last4``` e ```marca``` para permitir que o cliente identifique corretamente seu instrumento de pagamento.

Você pode usar o token de pagamento no tipo de solicitação de pagamento primário relevante para executar o pagamento do cliente. Para atualizar ou excluir tokens de pagamento do cliente, consulte a seção Token.

### Crie um Token de Pagamento

Para tokenizar um cartão de pagamento separadamente para um pagamento, POST o payload abaixo para /payment-tokens (exemplo fornecido para cartão, mas use o objeto paymentMethod relevante para o instrumento de sua escolha).

```json
{
  "requestType": "PaymentCardPaymentTokenizationRequest",
  "paymentCard": {
    "number": "4012000033330026",
    "expiryDate": {
      "month": "12",
      "year": "24"
    }
  },
  "createToken": {
    "value": "123abc456def"
    "reusable": true,
    "declineDuplicates": false
  },
  "accountVerification": false,
  "additionalDetails": {
    "operatorId": "OPERATOR_ID_123XXX",
    "salesSystemId": "W-EU-H3866-FLS2"
  }
}
```

Se você deseja definir seu próprio valor para o token, inclua o atributo ```value``` no objeto createToken. Se você não incluir isso, definiremos o valor do token e o retornaremos na resposta como o atributo ```value``` no objeto paymentToken. O atributo ```reuseable``` é booleano. Se definido como verdadeiro, o token pode ser reutilizado. Se false, tem apenas um único uso. O atributo ```declineDuplicates``` também é booleano. Se você forneceu seu próprio valor de token para o instrumento de pagamento e um pagamento é enviado com o mesmo cartão de pagamento, mas sem token, o sistema recusará o pagamento (este é um controle de fraude que você pode definir no nível do token).

Use o seguinte exemplo de gerador para ver as cargas úteis e as respostas:

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-tokens",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  }
  "body" {
    "requestType": "PaymentCardPaymentTokenizationRequest",
    "paymentCard": {
      "number": "4012000033330026",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    },
    "createToken": {
      "value": "123abc456def"
      "reusable": true,
      "declineDuplicates": false
    },
    "accountVerification": false,
    "additionalDetails": {
      "operatorId": "OPERATOR_ID_123XXX",
      "salesSystemId": "W-EU-H3866-FLS2"
    }
  }
} 
```

## Atualizando Tokens de Pagamento

Você pode atualizar um ou mais tokens de pagamento por vez e alterar as configurações associadas ao token ou ao cartão de pagamento associado ao token. Para fazer essas atualizações, faça um PATCH para /payment-tokens usando requestType ```PaymentCardPaymentTokenUpdateRequest```.

O objeto ```PaymentTokens``` é uma lista e pode incluir vários tokens de pagamento. Veja o exemplo abaixo para ver como construir o payload. Cada um dos objetos de token abaixo inclui as atualizações necessárias - elas serão gravadas automaticamente no registro de token em nossos sistemas se a solicitação for processada com sucesso.

```json
{
  "requestType": "PaymentCardPaymentTokenUpdateRequest",
  "paymentTokens": [
    {
      "value": "1751905117310026",
      "reusable": true,
      "declineDuplicates": false,
      "paymentCard": {
        "number": "5424180279791732",
        "expiryDate": {
          "month": "03",
          "year": "21"
        },
        "securityCode": "977"
      }
    },
    {
      "value": "9877hkhk68688888ffgh",
      "reusable": true,
      "declineDuplicates": false,
      "paymentCard": {
        "number": "4773410012347324",
        "expiryDate": {
          "month": "12",
          "year": "26"
        },
        "securityCode": "123"
      }
    }
  ]
}
```

Se uma das atualizações falhar, isso será especificado na resposta - veja abaixo um exemplo de resposta acima em que a segunda atualização falhou.

```json
{
  "requestStatus": "PARTIAL_SUCCESS",
  "requestTime": "1554308829345",
  "errors": {
    "details": [
      {
        "message": "HOSTED_DATA_ID4773410012347324. Invalid credit card number: CreditCard [cardNumber=4773410...7324, expirationMonth=12, expirationYear=2026"
      },
    ]
  }
}
```

## Outras funções de token de pagamento

Um token de pagamento pode ser excluído enviando um DELETE para /payment-tokens/{token-id}.

Você pode recuperar os dados do cartão de pagamento associados a um token e as configurações do token enviando um GET para /payment-tokens/{token-id}.

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
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
