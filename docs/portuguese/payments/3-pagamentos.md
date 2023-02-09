---
tags: [Payments, API, Header, Parameters, Polymorphism, Primary Transaction]
---

# Payments

Utilize nossa API para aceitar Pagamentos de todos os tipos e para processar ações complementares como anulações, reembolsos ou confirmações de pré-autorização.

Você pode usar nossa API de pagamentos para oferecer uma variedade de soluções para os seus clientes. Ela permite que você aceite pagamentos usando a forma mais apropriada de acordo ao tipo de transação.

A API de pagamento da Fiserv suporta os principais meios de pagamento: ```Visa```, ```Mastercard```, ```Amex```, ```Apple Pay```, ```Google Pay```, ```Sepa```, assim como várias outras opções locais e internacionais. Para obter acesso a meios de pagamento além deste pacote principal, por favor acione seu representante de integração de serviços.

Outras soluções de pagamento podem ser usadas para criar e concluir um pagamento, criar e gerenciar um agendamento de pagamento recorrente, gerar um link de pagamento para envio a um cliente, checar o cartão de um cliente ou oferecer a ele a opção de pagar em sua moeda local.

Uma vez que você executa uma transação usando esta API, você consegue recuperar todos os seus detalhes usando nossas APIs de Merchant Intelligence.


## Header e Parâmetros

Nossa API de pagamento tem um cabeçalho estruturado, consistente e baseado em um conjunto de parâmetros. Para criar o “header”/cabeçalho, utilize os seguintes valores:


| Header Parameter  | Type    | Description                                                                                                                                                                                                                        |
|-------------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Content-Type      | String  | Defina esse atributo como application/json                                                                                                                                                                                         |
| Client-Request-Id | String  | Esse é um ID que você cria de forma que possamos mutuamente gerenciar sua requisição. Ele deve ser único por requisição. Recomendamos que ele seja construído no formato 128-bit UUID.                                             |
| Api-Key           | String  | Essa é a chave que você usará para se identificar conosco. Você receberá uma chave para desenvolvimento e testes, e então uma nova chave quando estiver pronto para produção.                                                      |
| Timestamp         | Integer | Você deve definir esse campo como “timestamp” em milissegundos. Ele será usado para assinatura da geração da requisição e controle de tempo limite.                                                                                |
| Message-Signature | String  | Nós o usamos para segurança. A assinatura dessa mensagem é codificada padrão Base64 HMAC hash (algoritmo SHA256 com a API segredo como chave). Para mais informações, acesse a documentação de suporte do Portal do Desenvolvedor. |

O “header”/cabeçalho é construído como no exemplo abaixo:

```json
{
  "method": "post",
  "url": "https://prod.emea.api.fiservapps.com/ipp/payments-gateway/v2/payments",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
}
```

Gerar a assinatura de uma mensagem é um passo importante desta API. Você pode aprender mais aqui: 

[Gerar uma assinatura de mensagem](?path=recipes/1-generate-message.md)


## Polimorfismo

Nossas APIs de Pagamentos são polimórficas, o que significa que você pode submeter vários tipos de requisição com regimes diferentes para as mesmas APIs e gerar diferentes tipos de pagamentos e respostas.

O termo é tirado da química – Polimorfismo é a habilidade de materiais sólidos existirem em duas ou mais formas cristalinas com diferentes arranjos.

Quando aplicamos isso para o desenho das APIs, isso significa que uma mesma API pode ser utilizada para múltiplas ações de pagamento. Nós habilitamos isso pela definição de diferentes esquemas no corpo da requisição, cada um usado para um tipo de ação de pagamento.

O polimorfismo na nossa API de pagamento está definido no tipo de requisição (o campo ```requestType```), que te habilita a separar a ação de pagamento de acordo com o tipo da transação (```sale```, ```refund```, ```cancellation```, etc.) e o meio de pagamento que o cliente quer usar (cartão de crédito ou débito, carteira digital, SEPA, Paypal etc.).

