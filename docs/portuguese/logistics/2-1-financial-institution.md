---
tags: [Financial Institution, Logistics, API, LATAM]
---

# Financial Institution

Em diferentes momentos um estabelecimento pode pedir serviços que precisem de intervenção de um dos nossos provedores de field services e aqui detalhamos alguns conceitos que requerem atenção antes de aprender como usar as APIs.

1.	Deve existir um estabelecimento ativo para permitir a requisição de instalação de um terminal e a identificação deste estabelecimento deve ser conhecida previamente,
2. Deve existir um terminal ativo para o estabelecimento selecionado para gerar um pedido de bobinas e a identificação dos dois deve ser conhecida previamente,
3.	Deve existir pelo menos um terminal ativo para o estabelecimento selecionado para pedir sua retirada. Isso implica que não se pode pedir, para eventos sazonais, um terminal para instalação em uma data futura e, ao mesmo tempo, pedir sua retirada para algumas semanas ou meses depois. A “Service Order” de retirada somente se pode gerar quando o terminal está instalado no estabelecimento.
4. É obrigatório para o sucesso de uma “Service Order” que o endereço do estabelecimento esteja confirmado com ele antes de gerar o pedido, uma vez que esses dados não se alteram na sequência.
5.	Devido aos meios de integração entre Fiserv e seus provedores de field services, as atualizações de cada “Service Order” ocorrem quase que em tempo real, o que ajuda a informar os dados sempre mais atualizados.
6.	Para utilizar as APIs detalhadas neste documento, um processo de registro nesse portal de desenvolvedor deve estar concluído. Esse processo vai gerar as credenciais requeridas para uso como parâmetros durante uma chamada de API.

O diagrama a seguir ilustra os momentos em que cada API deve ser utilizada

![image](https://user-images.githubusercontent.com/111396588/213576554-0bda6765-1bdf-4a55-9770-11b293dfd999.png)

As principais APIs disponíveis para uso pela Instituição Financeira são:

| API                             | Responsável API         | Usuário da API                                   | Descrição                                                                                          |
|---------------------------------|-------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Ticket update                   | Provedor field services | Fiserv                                           | API para habilitar a criação de Service Orders na plataforma do provedor de field services         |
| New ticket                      | Provedor field services | Fiserv                                           | API para habilitar a atualização de Service Orders na plataforma do provedor de field services     |
| Uninstall                       | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders para pedir retirada de terminais                    |
| Supply request                  | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders para solicitação de bobina                          |
| Re-schedule                     | Fiserv                  | Instituição Financeira e Provedor field services | API para habilitar a atualização de Service Orders (exclusivamente para reprogramação de datas)    |
| Order Update                    | Fiserv                  | Field Services provider                          | API para habilitar atualização de Service Orders associadas a um provedor de field services        |
| Maintenance / Replacement       | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders pedindo uma troca de terminal                       |
| Maintenance / Change Technology | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders pedindo a troca de tecnologia de um terminal        |
| Maintenance / Change Carrier    | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders pedindo uma troca de SimCard                        |
| Install                         | Fiserv                  | Instituição Financeira                           | API para habilitar a criação de Service Orders pedindo a instalação de um terminal                 |
| Cancel order                    | Fiserv                  | Instituição Financeira e Provedor field services | API para habilitar o cancelamento de uma Service Order aberta previamente                          |
| Ticket List                     | Fiserv                  | Instituição Financeira                           | API para habilitar a consulta de Service Orders associadas a um estabelecimento em particular      |
| Terminal List                   | Fiserv                  | Instituição Financeira                           | API para habilitar a consulta de terminais associados a um estabelecimento em particular           |
| Product                         | Fiserv                  | Instituição Financeira                           | API para habilitar a consulta de terminais associados a um estabelecimento em particular           |
| Ticket List details             | Fiserv                  | Instituição Financeira e Provedor field services | API para habilitar a consulta de detalhes de uma Service Order de acordo ao seu número de registro |

---

## See Also

- [Field Services Provider](?path=docs/portuguese/logistics/2-2-field-services-provider.md )

---
