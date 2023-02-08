---
tags: [Getting Started, Payments, 3-D Secure, Frictionless Flow, Challenge Flow]
---

# 3-D Secure

Quando você utiliza nosso Gateway e a Fiserv como seu provedor de 3D Secure, a autenticação é realizada em paralelo com o fluxo transacional existente. O processo é iniciado pela execução de uma típica autorização ou pedido de venda que pede a validação 3D Secure na requisição.

A autorização então é colocada em um estágio de ```WAITING``` até que o processo de autenticação esteja concluído. Durante essa etapa, pode ser necessário que o estabelecimento tenha de atualizar a transação original uma ou mais vezes, para que consiga seguir o processo de aprovação.

Ao final do processo de autenticação, a transação original é atualizada com o resultado do processo de autenticação e então, finalizada.

O diagrama de sequência abaixo indica os passos a seguir. O primeiro diagrama representa o fluxo simples. Isso significa que o emissor não requer nenhuma autenticação do portador do cartão.

![Sequence diagrama for frictionless flow!](/assets/images/3-5-1-3ds-frictionless.png "Frictionless flow")

O próximo diagrama mostra o fluxo quando o cliente precisa autenticar, o que significa que o emissor pediu dados adicionais para processar a transação.

![Sequence diagrama for authenticate!](/assets/images/3-5-2-3ds-auth.png "Authenticate flow")

## Como implementar 3-D Secure com a nossa API

### Passo 1: Inicie uma transação de pagamento

Utilize ou um cartão de débito / crédito ou um token de pagamento para iniciar uma transação de pagamento.

Voce pode direcionar o pagamento para usar 3D Secure se desejar. Os requestTypes para autenticação com 3d Secure são:

- PaymentCardPreAuthTransaction
- PaymentCardSaleTransaction
- PaymentTokenPreAuthTransaction
- PaymentTokenSaleTransaction
- PaymentCardPayerAuthTransaction

Essa mensageria precisa incluir o objeto ```authenticationRequest``` no corpo da mensagem e indicar os seguintes valores:

