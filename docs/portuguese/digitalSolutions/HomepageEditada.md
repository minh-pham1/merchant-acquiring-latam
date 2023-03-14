
# APIs Página Principal

Bem-vindo à nossa documentação de suporte de APIs!

Para uma breve introdução de como usar o APIS, acesse nossa [introdução](?path=docs/portuguese/digitalSolutions/APIs-Introduction.md).

Esta página descreve as APIs REST e os recursos fornecidos pela Fiserv. 

Nossos sistemas são confiáveis com ótimo desempenho e elasticidade. 

Como líderes de negócios no segmento de pagamentos, as APIs fornecerão uma oportunidade aos desenvolvedores de aproveitar nosso ecossistema de pagamentos e nossa ampla infraestrutura.

Nossas APIs possuem paths URI e utilizam autenticação HTTP nativa, proporcionando acesso seguro às informações.

Faça solicitações HTTP usando os métodos GET e POST, receba respostas no formato JSON.

Isso permite que os desenvolvedores usem qualquer linguagem de programação para acessar nossas APIs, integrar de forma segura e construir vários aplicativos diferentes.

Para acessar as APIs comece adquirindo um [Token Access](../api/?type=post&path=/token/)

## Autenticação

[Token Access](../api/?type=post&path=/token/)

## Dispositivos

[Merchant Devices Daily Password - Consult](../api/?type=get&path=/bwa/senha-dia/v1/senha/dia)

[Merchant Device Availability - Consult](../api/?type=get&path=/bwa/tecnologia/habilitadas-ec)

[Search for Device Billing History](../api/?type=get&path=/bwa/cobranca-terminal/v1/consulta/{data})

[Request of Device Paper Roll](../api/?type=get&path=/bwa/solicitabobina/{instituicao}/{merchant}/{logico})

[Merchant Devices and Fees - Consult](../api/?type=get&path=/bwa/device-fees/{institution}/{merchanID})

[Search for Device Fees based on Merchant Institution and Merchant Code - Changed the resource name](../api/?type=get&path=/bwa/device-fees/v2)

[Search for Device Service Types](../api/?type=get&path=/bwa/device-fees/device-service)

## Informações de clientes

[Merchant Debits (fee, refund, reversal, etc)- Consult](../api/?type=get&path=/bwa/ajuste-debitos/v1/obter)

[Merchant Chargeback - Consult](../api/?type=post&path=/bwa/chargeback-ec/api/chargeback/ec/apigee/filter)

[Contracted Services - Consult](../api/?type=get&path=/bwa/produtos-servicos/v1/produtos-servicos)

[Existing Customer Validation](../api/?type=get&path=/bwa/validacao-cliente/v2/validacao/{documento})

[Existing Customer Validation - by Institution and Tax ID (CPF/CNPJ)](../api/?type=get&path=/bwa/validacao-cliente/v2/credenciamento/documento/{instituicao}/{documento})

[Existing Customer Validation - by Institution and Tax ID (CPF/CNPJ) and Service contract](../api/?type=get&path=/bwa/validacao-cliente/v3/credenciamento/documento/{instituicao}/{documento}/{service-contract})

[Existing Customer Validation - Validation by Boarding Channel](../api/?type=post&path=/bwa/validacao-cliente/v2/credenciamento/canal)

[Existing Customer Validation - Validation by MDR Comparison](../api/?type=post&path=/bwa/validacao-cliente/v2/credenciamento/taxa)

[Existing Customer Validation - Validation by Technology - ECommerce X POS comparison](../api/?type=get&path=/bwa/validacao-cliente/v2/credenciamento/tecnologia/{codigoTecnologia}/{instituicao}/{documento})

[Existing Customer Validation - Validation by Technology and Service contract - ECommerce X POS comparison](../api/?type=get&path=/bwa/validacao-cliente/v3/credenciamento/tecnologia/{codigoTecnologia}/{instituicao}/{documento}/{service-contract})

