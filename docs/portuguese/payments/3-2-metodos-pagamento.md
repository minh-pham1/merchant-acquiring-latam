---
tags: [Getting Started, Payments, Request Methods, SEPA Transactions]
---

# Métodos de Pagamento

Para criar uma transação de pagamento para um cliente usando uma transação primária, você deve incluir o campo “paymentMethod” na chamada JSON. Dependendo do “requestType”, você fará mudanças no campo “paymentMethod” para refletir diferentes instrumentos de pagamento e tipos de transação.

Para processar um pagamento de cartão, use “PaymentCardPaymentMethod”:


```json
{
  "paymentCard": {
    "number": "5424180279791732",
    "expiryDate": {
      "month": "03",
      "year": "21"
    },
    "securityCode": "977"
  }
}
```

Para processar um pagamento por token, use “PaymentTokenPaymentMethod”:

```json
{
  "paymentToken": {
    "value": "1235325235236",
    "function": "DEBIT",
    "securityCode": "977"
  }
}
```

Dentro do objeto “paymentMethod”, você deverá passar diferentes valores dependendo do tipo de meio de pagamento usado para a transação. É ele que define o instrumento de pagamento que o consumidor está usando na transação. As opções disponíveis estão indicadas abaixo. Elas serão atualizadas regularmente à medida que novos meios de pagamento são incorporados.

Mais informações sobre as mudanças na estrutura de pagamento associada com cada um desses métodos podem ser acessadas clicando em “paymentMethodType”.

| paymentMethodType | Descrição                                                                                                                                                                                                      |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PAYMENT_CARD      | método de pagamento cartão cobre todos os tipos e redes de cartão de débito e crédito. Seu representante na Fiserv lhe dirá quais as redes de pagamentos você pode processar transações.                       |
| PAYMENT_TOKEN     | O método de pagamento Token permite que você use um token criado por nós, ao invés do detalhamento do método de pagamento atual. Mais informações sobre o uso de tokens aqui.                                  |
| SEPA A SEPA       | Essa transação permite que o cliente faça o pagamento via uma requisição banco a banco através da rede europeia SEPA.                                                                                          |
| WALLET            | Habilita Pagamentos via carteira digital. Transações de carteira digital requerem um método de pagamento específico. Mais informações deste objeto está localizada na seção de pagamentos de carteira digital. |

## Transações SEPA

Para usar o requestType ```SepaSaleTransaction```, o objeto “paymentMethod” é substituído pelo objeto “SEPA”. O objeto SEPA inclui todos os dados requeridos para poder processar uma transação SEPA. SEPA significa “Área única de pagamentos europeia” e permite que os pagamentos sejam processados imediatamente entre os bancos em baixo custo. Essa funcionalidade está habilitada apenas para pagamentos domésticos na Alemanha neste momento. Outros países devem ser incorporados no futuro próximo.

Você precisa ser associado SEPA para esses pagamentos e ter um reference ID para popular esse campo mandatório no objeto.

O objeto de método de pagamento SEPA está indicado abaixo:

```json
{
  "sepa": {
    "iban": "DE34500100600032121604",
    "name": "Alan Turner",
    "country": "DEU",
    "email": "AlanTurner@Bonn.com",
    "mandate": {
      "reference": "3JLCSTIG",
      "url": "https://www.fiserv.com",
      "signatureDate": "2020-10-15",
      "type": "SINGLE"
    }
  }
}
```

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
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