| Atributo                   | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```   | O valor “Secure3DAuthenticationRequest” é padrão para pedidos de autenticação 3DS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ```termURL```              | Indica a URL de resposta na qual os resultados do processo de autenticação devem ser divulgados pelo servidor ACS (este é Servidor de Controle de Acesso que executa a autenticação do portador do cartão).                                                                                                                                                                                                                                                                                                                                          |
| ```methodNotifictionURL``` | Para ser notificado em tela sobre a conclusão do processo 3DSMethod, você precisa indicar esse elemento na sua requisição de transação. A URL precisa ter um identificador único, assim quando uma notificação for recebida nesta URL, você conseguirá correlacioná-la com a transação original. Isso elimina qualquer dependência do campo Secure3dTransId que você receberá como resposta do método 3DSMethod. Um jeito fácil de garantir o relacionamento com a transação correta é passar a referência da transação como uma string na consulta. |
| ```challengeIndicator```   | Se você quiser indicar o fluxo de autenticação a ser usado, você pode submeter esse elemento opcional com um dos valores listados abaixo. Caso esse indicador não seja enviado na transação original, o Gateway vai indicar o valor padrão “01” – sem preferência.                                                                                                                                                                                                                                                                                   |
| ```challengeWindowSize```  | Se você quiser definir o tamanho da janela de autenticação mostrada para os seus clientes durante esse processo, você pode listar esse elemento opcional com um dos valores listados abaixo.                                                                                                                                                                                                                                                                                                                                                          |

Valores disponíveis para o ```challengeIndicator``` são:

- 01 = No preference (You have no preference whether a challenge should be performed. This is the default value)
- 02 = No challenge requested (You prefer that no challenge should be performed.)
- 03 = Autenticação requerida: Obrigatório (Existem locais / regiões em que a autenticação é obrigatória), 
- 04 = Autenticação requerida: Obrigatório (Existem locais / regiões em que a autenticação é obrigatória), 
- 05 = Sem requisição de autenticação (A análise de risco da transação já foi feita), 
- 06 = Sem requisição de autenticação (Apenas compartilhamento de dados), 
- 07 = Sem requisição de autenticação (A SCA já foi feita), 
- 08 = Sem requisição de autenticação (Para utilização com listas de exceção - whitelists),
- 09 = Autenticação requerida (Para utilização com listas de exceção - whitelists)

Valores disponíveis para a ```challengeWindowSize``` são:

- 01 = 250 x 400
- 02 = 390 x 400
- 03 = 500 x 600
- 04 = 600 x 400
- 05 = Tela inteira

O seguinte documento JSON mostra um exemplo de uma transação de venda com um número mínimo de elementos:

```json
{
  "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "122.04",
      "currency": "USD"
      },
    "paymentMethod": {
      "paymentCard": {
        "number": "403587XXXXXX4977",
        "securityCode": "977",
        "expiryDate": {
        "month": "12",
        "year": "24"
      }   
    }
  },
  "authenticationRequest": {
    "authenticationType": "Secure3D21AuthenticationRequest",
    "termURL": "https://www.mywebshop.com/process3dSecure",
    "methodNotificationURL": "https://www.mywebshop.com/process3dSecureMethodNotification?transactionReferenceNumber=ffffffff-ba0b-539f-8000-016b2343ad7e",
    "challengeIndicator": "01",
    "challengeWindowSize": "01"
  }
}
```

Note que nem todos os emissores trabalham com a coleta de dados de browser usando o formulário 3DSMethod. Nestes casos, nenhum dado vai ser publicado no campo methodNotificationURL e o fluxo deve continuar com a postagem de um status ```EXPECTED_BUT_NOT_RECEIVED```, como abaixo:

### Passo 2: Resposta da autenticação 3-D Secure

Nossa resposta incluirá um elemento ```3DSMethod```, que gera um iframe oculto para coletar dados do browser do consumidor para os adquirentes. Esses dados são agregados ao seu perfil geral e ajudam na identificação de transações potencialmente fraudulentas, que aumenta a probabilidade de menores atritos, transações com mais êxito. 

Você precisará incluir o método ```3DSMethod``` no seu website como um iframe oculto. Nenhuma tela de interface será apresentada ao portador do cartão.

Neste ponto, ocorre uma verificação para determiner se o Sistema 3D Secure está ativo e se o portador do cartão está cadastrador para o 3D Secure. Se o sistema não estiver ativo ou o portador do cartão não estiver habilitado, a transação será processada normalmente e aprovada ou rejeitada pela rede de processamento.

No caso acima, o status da transação aparecerá da seguinte forma:

```transactionStatus = `APPROVED` || `DECLINED` ```

Se o portador do cartão estiver habilitado no programa 3d Secure, então o objeto 'authenticationResponse' será incluído na resposta da transação.

Enquanto aguardamos a resposta da transação, ela poderá ter os seguintes status:

```transactionStatus = WAITING```

O objeto ```authenticationResponse``` poderá conter os seguintes valores:

| Atributo                             | Valor                                                                               |
|--------------------------------------|-------------------------------------------------------------------------------------|
| ```type```                           | 3D_SECURE                                                                           |
| ```version```                        | 2.1 ou 2.2                                                                          |
| ```secure3DMethod/methodForm```      | Formulário HTM com o iframe oculto para coletar os dados do browser para o emissor. |
| ```secure3DMethod/secure3dTransId``` | Um identificador único para a transação entregue pelo servidor ACS do emissor.      |

O documento JSON abaixo apresenta um exemplo de resposta:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
  "total": 122.04,
  "currency": "USD"
},
  "transactionStatus": "WAITING",
  "authenticationResponse": {
    "type": "3D_SECURE",
    "version": "2.1",
    "secure3dMethod": {
      "methodForm": "&lt;!DOCTYPE iframe SYSTEM "about:legacy-compat"&gt;
      &lt;iframe id="tdsMmethodTgtFrame" name="tdsMmethodTgtFrame"
      style="width: 1px; height: 1px; display: none;" src="javascript:false;"
      xmlns="http://www.w3.org/1999/xhtml"&gt;
      &lt;!--.--&gt; &lt;/iframe&gt;&lt;form id="tdsMmethodForm"
      name="tdsMmethodForm"
      action=https://localhost.modirum.com:8543/dstests/ACSEmu2
      method="post"
      target="tdsMmethodTgtFrame" xmlns="http://www.w3.org/1999/xhtml"&gt;
      &lt;input type="hidden" name="3DSMethodData"
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2My
      04MDAwLTAwMDAw    &amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9
      uVVJMIiA6ICJodHRwczovL2xvY2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL
      01lcmNoYW50U2VydmVyP21uPVkmdHhpZD0x
      &amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcVJRbllqNmozbWFDZlFJbTdFdjJYTm
      kwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt; &lt;input type="hidden"
      name="threeDSMethodData"            
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2
      My04MDAwLTAwMDA
      w&amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9uVVJMIiA
      6ICJodHRwczovL2xvY 2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL01lcm
      NoYW50U2VydmVyP21uPVkmdHhpZD0x&amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcV
      JRbllqNmozbWFDZlFJbTdFdjJYTmkwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt;
      &lt;/form&gt;&lt;script type="text/javascript" 
      xmlns="http://www.w3.org/1999/xhtml"&gt;
      document.getElementById("tdsMmethodForm").submit(); &lt;/script&gt;",
      "secure3dTransId": "3ac7caa7-aa42-2663-791b-2ac05a542c4a"
    }
  }
}
```