[Merchant Information - DIRF](../api/?type=get&path=/bwa/dirf-api/v1/dirf/irpdf/{ano})

[Historic Contract](../api/?type=get&path=/bwa/historico-contrato/v1/historicoContrato)

[Merchant Logistic Information - Add/Update](../api/?type=post&path=/bwa/dados-logistica/)

[Merchant Logistic Information - Consult Logistic Information](../api/?type=get&path=/bwa/dados-logistica/{numeroInstituicao}/{merchantId}/{cnpjCpf})

[Merchant Service Fees (MDR) - Search for MDR Fees based on Institution Number and Merchant Code](../api/?type=get&path=/bwa/mdr-fees/{institution}/{merchanID})

[Merchant Service Fees (MDR) - Consult](../api/?type=get&path=/bwa/mdr-fees/v2)

[Merchant Contract Status - Update](../api/?type=post&path=/bwa/status-estabelecimento/v1/atualizaStatusEstabelecimento)

[Merchant Customer Information - Consult](../api/?type=get&path=/bwa/estabelecimento/v2)

[Merchant Customer Information - Search for Merchant - To be retired (TBD)](../api/?type=get&path=/bwa/estabelecimento/{instituicao}/{documento})

[Merchant Information - Demographic History](../api/?type=get&path=/bwa/alteracoes-cadastrais/v1/consulta)

[Merchant Information - Monthly Presumed Billing](../api/?type=get&path=/bwa/merchant-billing/monthly/presumed)

[Merchant Payment Hierarchy - Consult](../api/?type=get&path=/bwa/hierarquia-pagamento/v1/config/account-type)

[Merchant Payment Hierarchy - Include](../api/?type=post&path=/bwa/hierarquia-pagamento/v1/incluir)

[Merchant Payment Hierarchy - Remove](../api/?type=post&path=/bwa/hierarquia-pagamento/v1/excluir)

[Merchant Payment Hierarchy - Search for Payment Hierarchy](../api/?type=post&path=/bwa/hierarquia-pagamento/v1/)

[Merchant Information - Registradora (CIRC3952)](../api/?type=get&path=/bwa/recebiveis/v1/receivableUnit/consulta/{dataInicio}/{dataFim})

[Merchant Information - Agenda Out](../api/?type=get&path=/bwa/recebiveis/v1/agendaOut/consulta/{dataInicio}/{dataFim})

[Merchant Information - Lock Information](../api/?type=get&path=/bwa/recebiveis/v1/lockInformation/consulta/{dataInicio}/{dataFim})

[Merchant Relationship Hierarchy - Consult](../api/?type=get&path=/bwa/hierarquia-relacionamento/v1/relacionamento/obterEstabelecimentoPai)

[Merchant Relationship Hierarchy - Add](../api/?type=post&path=/bwa/hierarquia-relacionamento/v1/relacionamento/incluir)

[Merchant Relationship Hierarchy - Remove](../api/?type=post&path=/bwa/hierarquia-relacionamento/v1/relacionamento/excluir)

[Merchant Requests (Cases) - Open](../api/?type=get&path=/bwa/wsm/merchantinformation/workingHours/config/motivos)

[Merchant Requests (Cases) - Search for Sub-Reasons to be used for Salesforce Case Opening by Reason](../api/?type=get&path=/bwa/abertura-caso/config/sub-motivos/{Id})

[Merchant Requests (Cases) - Search for Symptom to be used for Salesforce Case Opening by Symptoms](../api/?type=get&path=/bwa/abertura-caso/config/sintomas/{Id})

[Merchant Requests (Cases) - Request Opening (Cases in Salesforce)](../api/?type=post&path=/bwa/abertura-caso/)

[Merchant Requests (Cases) - Request Opening by Brand (Cases in Salesforce)](../api/?type=post&path=/bwa/abertura-caso/{marca})

[Search for Cases opened in Salesforce](../api/?type=get&path=/bwa/sales-force-ws/caso/{numeroInstituicao})

