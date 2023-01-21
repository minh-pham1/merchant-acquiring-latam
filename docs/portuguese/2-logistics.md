---
tags: [Logistics, API, LATAM]
---

# Logistics

## Latam Merchant Logistics APIs

No ramo de meios de pagamento, os provedores de field services tem um papel importante para habilitar novos estabelecimentos, através da instalação de terminais, entrega de bobinas, para dar assistência a terminais com defeito, substituindo SimCards e retirando terminais dos estabelecimentos sempre que solicitado. Para gerar uma “Service Order” a um provedor de field services, Fiserv entrega um conjunto de APIs, detalhadas neste documento, classificadas em duas sessões, sendo a primeira parte dedicada as instituições financeiras que prestam assistência a seus estabelecimentos e a segunda parte dedicada a ajudar um provedor de field services que queira integrar se com a Fiserv.

---

<!--
type: tab
titles: Financial Institution, Field Services Provider
-->

Em diferentes momentos um estabelecimento pode pedir serviços que precisem de intervenção de um dos nossos provedores de field services e aqui detalhamos alguns conceitos que requerem atenção antes de aprender como usar as APIs.

(i)	Deve existir um estabelecimento ativo para permitir a requisição de instalação de um terminal e a identificação deste estabelecimento deve ser conhecida previamente,
(ii)	Deve existir um terminal ativo para o estabelecimento selecionado para gerar um pedido de bobinas e a identificação dos dois deve ser conhecida previamente,
(iii)	Deve existir pelo menos um terminal ativo para o estabelecimento selecionado para pedir sua retirada. Isso implica que não se pode pedir, para eventos sazonais, um terminal para instalação em uma data futura e, ao mesmo tempo, pedir sua retirada para algumas semanas ou meses depois. A “Service Order” de retirada somente se pode gerar quando o terminal está instalado no estabelecimento.
(iv)	É obrigatório para o sucesso de uma “Service Order” que o endereço do estabelecimento esteja confirmado com ele antes de gerar o pedido, uma vez que esses dados não se alteram na sequência.
(v)	Devido aos meios de integração entre Fiserv e seus provedores de field services, as atualizações de cada “Service Order” ocorrem quase que em tempo real, o que ajuda a informar os dados sempre mais atualizados.
(i)	Para utilizar as APIs detalhadas neste documento, um processo de registro nesse portal de desenvolvedor deve estar concluído. Esse processo vai gerar as credenciais requeridas para uso como parâmetros durante uma chamada de API.

O diagrama a seguir ilustra os momentos em que cada API deve ser utilizada

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

As principais APIs disponíveis para uso pela Instituição Financeira são:

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

<!--
type: tab
-->

A Fiserv não repassa a seus provedores de field services acesso à sua ferramenta de gestão de “Service Orders” (TMP), uma vez que essa ferramenta não se especificou para elaborar rotas, manter documentação requerida para a conclusão ou cancelamento de uma Service Order. A Fiserv espera que seus provedores de field services utilizem suas plataformas internas e integrem um conjunto específico de controles para dar visibilidade das “Service Orders” em seu poder e para garantir rastreamento dos equipamentos da Fiserv ou das Instituições Financeiras associadas guardados com o provedor de field services.

Existem duas formas através das quais essa integração se realizará. A primeira é através de APIs geradas para (i) incorporar novos “Service Orders” da Fiserv na ferramenta do provedor, (ii) para atualização de status entre Fiserv e o provedor, (iii) para registrar o sucesso ou o cancelamento de uma “Service Order” baseado em um conjunto de opções predeterminadas, (iv) para comunicar a entrada de novos terminais, acessórios, etc.

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

O segundo método será através de troca de arquivos XML, uma vez ao dia, no qual a Fiserv espera que seus provedores de field services comuniquem toda mudança de localização de um terminal do dia anterior, incorporando (i) terminais ou SimCards transferidos de um estoque central a um posto avançado e vice-versa, (ii) terminais ou SimCards transferidos de um posto avançado para estabelecimentos e vice-versa, (iii) terminais ou SimCards transferidos de “triagem” para laboratório e vice-versa.

<!-- type: tab-end -->

---