### Passo 3: Requisição de notificação e resposta 3DSMethod

O 'methodForm' 3d Secure é usado para fornecer detalhes do ambiente do portador do cartão para o Servidor ACS do emissor. O ```methodForm``` contém o HTML de um iframe oculto que é incluído na sua página web. Ele vai forçar essa informação a ser automaticamente publicada no servidor ACS pela Fiserv. A informação do HTML é um bloco HTML embutido que não precisa ser modificado ou publicado, uma vez que ele será tratado automaticamente quando a página na qual ele foi inserido é atualizada. De forma alternativa, ele pode ser criado em uma página que nunca ficará visível para o portador do cartão.

Se recebida adequadamente, os dados de resposta serão publicados na URL entregue no campo ```methodNotificationURL``` e a corpo da mensagem conterá um campo, ```threeDSServerTransID```, contendo o ID único da transação ACS relacionada com o pedido original. Observe que o corpo dessa resposta conterá um elemento chamado ```threeDSMethodData```. Esse elemento conterá o JSON codificado padrão base64 que contém o campo ```threeDSServerTransID```.

Exemplo:

```xml
<form name="frm" method="POST" action="{value from methodNotificationURL}">
  <input type="hidden" name="threeDSMethodData" value="eyJ0aHJlZURTU2VydmVyVHJhbnNJRCI6IjNhYzdjYWE3LWFhNDItMjY2My03OTFiLTJhYzA1YTU0MmM0YSJ9">
</form>
```

threeDSMethodData decodificado:

```json
{"threeDSServerTransID":"3ac7caa7-aa42-2663-791b-2ac05a542c4a"}
```

O campo ```threeDSServerTransID``` não é mandatório para o processamento 3DS. Entretanto, é recomendável guardar esse valor para referência futura no servidor 3DS se necessário.

Você deve aguardar um mínimo de **10 segundos** para a operação acima de POST ser terminado e então determinar o methodNotificationStatus como a seguir:

| Status                    | Descrição                                                                                                                                                                                                                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RECEIVED                  | Você submeteu o elemento ```methodNotificationURL``` na transação original de venda e recebeu uma notificação do servidor ACS. Em 10 segundos, você receberá um HTTP POST do servidor ACS, que conterá o identificador único da transação indicada no campo secure3dTransId. |
| EXPECTED_BUT_NOT_RECEIVED | Você submeteu o elemento ```methodNotificationURL``` na transação original de venda e não recebeu resposta do servidor ACS em 10 segundos.                                                                                                                                   |
| NOT_EXPECTED              | Você não submeteu o elemento ```methodNotificationURL``` na transação original de venda.                                                                                                                                                                                     |

> 🚧 Há várias ocasiões nas quais você perceberá respostas duplicadas para a sua '3DSMethod Notification URL' ou 'Term URL', isso pode acontecer devido a duplicidade de respostas dos servidores ACS ou de comportamento do browser do consumidor. Recomendamos que você construa tratamentos desses eventos em seu sistema, assim, caso você receba respostas duplicadas para o seu '3DSMethod Notification URL' ou 'Term URL' você não precisa enviar um processamento duplicado para o Gateway.
<!-- theme: warning -->

## Fluxo simples

### Passo 4: Requisição para proceder com autenticação 3DS

Quando a chamada do método ‘3DSMethod’ estiver concluída, você precisa notificar o Gateway que o processo de autenticação pode prosseguir através do envio do elemento 'methodNotificationStatus' com os valores de acordo as condições correspondentes do método ‘3DSMethod’ acima. Isso é feito através de uma operação de **PATCH** na transação original.

