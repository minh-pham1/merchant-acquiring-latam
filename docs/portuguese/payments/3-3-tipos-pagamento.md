---
tags: [Getting Started, Payments, Payment Types, Card Brands]
---

# Tipos de pagamento

*Essa página traz detalhes das formas de pagamento mais comuns que são referenciadas na maioria das nossas APIs.*

Há muitos tipos diferentes de 'instrumentos de pagamentos', itens ou mecanismos que um provedor utiliza para pagar uma outra parte. As principais categorias destes nas nossas APIs são:

- **Cartão** - um meio físico ou digital, organizado sobre a estrutura de uma Bandeira como Visa e contém informações como o número do cartão e sua data de expiração. Os dados requeridos e guardados nesse caso respeitam um conjunto de regras.
- **Dinheiro** - tradicional, muita pouca informação sobre o instrument pode ser armazenada nesse caso.
- **Alternativa** - uma forma alternativa de pagamento, como carteiras digitais tais como Google Pay, PayPal, ou dados de cartões tokenizados. Os dados guardados aqui podem variar dependendo do serviço utilizado.

## Bandeiras

Veja a tabela seguir para mais detalhes da combinação de bandeiras mencionadas nas nossas APIs. Note que não necessariamente todas as APIs operam com todas as bandeiras.

| Nosso código    | Nome             | Descrição                                                                                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```VISA```      | Visa             | Bandeira mais comum, aceita em quase todos os lugares.                                                                                                      |
| ```MC```        | MasterCard       | Segunda Bandeira mais comum, aceita em quase todos os lugares.                                                                                              |
| ```MAESTRO```   | Maestro          | Marca dos cartões de débito da Mastercard, a ser decomissionada a partir de 2023, [Iniciando na Europa](https://en.wikipedia.org/wiki/Maestro_(debit_card)) |
| ```DINERS```    | Diners Club      | Bandeira gerenciada por Discover.                                                                                                                           |
| ```CUP```       | China UnionPay   | Rede de bancos nacional da China.                                                                                                                           |
| ```JCB```       | JCB              | Bandeira internacional baseada no Japan, que funciona com Discover e AMEX.                                                                                  |
| ```AMEX```      | American Express | Bandeira com regime de pagamento fechado.                                                                                                                   |
| ```PP```        | Polcard Prepaid  | Um tipo de cartão pré-pago usado na Polônia.                                                                                                                |
| ```PRIV```      | Private Label    | Um cartão que foi incorporado por outra companhia, geralmente associado a outra bandeira.                                                                   |
| ```CARTBLANC``` | Carte Bancaire   | Rede de cartões locais na França.                                                                                                                           |
| ```GIRO```      | Giro Card        | Rede de cartões locais na Alemanha.                                                                                                                         |
| ```PAGO```      | Pago             | Rede de cartões locais na Itália.                                                                                                                           |
| ```VPAY```      | vPay             | Cartão de débito SEPA, geralmente em parceria com outras redes como Giro na Alemanha.                                                                       |
| ```RUPAY```     | RuPay            | Rede de pagamentos global, sediada na India.                                                                                                                |

## Métodos de pagamento alternativos

Exemplos de métodos de pagamento alternativos (comumente abreviados para APM's) são como a seguir:

| Nosso código    | Nome                                      | Descrição                                                                         |
|-----------------|-------------------------------------------|-----------------------------------------------------------------------------------|
| ```BLIK```      | blik                                      | Sistema de pagamentos móveis / instantâneo usado na Polonia.                      |
| ```SEPADD```    | SEPA Direct Debit                         | Uma solução de pagamento instantâneo de acordo com a regulação SEPA Direct Debit. |
| ```ELV```       | ELV (Elektronisches Lastschriftverfahren) | Método de pagamentos de débito em operação por bancos na Alemanha.                |
| ```POSTFIN```   |                                           | Método de pagamento bancário direto.                                              |
| ```IDEAL```     | iDeal                                     | Método de pagamento online via SEPA.                                              |
| ```SOFORT```    | Sofort                                    | Solução de pagamento exclusiva.                                                   |
| ```PAYPAL```    | PayPal                                    | Carteira digital mais comumente utilizada.                                        |
| ```PBL```       | Pay-by-link                               | Método utilizado para gerar links de pagamento para cobrança a clientes.          |
| ```VIPPS```     | Vipps                                     | Método de pagamento móvel usado na Noruega.                                       |
| ```EASYCASH```  | EasyCash                                  | Carteira digital / móvel.                                                         |
| ```MOBILEPAY``` | Mobile Pay                                | Carteira digital / móvel.                                                         |
| ```TWINT```     | TWINT                                     | Carteira digital / móvel.                                                         |
| ```SWISH```     | Swish                                     | Carteira digital / móvel usada na Suécia.                                         |
| ```ALIPAY```    | AliPay                                    | Carteira digital / móvel usada na China.                                          |
| ```WECHATPAY``` |                                           | Carteira digital / móvel usada na China.                                          |

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
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