[Search for Comments related to a Case](../api/?type=get&path=/bwa/sales-force-ws/comentarios)

[Merchant Services - Consult/Update for Associations/Schema](../api/?type=get&path=/bwa/consulta-servico-bandeira/v1/consulta/servicoBandeira)

[Merchant Services - Consult for Associations/Schema Status by Category](../api/?type=get&path=/bwa/consulta-servico-bandeira/v1/consulta/servicoBandeira/{categoria})

[Merchant Services - Update for Associations/Schema Status](../api/?type=post&path=/bwa/consulta-servico-bandeira/v1/altera/status)

[Merchant Addresses - Update](../api/?type=post&path=/bwa/wsm/merchantinformation/address/updateAddress/)

[Merchant Contact Information - Update](../api/?type=post&path=/bwa/wsm/merchantinformation/contact/updateContactInformation/)

[Merchant Information - Update Partner](../api/?type=post&path=/bwa/atualiza-socio/v1/atualizaEstabelecimento/{clientNumber})

[Merchant Trade Name - Update](../api/?type=post&path=/bwa/wsm/merchantinformation/tradeName/updateTradeName/)

[Merchant Working Hours - Update](../api/?type=post&path=/bwa/wsm/merchantinformation/workingHours/updateWorkingHours/)

## Credenciamento

[Online Boarding Endpoints](?path=docs/portuguese/digitalSolutions/BoardingInformation.md)

## Pagamentos e Antecipações

[Get transaction details](../api/?type=get&path=/bwa/cashflow/v1/transactionDetails)

[Get cash flow summary by scheme Total](../api/?type=get&path=/bwa/cashflow/v1/summaryByScheme/total)

[Get cash flow summary by scheme for Products](../api/?type=get&path=/bwa/cashflow/v1/summaryByScheme/products)

[Get Summary By Scheme For Period](../api/?type=get&path=/bwa/cashflow/v1/summaryByScheme/period)

[Get resume](../api/?type=get&path=/bwa/cashflow/v1/resume)

[Get cash flow detail by scheme](../api/?type=get&path=/bwa/cashflow/v1/detailByScheme)

[Payment Settlement Account - Validation](../api/?type=post&path=/bwa/domicilio-bancario/)

[Payment Settlement Account - Validation based on Rules for Bank and Type of Account](../api/?type=post&path=/bwa/domicilio-bancario/validar)

[Payment Settlement Account - Validation based on Rules for Bank and Type of Account - V2](../api/?type=post&path=/bwa/domicilio-bancario/resources/v2/domicilio-bancario/validate)

[Transactions related to Manual/Automatic Prepayment - Get details of sales on prepayments made by period](../api/?type=get&path=/bwa/antecipacoes-realizadas/v1/sumarizacao/socPeriodo)

[Transactions related to Manual/Automatic Prepayment - Get details of sales on prepayments made by number](../api/?type=get&path=/bwa/antecipacoes-realizadas/v1/sumarizacao/socNumero)

[Get prepayment status](../api/?type=get&path=/bwa/antecipacoes-realizadas/v1/cerc/status)

[Get prepayment details](../api/?type=get&path=/bwa/antecipacoes-realizadas/v1/cerc/detalhes)

[Transactions related to Manual/Automatic Prepayment](../api/?type=get&path=/bwa/antecipacoes-realizadas/v1/detalhamento/antecipacoesRealizadas)

[Automatic Prepayment - Cancel - Consult Automatic Prepayment](../api/?type=get&path=/bwa/antecipacao-automatica/v1/antecipacao/)

[Automatic Prepayment - Cancel - Automatic Prepayment](../api/?type=post&path=/bwa/antecipacao-automatica/v1/antecipacao)

[Prepayment - History Fees Consult - Consult History of Prepayment Fees](../api/?type=get&path=/bwa/taxa-antecipacao/v1/consulta)