Você também pode incluir os dados de endereço do portador do cartão e o código de segurança nesse momento.

O documento JSON abaixo mostra um exemplo de requisição a ser enviada depois do processamento do método ```3DSMethod```:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "billingAddress": {
  "company": "Test Company",
  "address1": "5565 Glenridge Conn",
  "address2": "Suite 123",
  "city": "Atlanta",
  "region": "Georgia",
  "postalCode": "30342",
  "country": "USA"
},
  "securityCode": "123",
  "methodNotificationStatus": "RECEIVED"
}
```

### Passo 5: Resposta Final 3DS

Quando foi determinado que o fluxo simples deverá ser executado (exemplo: o cliente foi plenamente autenticado pelo seu banco sem a necessidade de qualquer intervenção), o processo 3D Secure estará completo e a autorização da transação é processada.

A resposta da transação contém o objeto ‘secure3dResponse’ e a transação pode ser aprovada ou rejeitada.

```transactionStatus = APPROVED or DECLINED```

O objeto 'secure3dResponse' conterá o seguinte campo: 'responseCode3dSecure'

O documento JSON a seguir representa um exemplo de resposta que você deve receber da API indicando que a autorização foi processada com sucesso:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "APPROVED",
  "schemeTransactionId": "019078743804756",
  "processor": {
    "responseCode": "00",
    "responseMessage": "APPROVED",
    "authorizationCode": "OK7118"
  },
  "secure3dResponse": {
    "responseCode3dSecure": "1"
  }
}
```

## Fluxo autenticado

O fluxo autenticado é acionado quando a transação original de venda não foi classificada como de baixo risco ou o emissor pede uma autenticação extra para o portador do cartão. O processo é iniciado quando um pedido de pré-autorização ou de venda usando o método 3DSMethod é acionado, como descrito nos passos 1 a 4 indicados acima.

### Passo 6: Requisição para proceder com a autenticação 3DS

Uma vez que a chamada “3DSMethod” foi concluída, você precisa notificar o Gateway que o processo de autenticação pode continuar, submetendo o elemento 'methodNotificationStatus' com os valores de acordo as condições correspondentes do método ‘3DSMethod’ acima. Isso é feito através de uma operação de **PATCH** na transação original.

Você também pode incluir os dados de endereço do portador do cartão e o código de segurança nesse momento.

O documento JSON abaixo mostra um exemplo de requisição a ser enviada depois do processamento do método 3DSMethod:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "RECEIVED"
}
```

### Passo 7: Resposta do Gateway para seguir com a autenticação 3DS

Para o fluxo autenticado, o status da transação será devolvido como a seguir: 

```transactionStatus = "WAITING"```

A resposta deverá conter o objeto ```authenticationResponse``` com os seguintes campos:

| **Campo**         | Descrição                                                                                                                         |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| ```type```        | 3D_SECURE                                                                                                                         |
| ```version```     | 2.1 ou 2.2                                                                                                                        |
| ```acsURL```      | A URL para a qual os valores 'cReq' e 'sessionData' devem ser publicados para o portador do cartão se autenticar.                 |
| ```termURL```     | A URL para qual os resultados da autenticação serão publicados.                                                                   |
| ```cReq```        | A mensagem de autenticação codificada repassada pelo servidor ACS.                                                                |
| ```sessionData``` | Uma lista codificada de parâmetros de sessão a serem usados para autenticação. Note que esse valor pode nem sempre ser devolvido. |

O documento JSON a seguir mostra um exemplo de resposta:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "WAITING",
  "authenticationResponse": {
    "type": "3D_SECURE",
    "version": "2.1",
    "params": {
      "acsURL": "https://3ds-acs.test.modirum.com/mdpayacs/pareq",
      "termURL": "https://www.mywebshop.com/process3dSecure/",
      "cReq": "ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9",
      "sessiondata": "50F2156E03083CA665BCB4.."
    }
  }
}
```

### Passo 8: Autenticação do portador do cartão

No próximo passo, você precisa enviar um POST dos dados para a ```acsURL``` indicada, geralmente implementada por um formulário de autoenvio. Isso precisa ser implementado no seu website. O portador do cartão será redirecionado para o ACS e receberá a interface de usuário (UI) para coletar os detalhes de autenticação – por exemplo para entrar uma senha única ou realizar uma autenticação com o seu aplicativo do banco. Quando a autenticação for concluída, o consumidor é redirecionado para a sua página web.

