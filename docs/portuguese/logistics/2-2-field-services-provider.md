---
tags: [Field Services Provider, Logistics, API, LATAM]
---

# Field Services Provider

A Fiserv não repassa a seus provedores de field services acesso à sua ferramenta de gestão de “Service Orders” (TMP), uma vez que essa ferramenta não se especificou para elaborar rotas, manter documentação requerida para a conclusão ou cancelamento de uma Service Order. A Fiserv espera que seus provedores de field services utilizem suas plataformas internas e integrem um conjunto específico de controles para dar visibilidade das “Service Orders” em seu poder e para garantir rastreamento dos equipamentos da Fiserv ou das Instituições Financeiras associadas guardados com o provedor de field services.

Existem duas formas através das quais essa integração se realizará. A primeira é através de APIs geradas para 1. incorporar novos “Service Orders” da Fiserv na ferramenta do provedor, 2. para atualização de status entre Fiserv e o provedor, 3. para registrar o sucesso ou o cancelamento de uma “Service Order” baseado em um conjunto de opções predeterminadas, 4. para comunicar a entrada de novos terminais, acessórios, etc.

As principais APIs entregues para utilização do provedor de field services são:

|API|	Responsável API	|	Usuário da API|	Descrição|
|---|-------------|---------|-----------|
|Ticket update|	Provedor field services|	Fiserv|	API para habilitar a criação de Service Orders na plataforma do provedor de field services |
|New ticket|	Provedor field services|	Fiserv|	API para habilitar a atualização de Service Orders na plataforma do provedor de field services |
|Uninstall|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders para pedir retirada de terminais|
|Supply request|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders para solicitação de bobina|
|Re-schedule|	Fiserv|	Instituição Financeira e Provedor field services|	API para habilitar a atualização de Service Orders (exclusivamente para reprogramação de datas)|
|Order Update|	Fiserv|	Field Services provider|	API para habilitar atualização de Service Orders associadas a um provedor de field services|
|Maintenance / Replacement|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders pedindo uma troca de terminal|
|Maintenance / Change Technology|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders pedindo a troca de tecnologia de um terminal|
|Maintenance / Change Carrier|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders pedindo uma troca de SimCard|
|Install|	Fiserv|	Instituição Financeira|	API para habilitar a criação de Service Orders pedindo a instalação de um terminal|
|Cancel order|	Fiserv|	Instituição Financeira e Provedor field services|	API para habilitar o cancelamento de uma Service Order aberta previamente|
|Ticket List|	Fiserv|	Instituição Financeira|	API para habilitar a consulta de Service Orders associadas a um estabelecimento em particular|
|Terminal List|	Fiserv	|Instituição Financeira|	API para habilitar a consulta de terminais associados a um estabelecimento em particular|
|Product|	Fiserv|	Instituição Financeira|	API para habilitar a consulta de terminais associados a um estabelecimento em particular|
|Ticket List details|	Fiserv|Instituição Financeira e Provedor field services	|API para habilitar a consulta de detalhes de uma Service Order de acordo ao seu número de registro|

Pré-requisito para a utilização destas APIs é a aquisição de um certificado digital privado, que será detalhado nesse material.

O segundo método será através de troca de arquivos XML, uma vez ao dia, no qual a Fiserv espera que seus provedores de field services comuniquem toda mudança de localização de um terminal do dia anterior, incorporando 1. terminais ou SimCards transferidos de um estoque central a um posto avançado e vice-versa, 2. terminais ou SimCards transferidos de um posto avançado para estabelecimentos e vice-versa, 3. terminais ou SimCards transferidos de “triagem” para laboratório e vice-versa.

---

## See Also

- [Financial Institution](?path=docs/portuguese/logistics/2-1-financial-institution.md)

---