[Prepayment - History Fees Consult - Consult of Sales Related to Prepayment](../api/?type=get&path=/bwa/taxa-antecipacao/v1/consulta/resumoVendas/{prepaymentNumber})

[Payment Settlement Account - Update](../api/?type=post&path=/bwa/wsm/merchantinformation/accounts/paymentAccountInfo/)

[Payments - Consult for All Transaction Period - Summarized](../api/?type=get&path=/bwa/pagamentos/resources/v1/transacoes/sumarizacao/{inst}/{merchant}/{dataInicio}/{dataFim})

[Payments - Consult for Completed Payments](../api/?type=get&path=/bwa/pagamentos/resources/v1/{inst}/{merchant}/{dataInicio}/{dataFim})

[Payments - Search for Payments in specific status suspended or rejected](../api/?type=get&path=/bwa/pagamentos/resources/v1/{tipoPagamento}/{inst}/{merchant}/{dataInicio}/{dataFim})

[Payments - Search for Payment Order](../api/?type=get&path=/bwa/pagamentos/resources/v1/transacoes/{inst}/{merchant}/{dataInicio}/{dataFim}/{ordemPagamento})

[Payments - Search all Transactions for a period](../api/?type=get&path=/bwa/pagamentos/resources/v1/transacoes/{inst}/{merchant}/{dataInicio}/{dataFim})

[Payments - Get Payments](../api/?type=get&path=/sba/PagamentosRealizados/{inst}/{merchant}/{dataInicio}/{dataFim})

[Detailing payments](../api/?type=post&path=/bwa/pagamentos-liberados/v1/socs/detalhamento)

[Detailing of sales](../api/?type=get&path=/bwa/pagamentos-liberados/v1/rocs/detalhamento)

[Service Order - Terminal Maintenance - Prepayment Flag Update (Turn On/Off)](../api/?type=post&path=/bwa/wsm/fundingtools/prepayFlag/updatePrepayFlag/)

[Prepayment - Maintenance/Simulate/Confirm/Reject - Consult Available Balances For Prepayment (all Associations)](../api/?type=post&path=/bwa/wsm/prepayments/consultations/consultPrepayment)

[Automatic Prepayment Information Maintenance](../api/?type=post&path=/bwa/wsm/prepayments/autoPrepayments/automaticPrepayment)

[Prepayment - Maintenance/Simulate/Confirm/Reject - Consult or Simulate Manual Prepayment](../api/?type=post&path=/bwa/wsm/prepayments/prepaySimulations/prepaymentSimulation)

[Prepayment - Maintenance/Simulate/Confirm/Reject - Confirmation or Rejection of Manual Prepayment Simulation](../api/?type=post&path=/bwa/wsm/prepayments/prepaySimulationsAction/prepaySimConfReject)

[Wallet - PIX - Transactions](../api/?type=get&path=/pix/transactions)

[Wallet - PIX - Credentials](../api/?type=get&path=/pix/credentials/{pspCode}/{taxIdNumber1})

[Wallet - PIX - New Credentials](../api/?type=post&path=/pix/credentials)

## Informações de Vendas

[Merchant Authorizations History - Consult](../api/?type=get&path=/bwa/autorizacoesbw/v1/autorizacoesbw)

[Consult Chargeback Information](../api/?type=post&path=/bwa/chargeback-api-external/api/chargeback/apigee/filter)

[Merchant Sales - Keyed Trans Permission - Consult](../api/?type=get&path=/bwa/consulta-transacao-digitada/v1/consulta)

[Merchant Authorizations - Consult (Institute and marchant)](../api/?type=get&path=/bwa/autorizacoes/{inst}/{merchant})

[Merchant Authorizations - Consult by Institution Number, Merchant Code and Terminal](../api/?type=get&path=/bwa/autorizacoes/{inst}/{merchant}/{terminals})

[Merchant Sales - Paid or To Be Received - Consult](../api/?type=get&path=/bwa/vendas/resources/v1/receber/{inst}/{merchant}/{dataInicio}/{dataFim})