Você então precisa fazer um POST dos valores de ```cReq``` e ```sessionData``` na URL especificada no campo ```acsURL```.

Essas informações são publicadas usando os seguintes nomes de campo:

| **```cReq```**               | A mensagem completa do campo cReq codificada base64 como obtida acima.        |
|------------------------------|-------------------------------------------------------------------------------|
| **```threeDSSessionData```** | A mensagem completa do campo sessionData codificada base64 como obtida acima. |

Exemplo:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.modirum.com/mdpayacs/pareq ">
  <input type=”hidden” name=”creq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

Quando a autenticação é concluída, uma resposta será publicada para a URL indicada no campo 'termURL'.

### Passo 9: Requisição para completer uma transação

Depois que você recebeu os dados da ACS, você precisa submetê-los para o Gateway no elemento ```cRes``` junto com a referência da transação original. Isso é feito através do envio de uma requisição de **PATCH** para a transação original e inclui os seguintes valores:

| **```authenticationType```** | **Secure3D21AuthenticationUpdateRequest**                                  |
|------------------------------|----------------------------------------------------------------------------|
| **```acsResponse/cRes```**   | Os dados do campo ```cRes``` publicados na ```termURL``` pelo servidor ACS.|

É altamente recomendável incluir os dados opcionais de endereço de cobrança e o código de segurança nesse momento.

O documento JSON a seguir mostra um exemplo de requisição com o elemento ‘cRes’:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "billingAddress": {
    "company": "Test Company",
    "address1": "5565 Glenridge Conn",
    "address2": "Suite 123"
    "city": "Atlanta",
    "region": "Georgia",
    "postalCode": "30342",
    "country": "USA"
  },
  "securityCode": "123",
  "acsResponse": {
    "cRes": "ewogICAiYWNzUmVmZX…Fuc1N0YXR…IKfQ=="
  }
}
```

### Passo 10: Resposta final

Uma vez que essa transação foi iniciada como uma venda, a autorização é realizada como parte desta etapa final, caso a autenticação tenha sido exitosa.

A transação de resposta contém um objeto ```secure3dResponse``` e ela será aprovada ou negada.

O objeto secure3dResponse conterá o seguinte campo: ```responseCode3dSecure```

O documento JSON a seguir representa um exemplo de resposta que você deve receber indicando que a autorização foi exitosa:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "APPROVED",
  "schemeTransactionId": "019078743804756",
  "processor": {
    "responseCode": "00",
    "responseMessage": "APPROVED",
    "authorizationCode": "OK7118"
  },
  "secure3dResponse": {
    "responseCode3dSecure": "1"
  }
}
```

## Fallback para 3DS 1.0

Para alguns casos, quando o emissor não suporta o protocolo EMV 3DS, o Gateway entrega uma opção de "fallback" para a autenticação 3DS 1.0.

Como primeiro passo, você precisa publicar um **POST** na API para o protocolo EMV 3DS:

```json
{
  "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "12.99",
      "currency": "EUR"
      },
    "paymentMethod": {
      "paymentCard": {
        "number": "403587XXXXXX4977",
        "securityCode": "999",
        "expiryDate": {
        "month": "12",
        "year": "24"
      }  
    }
  },
  "authenticationRequest": {
    "authenticationType": "Secure3D21AuthenticationRequest",
    "termURL": "https://test.ipg-online.com/webshop/simulator/secure3d/return",
    "methodNotificationURL": "https://test.ipg-online.com/ipgconfirmation/services/secure3ds",
    "challengeIndicator": "01",
    "challengeWindowSize": "01"
  }
}
```

Você vai receber uma reposta do Gateway indicando que o cartão de crédito está habilitado apenas para a versão 1 do 3DS:

