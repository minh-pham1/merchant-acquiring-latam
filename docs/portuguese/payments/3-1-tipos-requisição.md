---
tags: [Getting Started, Payments, Request Types]
---

# Tipos de Requisição

Diferentes ações de pagamento requerem diferentes tipos de “requestType”. A tabela abaixo indica em que situações você deve usar cada valor de “requestType”. O detalhamento técnico de cada um está incluído no corpo da API. Todas as chamadas da API são do tipo ```POST```.

| Tipo do pagamento                         | Tipo de Solicitud                                | Cenário de Pagamento / Caso de uso                                                                                                                                                                                                                                                        |
|-------------------------------------------|--------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pagamento cartão único                    | PaymentCardSaleTransaction                       | Use este requestType para processar uma transação padrão com um cartão de crédito ou de débito.                                                                                                                                            |
| Crédito                                   | PaymentCardCreditTransaction                     | Use este requestType para processar uma transação de crédito para o portador do cartão de crédito ou débito. Isso significa que você vai reembolsar esse valor ao portador do cartão sem qualquer vínculo a uma transação de venda prévia. |
| Pré-autorização (confirmando um depósito) | PaymentCardPreAuthTransaction                    | Use este requestType para pré-autorizar um valor a um cartão de crédito, para completar a transação em um momento posterior.                                                                                                               |
| Pagamento sem um token                    | PagoTokenSaleTransaction                         | Use este requestType para processar uma transação com um token gerado anteriormente.                                                                                                                                                       |
| Reembolso com Token                       | PaymentTokenCreditTransaction                    | Use este requestType para processar um reembolso ao portador do cartão de débito ou crédito usando um token gerado anteriormente.                                                                                                          |
| Token de pré-autorização                  | PaymentTokenPreAuthTransaction                   | Use este requestType para pré-autorizar um valor, usando um token, para completar a transação em um momento posterior.                                                                                                                     |
| Pagamentos SEPA                           | SepaSaleTransaction                              | Pagamentos SEPA	SepaSaleTransaction	Use este requestType para processar um pagamento via SEPA.                                                                                                                                          |
| Venda Carteira Digital                    | WalletSaleTransaction                            | Use este requestType para processar um pagamento regular com uma carteira digital.                                                                                                                                                         |
| Pré-autorização carteira digital          | WalletPreAuthTransaction                         | Use este requestType para processar uma pré-autorização usando uma carteira digital.                                                                                                                                                       |

Para transações primárias, a matriz de decisão para definir qual requestType usar está indicada abaixo:

![Request Type Decision Matrix!](/assets/images/3-1-decision-matrix.png "Request Type Decision Matrix")

As diferenças requeridas no JSON de cada um desses tipos de requisição estão explicadas no restante desse guia. 

---

## Veja também

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