[Merchant Sales - To Be Received - Consult - It allows the selection of MULTIPLE association schema](../api/?type=get&path=/bwa/vendas/resources/v1/realizadas/{inst}/{merchant}/{dataInicio}/{dataFim})

[Merchant Sales - Paid - Consult - It allows the selection of MULTIPLE association schema -V2](../api/?type=get&path=/bwa/vendas/resources/v2/realizadas/{dataInicio}/{dataFim})

[Merchant Sales - To Be Received - Consult - It allows the selection of MULTIPLE association schema V2](../api/?type=get&path=/bwa/vendas/resources/v2/receber/{dataInicio}/{dataFim})

[Merchant Sales - Canceled - Consult - It allows the selection of MULTIPLE association schema](../api/?type=get&path=/bwa/vendas/resources/v2/canceladas)

[Sales tracking](../api/?type=get&path=/bwa/acompanhamento-vendas/v1/acompanhamentoVendas)

[Operations - Consult](../api/?type=post&path=/bwa/wsm/devicerequest/consultoperation/processConsultOperationRequest/)

[Merchant Sales - Suspended Transactions - Consult](../api/?type=post&path=/bwa/consulta-transacoes-suspensas/v1/consulta/transacaosuspensa)

[Merchant Sales - Suspended Transactions - Consult suspended summarized by Merchant ID](../api/?type=post&path=/bwa/consulta-transacoes-suspensas/v1/consulta/transacaosuspensaSumarizada)

## Service Order Information

[Find, by code, one cancellation reason registered in BR TMP](../api/?type=get&path=/bwa/service-order-cancellation-reason/{code})

[List all cancellation reasons registered in TMP](../api/?type=get&path=/bwa/service-order-cancellation-reason/)

[Search for Merchant by Partner Tax ID](../api/?type=get&path=/bwa/consulta-estabelecimento-socio/v1/consulta/{documento})

[Merchant Status Update](../api/?type=post&path=/bwa/wsm/fundingtools/contractStatus/updateContractStatus)

[Geo Location](../api/?type=get&path=/bwa/geocode/{address})

[List all the special modals based on the parameters provided](../api/?type=get&path=/bwa/modal/search)

[Tax ID (company) Validation(not available PROD)](../api/?type=get&path=/bwa/cnpj/{cnpj})

[Tax ID (individual) Validation (not available PROD)](../api/?type=get&path=/bwa/consulta-cpf/{cpf})

[Service Order - Terminal Maintenance - Request Device - Additional Terminal](../api/?type=post&path=/bwa/wsm/devicerequest/addterminal/processAddTerminalRequest)

[Service Order - Terminal Maintenance - Request Device - Terminal Maintenance](../api/?type=post&path=/bwa/wsm/devicerequest/maintenance/processMaintenanceRequest)

[Service Order - Terminal Maintenance - Request Device - Replace Terminal](../api/?type=post&path=/bwa/wsm/devicerequest/replacechange/processReplaceChangeRequest)

[Service Order - Terminal Maintenance - Device Request -  Uninstall Terminal](../api/?type=post&path=/bwa/wsm/devicerequest/uninstall/processUninstallRequest)

[TMP - Find, by merchantId, the list of ticket registered in TMP](../api/?type=get&path=/bwa/brtmp-microservices/listTicket/{merchantId})

[TMP - Find, by its Serial Number, a product registered in TMP](../api/?type=get&path=/bwa/brtmp-microservices/product/{serialNumber})

[TMP - List all reschedule motive registered in TMP](../api/?type=get&path=/bwa/brtmp-microservices/ticket-reschedule-motive)

[TMP - List all motives reverse registered in TMP with filter by code](../api/?type=get&path=/bwa/brtmp-microservices/motives-reverse)

[TMP - Register reschedule in TMP](../api/?type=post&path=/bwa/brtmp-microservices/ticket-reschedule)