```json
{
   "clientRequestId": "55351544-749a-4c03-9e35-3e240ea5572d",
   "apiTraceId": "YKUh6bqtrBfPwvICng2wBgAAATM",
   "ipgTransactionId": "84563547902",
   "orderId": "R-ff489e2d-b5f3-4757-a91b-0a774681e26a",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "403587",
         "last4": "4977",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "Germany",
   "transactionTime": 1621434858,
   "transactionStatus": "WAITING",
   "authenticationResponse":    {
      "type": "3D_SECURE",
      "version": "1.0",
      "params":       {
         "payerAuthenticationRequest": "eJxVUdtygjAQ/RXG184QEvASZ80M1V54aAcRP4BCWqgSNAlW/r4JeGn3ac/Jnt3NWUhLyflqw/NWcgZvXKnsiztVsRjFyXo88Yg3xSMGcZjwI4MTl6pqBMOu5xJAV2h0Mi8zoRlk+fExemfjPgBdINRcRitGqWfCBzRAEFnNWRS/OClX2vFXG0A9BXnTCi07NiMTQFcArdyzUuuDmiOkjcL1C+V+VlLpItOZmzc1IFsD6L5P3NpMmZ7nqmBxkW4flj80weK7DE9h130872b7VES7BSBbAaYVZ8Qj2Btj6uBg7gdzPAPU85DVdhn2tE0cTFxKzQ8HBg52UDgATOzLXwaMv5KLvGN0aprdEPDzoRHcVBg7bzmg+9rLV2tqro1f2Pc96uMAW197xsorYw0J8KC3AJDVoMvJ0OWmJvt361+B66YO",
         "termURL": "https://test.ipg-online.com/webshop/simulator/secure3d/return",
         "merchantData": "",
         "acsURL": "https://3ds-acs.test.modirum.com/mdpayacs/pareq"
      }
   }
}
```

No próximo passo, você precisa efetuar um **POST** dos dados para a URL implementada normalmente como um formulário de autoenvio. Isso precisa ser implementado no seu website. Nós recomendamos que você faça o POST do elemento de autenticação sem letras maiúsculas (exemplo: ‘pareq’) para evitar problemas de comunicação com o ACS.

Exemplo:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.com/mdpayacs/pareq ">
  <input type=”hidden” name=”pareq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

Depois que você recebeu os dados do ACS, é preciso submetê-los para o Gateway no elemento ```payerAuthenticationResponse``` junto com a referência da transação original. Isto é feito pelo envio de um PATCH para a transação original.

*Observação: O elemento ```merchantData``` pode não ser devolvido por todos os emissores. Caso você não receba no passo anterior, por favor não submeta no próximo PATCH.*

### PATCH /ipgrestapi/v2/services/payments/{ipgTransactionId}

```json
{
  "authenticationType": "Secure3D10AuthenticationUpdateRequest",
  "billingAddress": {
    "address1": "5565 Glenridge Conn",
    "city": "Atlanta",
    "postalCode": 30342,
    "country": "USA"
  },
  "merchantData": "MD____13992017033012241629.....c-4a40-aeb4-b41a8a34480fa067ac",
  "payerAuthenticationResponse": "eJzlWFeP67iS/………9Xm88X5c/37Pcj6I/v3P8JV86aGw=="
}
```

O documento JSON a seguir representa um exemplo de resposta que você recebe do Gateway indicando que a autorização foi processada com sucesso e marcada como autenticada:

```json
{
   "clientRequestId": "4c3aa885-db8e-43fb-b3c4-0e5c6927408c",
   "apiTraceId": "YKUjRJVBbdinNESL8Era8AAAAIs",
   "ipgTransactionId": "84563547902",
   "orderId": "R-ff489e2d-b5f3-4757-a91b-0a774681e26a",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "403587",
         "last4": "4977",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "Germany",
   "terminalId": "80000860",
   "merchantId": "000102072004393",
   "transactionTime": 1621434858,
   "approvedAmount":    {
      "total": 12.99,
      "currency": "EUR",
      "components": {"subtotal": 12.99}
   },
   "transactionStatus": "APPROVED",
   "secure3dResponse": {"responseCode3dSecure": "1"},
   "schemeTransactionId": "519154004377788",
   "processor":    {
      "referenceNumber": "113914232351",
      "authorizationCode": "907587",
      "responseCode": "00",
      "responseMessage": "Function performed error-free",
      "avsResponse":       {
         "streetMatch": "N",
         "postalCodeMatch": "N"
      }
   }
}
```

## Autenticação com um provedor terceirizado de 3DS 

Caso  você esteja usando seu próprio ou provedor terceiro de 3DS e planeje enviar o pedido de autorização para o Gateway, você precisa submeter para autenticação os valores obtidos do seu provedor de 3DS.