Você usará a mesma API para processar um pagamento para todas essas variações, mas o valor do campo ```requestType``` que você usar, e os outros objetos da sua requisição vão variar dependendo do tipo de ação de pagamento que você queira executar.

Como um exemplo, você vai usar o requestType ```PaymentCardSaleTransaction``` para processar um pagamento tradicional quando o cliente quer fazer o check out. Você então pode usar uma segunda transação com outro requestType` para pedir o reembolso ou a anulação da transação.

## Exemplo

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "12.04",
    "currency": "USD"
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
}
```

```json
{
  "requestType": "ReturnTransaction",
  "transactionAmount": {
    "total": 3,
    "currency": "USD"
  }
}
```

Estes exemplos estão indicados abaixo:

[Processando uma venda com cartão](?path=recipes/2-card-payment.md)

[Transação de reembolso](?path=recipes/3-refund-transaction.md)

## Como esse recurso funciona

O endpoint ```/payments``` te permite ```create```, ```inquire``` about, e ```finalize``` pagamentos.

Ela te habilitará ```void```, ```refund``` uma transação prévia ou mesmo executar um ```voids``` ou ```partial refunds``. Por fim, e sempre que necessário, ela te permite ```pre-authorise``` transações que deverão ser concluídas posteriormente.

A API te permite realizar pagamentos usando diferentes instrumentoos, ou cartões de crédito e débito, tokens ou através de Soluções locais como SEPA DD ou uma conta Paypal. Todos esses métodos serão explicados abaixo.

Você pode usar a API para aceitar transações via wallet por meios de pagamento como Apple Pay ou Google Pay. Isto te permite usar protocolos extras de autenticação para certificação de que os pagamentos do cliente são seguros e te proteger de fraudes.

Nossa API de pagamentos tem dois usos principais – Transações primárias e secundárias. Transações primárias são tipicamente transações de vendas, pré-autorizações ou créditos. Transações secundárias te permitem o reembolso, cancelamento de uma transação ou a conclusão de uma pré-autorização.


## Criando uma transação primária

Transações primárias são usadas para capturar um pagamento, uma pré-autorização ou transação de crédito que não guarda relação com uma transação anterior. Neste passo a passo, usaremos o RequestType “PaymentCardSaleTransaction” que você pode usar para uma transação padrão de débito ou crédito sem referência com uma transação anterior. O uso de cada tipo diferente de RequestType, seus cenários de uso e as diferenças na chamada da requisição serão explicadas nesse guia.

O RequestType ```PaymentCardSaleTransaction`` requer.os seguintes campos para realizar o pedido de uma transação primária.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
  "body": {
    "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "12.04",
      "currency": "USD"
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
  }
}
```

Neste modelo, os campos mais importantes são o ```transactionAmount``` e o ```paymentMethod```. Sem estes campos preenchidos, não podemos processar o pagamento.

Também recomendamos que você inclua a informação de cobrança e despacho para permitir que as checagens de 3DSecure possam rodar no background (veja a página [Implementando 3DSecure](?path=docs/portuguese/payments/3-5-3d-secure.md) para mais informações). Isto torna o processo de checkout mais simples para os seus clientes e permite que os nossos sistemas antifraude te protejam mais facilmente.

Ambos campos, cobrança ```billing``` e despacho ```shipping`` tem a mesma estrutura:

```json
{
  "name": "Alec Leamas",
  "customerId": "1234567890",
  "contact": {
    "phone": "07777777777",
    "email": "alecleamas@tswciftc.com"
  }
  "address": {
    "address1": "Bernauer Strasse 111",
    "city": "Berlin",
    "postalCode": "13355",
    "country": "Germany"
  }
}
```

O fluxo abaixo mostra o processo de pagamento padrão usando cartão.
![standard payments process!](/assets/images/3-payment-process.png "Standard payment process")


[Verificando um pagamento de cartão](?path=recipes/4-verify-card-payment.md)

[Pesquisando informações de cartão](?path=recipes/5-card-information.md)

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