[TMP - Find, by code, one cancellation reason registered in BR TMP](../api/?type=get&path=/bwa/brtmp-microservices/ticket-cancel-reason/{code})

[TMP - List all cancellation reasons registered in TMP](../api/?type=get&path=/bwa/brtmp-microservices/ticket-cancel-reason)

[TMP - It List, according the given parameters, the Service Providers/Modals which can be attached in the Service Order](../api/?type=get&path=/bwa/brtmp-microservices/modal/search)

[TMP - Consults special condition by institution](../api/?type=get&path=/bwa/brtmp-microservices/flag/pesquisa-por-instituicao)

[TMP - Request for actions with the sale format (Reversal, post code, dispute) in TMP](../api/?type=post&path=/bwa/brtmp-microservices/update-task)

[TMP - Get Coil Inventory By EC](../api/?type=get&path=/bwa/brtmp-microservices/coil/inventory/{merchant})

[TMP - Get Coil Balance By EC](../api/?type=get&path=/bwa/brtmp-microservices/coil/balance/{merchant})

[TMP - Find, by code, one maintenance reason registered in BR TMP](../api/?type=get&path=/bwa/brtmp-microservices/ticket-maintenance-reason/{code})

[TMP - List all maintenance reasons registered in TMP](../api/?type=get&path=/bwa/brtmp-microservices/ticket-maintenance-reason)

[TMP - Find, by the Service Order number, the Service Order's logistic data](../api/?type=get&path=/bwa/brtmp-microservices/service-order/logistics/{referenceNumberID})

[TMP - Find Order number by Inc and merchant](../api/?type=get&path=/bwa/brtmp-microservices/tms-cos/rest/consultaos/v2/json/trackos/{instituicao}/{merchant})

[TMP - List Services Orders](../api/?type=get&path=/bwa/brtmp-microservices/tms-cos/resources/v2/json/{merchant}/{institution})

[TMP - Find Order number by Inc and merchant V3](../api/?type=get&path=/bwa/brtmp-microservices/tms-cos/rest/consultaos/v3/json/trackos/{instituicao}/{merchant})

[TMP - Find byos by Inc and merchant V2](../api/?type=get&path=/bwa/brtmp-microservices/tms-cos/resources/v2/json/byos/{instituicao}/{serviceorder})

[TMS - Service Order Opening for API TMS Microservices](../api/?type=post&path=/bwa/brtms-microservices/serviceorder/remote/transaction)

[Service Order - Cancelling](../api/?type=post&path=/bwa/wsm/devicerequest/canceloperation/processCancelOperationRequest)

[Service Order - Consult - Service Order Search by Institution Number and Merchant Code](../api/?type=get&path=/bwa/consultaos/{instituicao}/{numeroMerchant})

[Service Order - Consult - Service Order Inquiry by Institution and Service Order Number](../api/?type=get&path=/bwa/consultaos/byos/{instituicao}/{osNumero})

[Service Order - Consult - Service Order Tracking by Institution Number and Merchant Code](../api/?type=get&path=/bwa/consultaos/trackos/{instituicao}/{numeroMerchant})

[Service Order - Consult - Service Order Search by Institution Number and Merchant Code](../api/?type=get&path=/bwa/consultaos/resources/v2/os)

[Service Order - Consult - Service Order Inquiry by Institution and Service Order Number](../api/?type=get&path=/bwa/consultaos/resources/v1/os/{osNumero})

[Service Order - Consult - Service Order Tracking by Institution Number and Merchant Code](../api/?type=get&path=/bwa/consultaos/resources/v1/trackos)

[Service Order - Consult - Service Order Tracking by Institution Number, Merchant Code and Service Order Number](../api/?type=get&path=/bwa/consultaos/resources/v1/trackos/{osNumero})

[Zipcode (CEP) - Consult](../api/?type=get&path=/bwa/cep-service/cep/{cep})

[Zipcode (CEP) - Consult - Search for City Name](../api/?type=get&path=/bwa/cep-service/cidade/{city})