| **Campo**                     | **Descrição**                                                                                                                                                                                    |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```      | Usado para enviar o resultado de uma autenticação realizada por um provedor terceiro de 3DS.                                                                                                     |
| ```cavv	```                   | Valor da autenticação obtido na resposta de autenticação do provedor de 3DS.                                                                                                                     |
| ```dsTransactionId```         | ID da transação de autenticação, obtido do provedor de 3DS.                                                                                                                                      |
| ```authenticationResponse	``` | Indica o resultado da autenticação com seguintes valores permitidos: Y = transação totalmente autenticada, A = Tentativa de autenticação com sucesso, U = Impossível de autenticar por DS ou ACS |

Apenas os seguintes resultados de autenticação são elegíveis para envio ao servidor de autorização:

| **Caso de uso**                                       | **authenticationResponse** | **cavv**                    | **responseCode3dSecure** |
|-------------------------------------------------------|----------------------------|-----------------------------|--------------------------|
| Transação totalmente aprovada (ECI = 02 & 05)         | Y                          | value                       | 1                        |
| Tentativa de autenticação com sucesso (ECI = 01 & 06) | A                          | value                       | 4                        |
| Impossível de autenticar com DS ou ACS (ECI07)        | U                          | field must not be submitted | 6                        |

O documento JSON a seguir indica um exemplo de transação de venda submetida ao nosso Gateway depois de ter sido plenamente autenticada por um provedor terceiro de 3DS:

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "12.00",
    "currency": "EUR"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "401699XXXX0006",
      "securityCode": "999",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    }
  },
  "authenticationResult": {
    "authenticationType": "Secure3DAuthenticationResult",
    "cavv": "AAAAAAAAAAAAAAAAAAAAAAAAAAA=",
    "dsTransactionId": "5a56fdc9-6d47-5fee-8000-000000296743",
    "authenticationResponse": "Y"
  }
}
```

O documento JSON a seguir representa um exemplo de resposta que você receberá do Gateway indicando que a autorização foi concluída com sucesso e marcada como plenamente autenticada:

```json
{
   "clientRequestId": "97c67e8f-7c2d-421d-9d97-b749206aab06",
   "apiTraceId": "YJPLezoO2XZa9K8QL10bvgAAA98",
   "ipgTransactionId": "84411977859",
   "orderId": "R-941fc643-adae-4468-bc48-26e5099f4367",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "401699",
         "last4": "0006",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "USA",
   "terminalId": "80000012",
   "merchantId": "520334507229862",
   "transactionTime": 1620298619,
   "approvedAmount":    {
      "total": 12,
      "currency": "EUR",
      "components": {"subtotal": 12}
   },
   "transactionStatus": "APPROVED",
   "secure3dResponse": {"responseCode3dSecure": "1"},
   "schemeTransactionId": "234567891234560",
   "processor":    {
      "referenceNumber": "112610940537",
      "authorizationCode": "005042",
      "responseCode": "00",
      "responseMessage": "Function performed error-free",
      "avsResponse":       {
         "streetMatch": "Y",
         "postalCodeMatch": "Y"
      },
      "securityCodeResponse": "MATCHED"
   }
}
```

---

## Veja também

- [Tipos de Requisição](?path=docs/portuguese/payments/3-1-tipos-requisição.md)
- [Métodos de pagamento](?path=docs/portuguese/payments/3-2-metodos-pagamento.md)
- [Tipos de pagamento](?path=docs/portuguese/payments/3-3-tipos-pagamento.md)
- [Post-authorisation & Returns](?path=docs/portuguese/payments/3-4-post-auth.md)
- [Verificação de cartão](?path=docs/portuguese/payments/3-6-verificação-cartão.md)
- [Conversão de moeda](?path=docs/portuguese/payments/3-7-conversão-moeda.md)
- [Redirecionamento controlado](?path=docs/portuguese/payments/3-8-redirecionamento-controlado.md)
- [Ordens](?path=docs/portuguese/payments/3-9-ordens.md)
- [Link de Pagamento](?path=docs/portuguese/payments/3-10-link-pagamento.md)
- [Pagamento recorrente](?path=docs/portuguese/payments/3-11-pagamento-recorrente.md)
- [Tokenização](?path=docs/portuguese/payments/3-12-tokenização.md)
- [Códigos de erro](?path=docs/portuguese/payments/3-13-codigos-erro.md)

---
