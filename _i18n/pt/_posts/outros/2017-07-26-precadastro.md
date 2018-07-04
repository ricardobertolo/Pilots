---
layout: manual
title: Pré-cadastro Cielo
description: Manual integração técnica via API
search: true
translated: true
toc_footers: true
categories: manual
sort_order: 1
tags:
  - Outros
language_tabs:
  json: JSON
  shell: cURL
---

# Cielo OAUTH

O Cielo OAUTH é um processo de autenticação utilizado em APIs Cielo que são correlacionadas a produtos E-commerce. Ele utiliza como segurança o protocolo **[OAUTH2](https://oauth.net/2/)**, onde é necessário primeiramente obter um token de acesso, utlizando suas credenciais, que deverá posteriormente ser enviado à API CieloOAuth

> Para obter o `ClientID` e o `ClientSecret`, acione a equipe de Produtos Cielo. Credênciais liberadas apenas para lojistas selecionados,

Para utilizar o Cielo Oauth são necessarias as seguintes credenciais:

| PROPRIEDADE    | DESCRIÇÃO                                                             | TIPO   |
|----------------|-----------------------------------------------------------------------|--------|
| `ClientId`     | Identificador chave fornecido pela CIELO                              | guid   |
| `ClientSecret` | Chave que valida o ClientID. Fornecida pela Cielo junto ao `ClientID` | string |

## Token de acesso

Para obter acesso a serviços Cielo que utilizam o `Cielo Oauth`, será necessário obter um token de acesso, conforme os passos abaixo:

1. Concatenar o _ClientId_ e o _ClientSecret_, **ClientId:ClientSecret**
2. Codificar o resultado em **Base64**
3. Enviar uma requisição, utilizando o método HTTP POST

### Concatenação

|Campo|Formato|
|-|-|
|**ClientId** | b521b6b2-b9b4-4a30-881d-3b63dece0006|
|**ClientSecret**| 08Qkje79NwWRx5BdgNJsIkBuITt5cIVO |
|**ClientId:ClientSecret**| *b521b6b2-b9b4-4a30-881d-3b63dece0006:08Qkje79NwWRx5BdgNJsIkBuITt5cIVO*|
|**Base64**| *YjUyMWI2YjItYjliNC00YTMwLTg4MWQtM2I2M2RlY2UwMDA2OiAwOFFramU3OU53V1J4NUJkZ05Kc0lrQnVJVHQ1Y0lWTw*|

### Request

O Request dever ser enviado apenas no Header da requisição.

<aside class="request"><span class="method post">POST</span><span class="endpoint">/v2/public/v2/token</span></aside>

``` json
x-www-form-urlencoded
--header "Authorization: Basic {base64}"  
--header "Content-Type: application/x-www-form-urlencoded"  
grant_type=client_credentials
```

### Response

O response possuirá o Token utilizado para novas requisições em Serviços Cielo

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJjbGllbnRfbmFtZSI6Ik1ldUNoZWNrb3V0IE1hc3RlciBLZXkiLCJjbGllbnRfaWQiOiJjODlmZGasdasdasdmUyLTRlNzctODA2YS02ZDc1Y2QzOTdkYWMiLCJzY29wZXMiOiJ7XCJTY29wZVwiOlwiQ2hlY2tvdXRBcGlcIixcIkNsYWltc1wiOltdfSIsInJvbGUiOiJasdasdasd291dEFwaSIsImlzc47I6Imh0dHBzOi8vYXV0aGhvbasdasdnJhc3BhZy5jb20uYnIiLCJhdWQiOiJVVlF4Y1VBMmNTSjFma1EzSVVFbk9pSTNkbTl0ZmasdsadQjVKVVV1UVdnPSIsImV4cCI6MTQ5Nzk5NjY3NywibmJmIjoxNDk3OTEwMjc3fQ.ozj4xnH9PA3dji-ARPSbI7Nakn9dw5I8w6myBRkF-uA",
    "token_type": "bearer",
    "expires_in": 1199
}
```

|PROPRIEDADE   | DESCRIÇÃO                                                 |TIPO  |
|--------------|-----------------------------------------------------------|------|
|`Access_token`| Utilizado para acesso aos serviços da API                 |string|
|`Token_type`  | Sempre será do tipo `bearer`                              |texto |
|`Expires_in`  | Validade do token em segundos. Aproximadamente 20 minutos |int   |

> O token retornado (access_token) deverá ser utilizado em toda requisição como uma chave de autorização, destacando que este possui uma validade de 20 minutos (1200 segundos) e após esse intervalo, será necessário obter um novo token para acesso aos serviços Cielo. 

# Consulta EC

O processo de consulta de EC busca a existencia de afiliações e MerchanIDs na base de lojas **Checkout Cielo**.

<aside class="warning">Não são retornadas "lojas legado" do checkout cielo, sendo essas  CHECKOUT SIMPLIFICADO OU CHECKOUT WEBSERVICE </aside>

> Caso um lojistas Checkout Simplificado deseje usar o link de pagamentos, ele deve ser convertido em Checkout Completo. Essa mudança impacta a integração da loja.

A Consulta consiste em um `GET` ao Endpoint abaixo:

<aside class="request"><span class="method get">GET</span> <span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/merchant/credentials/{{EC}}}</span></aside>

```json
{
	"Affiliation": "10101010101"
	"MerchantId":  "dc9d6efa-b582-4ac8-ac59-12c5724"
	"ClientId";   "dc9d6efa-b582-4ac8-ac59-12c5724"
	"ClientSecret":  "ClientSecretXX"
}
```

| PROPRIEDADE    | DESCRIÇÃO                                                                                                    | TIPO   |
|----------------|--------------------------------------------------------------------------------------------------------------|--------|
| `Affiliation`  | Numero de afiliação cadastrado no Checkout Cielo                                                             | string |
| `MerchantID`   | Identificador da loja no Checkout Cielo e Identificado para Criação de Links e consulta transacional         | GUID   |
| `ClientId`     | Identificador da loja no Cielo OAUTH - <br><br> Para lojas **Checkout Cielo**, é o mesmo valor do MerchandID | GUID   |
| `ClientSecret` | Chave que valida o ClientID.                                                                                 | string |

> Tipos de Retorno para ECs não encontrados:
> <br>
> * **EC inexistente**: NotFound
> * **EC inválido**: Forbidden

# Pré-cadastro

O Pré-cadastro Cielo tem como objetivo permitir que plataformas parceiras possam criar lojas para a **API Cielo Ecommerce** e **Checkout Cielo** de maneira automatizada.
O Fluxo de pré-cadastro é descrito na seguinte ordem:

1. A Plataforma recebe da Cielo um identificador (`PlatformID`) + Credenciais do Cielo OAuth (`ClientID`+`ClientSecret`)
2. Com as credenciais, a plataforma envia de dados cadastrais para o sistema Cielo
3. O setor de credenciamento Cielo avalia o cadastro e Ativa a loja
4. Dados da loja são enviadas via `POST` para a URL de notificação da Plataforma.

> **OBS**: O Cadastro da plataforma junto a Cielo é pré-requisito para acesso as APIs de pré-cadastro. 

## Criar lojas

Para realizar a criação de lojas via pré-cadastro é necessario que a Plataforma esteja devidamente cadastrada juntoi a Cielo e possuas as chave de acesso ao **Cielo Oauth**.

Após realizar o processo de Autenticação descrito na sessão [Cielo OAUTH](https://docscielo.github.io/Pilots/manual/appcielo-link#cielo-oauth), a plataforma poderá realizar o `POST` de criação de lojas

### Post de criação

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://merchantpreregistration.cieloecommerce.cielo.com.br/api/preregistrations</span></aside>

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://merchantpreregistrationsandbox.cieloecommerce.cielo.com.br/api/preregistrations</span></aside>

``` json
{
  "Email": "json40@email.com",
  "DocumentType": 1,
  "INtegrationType":2,
  "ContactName": "Contact Name",
  "Phone": "(99) 99999-9999",
  "ActivationUrl":"http://www.google.com",

  "CompanyData": {
    "FancyName": "json sa",
    "CorporateName": "My Company Ltda.",
    "Cnpj": "99.999.999/9999-99"
  },
  "PlatformId":"6A87A6E1-E080-462E-1CCB-08D58DD4E419",
  "PersonalData": {
    "Cpf": "999.999.999-99",
    "FullName": "Customer Name"
  },
  "BusinessAddress": {
    "ZipCode": "99999-999",
    "Address": "Av. Lorem Ipsum Dolor",
    "Number": "99999",
    "Complement": "Lorem 999",
    "District": "Lorem Ipsum",
    "City": "Rio de Janeiro",
    "State": "RJ"
  },
  "TechnicalContact":{
      "ContactName":"ContactNameTest",
      "ContactPhone":"99 99999-9999",
      "ContactEmail":"json@email.com",
      "CompanyDeveloper":"CompanyDeveloperTest"
  },
  "TransactionalConfiguration": {
    "EC": "1010208045",
    "ProductionKey": "98348349849",
    "Mcc": "7032"
  },
  "BankingData":{
      "Bank":"Abc",
      "Agency":"1111",
      "Account":"22222",
      "AccountType":"CurrentAccount"
   }
}
```

| Campo                                      | Descrição                                                                                                                          | Tipo   | Tamanho | Obrigatório |
|--------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|--------|---------|-------------|
| `Email`                                    | E-mail de comunicação com o lojista                                                                                                | String | 60      | Sim         |
| `DocumentType`                             | Tipo de documentação <br><br>CPF = 1 <br>CNPJ = 2                                                                                  | Enum   | 1       | Sim         |
| `IntegrationType`                          | Produto Cielo: <br><br> Api Cielo Ecommerce = 1 <br> Checkout Cielo = 2                                                            | Enum   | 1       | Sim         |
| `ContactName`                              | Nome do lojista                                                                                                                    | String | 255     | Não         |
| `Phone`                                    | Telefone do lojista                                                                                                                | String | 10      | Não         |
| `ActivationUrl`                            | URL de Notificação da Plataforma                                                                                                   | String | 255     | Sim         |
| `PlatformId`                               | Identificação da plataforma na Cielo                                                                                               | GUID   | -       | Sim         |
| **CompanyData.**                           | **Informações sobre a loja, se IntegrationType = 2**                                                                               |        |         |             |
| `CompanyData.FancyName`                    | Nome fantasia                                                                                                                      | String |         | Sim         |
| `CompanyData.CorporateName`                | Razão social                                                                                                                       | String |         | Sim         |
| `CompanyData.Cnpj`                         | CNPJ (Sem Formatação)                                                                                                              | String |         | Sim         |
|  **PersonalData.**                         | **Informações sobre a loja, se IntegrationType = 1**                                                                               |        |         |             |
| `PersonalData.Cpf`                         | CPF (Sem formatação)                                                                                                               | String |         | Sim         |
| `PersonalData.FullName`                    | Nome do lojista                                                                                                                    | String |         | Sim         |
| **BusinessAddress.**                       | **Informações sobre endereço da loja**                                                                                             |        |         |             |
| `BusinessAddress.ZipCode`                  | CEP da loja                                                                                                                        | String | 9       | Sim         |
| `BusinessAddress.Address`                  | Endereço da loja                                                                                                                   | String | 32      | Sim         |
| `BusinessAddress.Number`                   | Numero da loja                                                                                                                     | String | 3       | Sim         |
| `BusinessAddress.Complement`               | Complemento                                                                                                                        | String | 26      | Sim         |
| `BusinessAddress.District`                 | Bairro da loja                                                                                                                     | String | 28      | Sim         |
| `BusinessAddress.City`                     | Cidade da loja                                                                                                                     | String | 28      | Sim         |
| `BusinessAddress.State`                    | Estado - Unidade Federativa (Ex: SP/RJ)                                                                                            | String | 2       | Sim         |
| **TechnicalContact.**                      | **Dados de contato do responsável tecnico da loja**                                                                                |        |         |             |
| `TechnicalContact.ContactName`             | Nome do desenvolvedor                                                                                                              | String | 32      | Sim         |
| `TechnicalContact.ContactPhone`            | Telefone do desenvolvedor                                                                                                          | String | 15      | Sim         |
| `TechnicalContact.ContactEmail`            | E-mail do desenvolvedor                                                                                                            | String | 64      | Sim         |
| `TechnicalContact.CompanyDeveloper`        | Nome da empresa de desenvolvimento                                                                                                 | String | 32      | Sim         |
| **TransactionalConfiguration.**            | **Caso a loja ja possua um cadastro na Cielo (Afiliação), este nó deve ser enviado**                                               |        |         | Sim         |
| `TransactionalConfiguration.EC`            | Afiliação Cielo da loja                                                                                                            | String | 10      | Sim         |
| `TransactionalConfiguration.ProductionKey` | Chave de produção Cielo                                                                                                            | String | 255     | Sim         |
| `TransactionalConfiguration.Mcc`           | Código de identificação de setor Cielo                                                                                             | String | 4       | Sim         |
| **BankingData.**                           | **Informações bancárias para cadastro de contas junto a Cielo**                                                                    |        |         |             |
| `BankingData.Bank`                         | Banco                                                                                                                              | String | 3       | Sim         |
| `BankingData.Agency`                       | Agência Bancária                                                                                                                   | String | 5       | Sim         |
| `BankingData.Account`                      | Conta bancária                                                                                                                     | String | 13      | Sim         |
| `BankingData.AccountType`                  | Tipo de conta (Para Caixa Econômica Federal) <br><br> 001 = CurrentAccount/Conta Corrente/ <br> 002 = SimpleAccount /Conta Simples | Enum   | 3       | Sim         |

### Post de Notificação

O Post de notificação será enviado quando a loja em pré-cadastro for ativada.
A URL de Ativação (`ActivationURL`) receberá um POST contendo os dados abaixo

``` json
{
 "Affiliation": "10101010101",
 "MerchantID": "dc9d6efa-b582-4ac8-ac59-12c57245df2a",
 "Merchantkey":"dd6efab5824ac8ac5912c57245df2ac9d6efab5824ac8ac5912c57245df2a",
 "IntegrationType": "1"
}

```

| PROPRIEDADE      | DESCRIÇÃO                                                                                                    | TIPO   |
|------------------|--------------------------------------------------------------------------------------------------------------|--------|
| `Affiliation`    | Numero de afiliação cadastrado no Checkout Cielo                                                             | string |
| `MerchantID`     | Identificador da loja no Checkout Cielo e Identificado para Criação de Links e consulta transacional         | GUID   |
| `Merchantkey`    | Chave de validação do MerchantID - Exclusivo para integração API Cielo Ecommerce                             | string |
| `IntegrationType`| Identificador do tipo de loja criada <br><br> 1 = API Cielo Ecommercer <br> 2 = Checkout Cielo               | GUID   |
 
## Ativar lojas

A Ativação de lojas é feita pela equipe de credenciamento Cielo. A Ativação ocorre apenas quando uma loja em pré-cadastro recebe uma Afiliação e uma chave de produção.

Caso uma loja cadastrada não seja ativada, entre em contato com a equipe de credenciamento e solicite informações.
