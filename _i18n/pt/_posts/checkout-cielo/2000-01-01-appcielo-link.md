---
layout: manual
title: App Cielo
description: Integração técnica via API
search: true
translated: true
toc_footers: true
categories: manual
sort_order: 5
tags:
  - Checkout Cielo
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

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://cieloecommerce.cielo.com.br/v2/public/v2/token</span></aside>

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

O processo de consulta de EC busca a existencia de afiliações e MerchanIDs na base de lojas Checkout Cielo.

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

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/PreRegistration/</span></aside>

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

# Link de Pagamento

A **API Link de Pagamentos** permite ao lojista criar, editar e consultar links de pagamentos. 

Seu principal objetivo é permitir que lojas possam criar links de pagamento (Botões ou QR Codes), através de seus próprios sistemas, sem a necessidade de acessar o Backoffice do Checkout Cielo e compartilhar com seus clientes.

> **Atenção**: 
> * O link de pagamentos não é uma URL DE **PEDIDO/TRANSAÇÃO**. Ele é um "carrinho" que pode ser reutilizado inúmeras vezes.
> * Para receber notificações sobre transações originadas de Links de pagamento é **OBRIGATÓRIO** o cadastro da **URL de Notificação** no backoffice do Checkout.

## Autenticação

O Processo de autenticação na API do link de pagamento é o **[Cielo OAUTH](https://docscielo.github.io/Pilots/manual/appcielo-link#cielo-oauth)**

## Criar Link

### Request

Para criar link de pagamentos Checkout, basta enviar realizar um POST com os dados do Link ao endpoint: 

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/products/</span></aside>

> Header: `Authorization: Bearer {access_token}`

``` json
{
   "Type":"Digital",
   "name" : "Pedido",
   "description" : "teste description",
   "price":"1000000",
   "weight": 2000000,
   "ExpirationDate":"2037-06-19",
   "maxNumberOfInstallments":"1",
   "quantity": 2,
   "Sku":"teste",
   "shipping":{
     "type":"WithoutShipping",
     "name": "teste",
     "price":"1000000000"
   },
   "SoftDescriptor" : "Pedido1234"
}
```
**Dados do produto**

| PROPRIEDADE               | DESCRIÇÃO                                                                                                                                                   | TIPO   | TAMANHO    | OBRIGATÓRIO |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|------------|-------------|
| `name`                    | Nome do produto                                                                                                                                             | String | 128        | SIM         |
| `description`             | Descrição do produto que será exibida na tela de Checkout caso a opção `show_description` seja verdadeira.                                                  | String | 512        |             |
| `showDescription`         | Flag indicando se a descrição deve ou não ser exibida na tela de Checkout                                                                                   | String | --         | Não         |
| `price`                   | Valor do produto em **centavos**                                                                                                                            | Int    | 1000000    | SIM         |
| `expirationDate`          | Data de expiração do link. Caso uma data senha informada, o link se torna indisponível na data informada. Se nenhuma data for informada, o link não expira. | String | YYYY-MM-DD | Não         |
| `weight`                  | Peso do produto em **gramas**                                                                                                                               | String | 2000000    | Não         |
| `softDescriptor`          | Descrição a ser apresentada na fatura do cartão de crédito do portador.                                                                                     | String | 13         | Não         |
| `maxNumberOfInstallments` | Número máximo de parcelas que o comprador pode selecionar na tela de Checkout.Se não informado será utilizada as configurações da loja no Checkout Cielo.   | int    | 2          | Não         |
| `type`                    | Tipo de venda a ser realizada através do link de pagamento: <br><br>**Asset** – Material Físico<br>**Digital** – Produto Digital<br>**Service** – Serviço<br>**Payment** – Pagamentos Simples<br>**Recurrent** – Pagamento Recorrente | String | 255 | SIM|

>  Dentro de `Description` Pode-se utilizar o caracter pipe `|` caso seja desejável quebrar a linha ao apresentar a descrição na tela de Checkout

**Dados do Frete**

| PROPRIEDADE              | DESCRIÇÃO                                                                                      | TIPO   | TAMANHO | OBRIGATÓRIO |
|--------------------------|------------------------------------------------------------------------------------------------|--------|---------|-------------|
| **shipping**             | Nó contendo informações de entrega do produto                                                  |        |         |             |
| `shipping.name`          | Nome do frete. **Obrigatório para frete tipo “FixedAmount”**                                   | string | 128     | Sim         |
| `shipping.price`         | O valor do frete. **Obrigatório para frete tipo “FixedAmount”**                                | int    | 100000  | Sim         |
| `shipping.originZipCode` | Cep de origem da encomenda. Obrigatório para frete tipo “Correios”. Deve conter apenas números | string | 8       | Não         |
| `shipping.type` |Tipo de frete.<br>**Correios** – Entrega pelos correios<br>**FixedAmount** – Valor Fixo<br>**Free** - Grátis<br>**WithoutShippingPickUp** – Sem entrega com retirada na loja<br>**WithoutShipping** – Sem entrega<br><br>Se o tipo de produto escolhido for “**Asset**”, os tipos permitidos de frete são: _**“Correios, FixedAmount ou Free”**_.<br><br>Se o tipo produto escolhido for “**Digital**” ou “**Service**”, os tipos permitidos de frete são: _**“WithoutShipping, WithoutShippingPickUp”**_.<br><br>Se o tipo produto escolhido for “**Recurrent**” o tipo de frete permitido é: _**“WithoutShipping”**_.|string|255|Sim|

**Dados da Recorrência**

| PROPRIEDADE          | DESCRIÇÃO                                                                                                                                                                           | TIPO   | TAMANHO | OBRIGATÓRIO |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|---------|-------------|
| **recurrent**        | Nó contendo informações da recorrência do pagamento.Pode ser informado caso o tipo do produto seja “Recurrent”                                                                      |        |         |             |
| `recurrent.interval` | Intervalo de cobrança da recorrência.<br><br>**Monthly** - Mensal<br>**Bimonthly** - Bimensal<br>**Quarterly** - Trimestral<br>**SemiAnnual** - Semestral<br>**Annual** – Anual<br> | string | 128     | Não         |
| `recurrrent.endDate` | Data de término da recorrência. Se não informado a recorrência não terá fim, a cobrança será realizada de acordo com o intervalo selecionado indefinidamente.                       | string | 128     | Não         |

### Response

> "HTTP Status": 201 – Created


``` json
{
    "id": "529aca91-2961-4976-8f7d-9e3f2fa8a0c9",
    "shortUrl": "http://bit.ly/2smqdhD",
    "type": "Asset",
    "name": "Pedido ABC",
    "description": "50 canetas - R$30,00 | 10 cadernos - R$50,00",
    "showDescription": false,
    "price": 8000,
    "weight": 4500,
    "shipping": {
        "type": "Correios",
        "originZipcode": "06455030"
    },
    "softDescriptor": "Pedido1234",
    "expirationDate": "2017-06-30T00:00:00",
    "maxNumberOfInstallments": 2,
    "links": [
        {
            "method": "GET",
            "rel": "self",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/product/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "PUT",
            "rel": "update",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/product/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "DELETE",
            "rel": "delete",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/product/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        }
    ]
}
```

Os dados retornados na resposta contemplam todos os enviados na requisição e dados adicionais referentes a criação do link.

| PROPRIEDADE   | TIPO   | DESCRIÇÃO                                                                                                                     |
|---------------|--------|-------------------------------------------------------------------------------------------------------------------------------|
| `id`          | guid   | Identificador único do link de pagamento.Pode ser utilizado para consultar, atualizar ou excluir o link.                      |
| `shortUrl`    | string | Representa o link de pagamento que ao ser aberto, em um browser, apresentará a tela do Checkout Cielo.                        |
| `links`       | object | Apresenta as operações disponíveis e possíveis (RESTful hypermedia) de serem efetuadas após a criação ou atualização do link. |

## Consultar Link

### Request

Para consultar um link existente basta realizar um `GET` informando o `ID` do link.

<aside class="request"><span class="method get">GET</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/products/{id}</span></aside>

> **Header:** Authorization: Bearer {access_token}

### Response

> HTTP Status: 200 – OK

``` json
{
    "id": "529aca91-2961-4976-8f7d-9e3f2fa8a0c9",
    "shortUrl": "http://bit.ly/2smqdhD",
    "type": "Asset",
    "name": "Pedido ABC",
    "description": "50 canetas - R$30,00 | 10 cadernos - R$50,00",
    "showDescription": false,
    "price": 8000,
    "weight": 4500,
    "shipping": {
        "type": "Correios",
        "originZipcode": "06455030"
    },
    "softDescriptor": "Pedido1234",
    "expirationDate": "2017-06-30",
    "maxNumberOfInstallments": 2,
    "links": [
        {
            "method": "GET",
            "rel": "self",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "PUT",
            "rel": "update",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "DELETE",
            "rel": "delete",
            "href": " https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        }
    ]
}
```

| PROPRIEDADE   | TIPO   | DESCRIÇÃO                                                                                                                     |
|---------------|--------|-------------------------------------------------------------------------------------------------------------------------------|
| `id`          | guid   | Identificador único do link de pagamento.Pode ser utilizado para consultar, atualizar ou excluir o link.                      |
| `shortUrl`    | string | Representa o link de pagamento que ao ser aberto, em um browser, apresentará a tela do Checkout Cielo.                        |
| `links`       | object | Apresenta as operações disponíveis e possíveis (RESTful hypermedia) de serem efetuadas após a criação ou atualização do link. |

> **OBS**: O Response da consulta contem os mesmos dados retornados na criação do link.

## Atualizar Link

### Request

Para Atualizar um link existente basta realizar um `GET` informando o `ID` do link.

<aside class="request"><span class="method put">PUT</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/products/{id}</span></aside>

> **Header**: Authorization: Bearer {access_token}

``` json
{
   "Type":"Asset",
   "name" : "Pedido ABC",
   "description" : "50 canetas - R$30,00 | 10 cadernos - R$50,00 | 10 Borrachas - R$10,00",
   "price": 9000,
   "expirationDate": "2017-06-30",
   "weight": 4700,
   "shipping":{
     "type":"FixedAmount",
     "name":"Entrega Rápida",
     "price":1000
   },
   "SoftDescriptor" : "Pedido1234",
   "maxNumberOfInstallments" : 2
}
```

### Response

> HTTP Status: 200 – OK

``` json
{
    "id": "529aca91-2961-4976-8f7d-9e3f2fa8a0c9",
    "shortUrl": "http://bit.ly/2smqdhD",
    "type": "Asset",
    "name": "Pedido ABC",
    "description": "50 canetas - R$30,00 | 10 cadernos - R$50,00 | 10 Borrachas - R$10,00",
    "showDescription": false,
    "price": 9000,
    "weight": 4700,
    "shipping": {
        "type": "FixedAmount",
        "name": "Entrega Rápida",
        "price": 1000
    },
    "softDescriptor": "Pedido1234",
    "expirationDate": "2017-06-30",
    "maxNumberOfInstallments": 2,
    "links": [
        {
            "method": "GET",
            "rel": "self",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "PUT",
            "rel": "update",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        },
        {
            "method": "DELETE",
            "rel": "delete",
            "href": "https://cieloecommerce.cielo.com.br/Api/public/v1/products/529aca91-2961-4976-8f7d-9e3f2fa8a0c9"
        }
    ]
}

```

| PROPRIEDADE   | TIPO   | DESCRIÇÃO                                                                                                                     |
|---------------|--------|-------------------------------------------------------------------------------------------------------------------------------|
| `id`          | guid   | Identificador único do link de pagamento.Pode ser utilizado para consultar, atualizar ou excluir o link.                      |
| `shortUrl`    | string | Representa o link de pagamento que ao ser aberto, em um browser, apresentará a tela do Checkout Cielo.                        |
| `links`       | object | Apresenta as operações disponíveis e possíveis (RESTful hypermedia) de serem efetuadas após a criação ou atualização do link. |

> **OBS**: O Response da consulta contem os mesmos dados retornados na criação do link.

## Excluir Link

### Request

Para excluir um link existente basta realizar um `DELETE` informando o `ID` do link.

<aside class="request"><span class="method put">DELETE</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/products/{id}</span></aside>

> **Header:** Authorization: Bearer {access_token}

### Response

> HTTP Status: 204 – No Content

## Códigos de Status HTTP

| CÓDIGO                          | DESCRIÇÃO                                                                                                  |
|---------------------------------|------------------------------------------------------------------------------------------------------------|
| **200 - OK**                    | Tudo funcionou corretamente.                                                                               |
| **400 – Bad Request**           | A requisição não foi aceita. Algum parâmetro não foi informado ou foi informado incorretamente.            |
| **401 - Unauthorized**          | O token de acesso enviado no header da requisição não é válido.                                            |
| **404 – Not Found**             | O recurso sendo acessado não existe. Ocorre ao tentar atualizar, consultar ou excluir um link inexistente. |
| **500 – Internal Server Error** | Ocorreu um erro no sistema.                                                                                |

# Controle transacional 

A **API de Controle Transacional** permite ao lojista modificar o status de os pedidos sem acessar o Backoffice do Checkout Cielo. 

As operações possíveis de serem realizadas são: 

* **Consulta** – consultar uma transação
* **Captura** – capturar uma transação com valor total
* **Cancelamento** – cancelar uma transação com valor total

Seu principal objetivo é permitir que lojas e plataformas possam automatizar as operações através de seus próprios sistemas. 

> **Endpoint Central** https://cieloecommerce.cielo.com.br/api/public/v2/orders/  

## Autenticação

O Processo de autenticação na API do link de pagamento é o **[Cielo OAUTH](https://docscielo.github.io/Pilots/manual/appcielo-link#cielo-oauth)**

## Pré-requisitos

Para realizar o controle transacional no Checkout Cielo é OBRIGATÓRIO que a loja possua um dos dois modelos de notificação abaixo configurado:

* URL de Notificação via **POST**
* URL de Notificação via **JSON**

A notificação é obrigatorio pois todos os comandos da API (Consulta / Captura / Cancelamento) usam o identificador único da transação, chamado de `Checkout_Cielo_Order_Number`.

O `Checkout_Cielo_Order_Number` é gerado apenas quando o pagamento é *finalizado na tela transacional*. Ele é enviado apenas pela *URL de Notificação* e não pelo Response da criação da tela transacional. 

## Consultar transação

### Request 

Para consultar uma transação pelo `Checkout_Cielo_Order_Number`, basta realizar um `GET`.

<aside class="request"><span class="method get">GET</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v2/orders/{checkout_cielo_order_number}</span></aside>

> **Header:** Authorization: Bearer {access_token}

### Response

> "HTTP Status": 200 – OK

``` json
{ 
    "merchantId": "c89fdfbb-dbe2-4e77-806a-6d75cd397dac", 
    "orderNumber": "054f5b509f7149d6aec3b4023a6a2957", 
    "softDescriptor": "Pedido1234", 
    "cart": { 
        "items": [ 
            { 
                "name": "Pedido ABC", 
                "description": "50 canetas - R$30,00 | 10 cadernos - R$50,00 | 10 Borrachas - R$10,00", 
                "unitPrice": 9000, 
                "quantity": 1, 
                "type": "1" 
            } 
        ] 
    }, 
    "shipping": { 
        "type": "FixedAmount", 
        "services": [ 
            { 
              "name": "Entrega Rápida", 
                "price": 2000 
            } 
        ], 
        "address": { 
            "street": "Estrada Caetano Monteiro", 
            "number": "391A", 
            "complement": "BL 10 AP 208", 
            "district": "Badu", 
            "city": "Niterói", 
            "state": "RJ" 
        } 
    }, 
    "payment": { 
        "status": "Paid", 
        "antifraud": { 
            "description": "Lojista optou não realizar a análise do antifraude." 
        } 
    }, 
    "customer": { 
        "identity": "12345678911", 
        "fullName": "Fulano da Silva", 
        "email": "exemplo@email.com.br", 
        "phone": "11123456789" 
    }, 
    "links": [ 
        { 
            "method": "GET", 
            "rel": "self", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/054f5b509f7149d6aec3b4023a6a2957" 
        }, 
        { 
            "method": "PUT", 
            "rel": "void", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/054f5b509f7149d6aec3b4023a6a2957/void" 
        } 
    ] 
}
```

merchantId 
orderNumber
softDescriptor 
    
    "cart": { 
        "items": [ 
            { 
                "name": "Pedido ABC", 
                "description": "50 canetas - R$30,00 | 10 cadernos - R$50,00 | 10 Borrachas - R$10,00", 
                "unitPrice": 9000, 
                "quantity": 1, 
                "type": "1" 
            } 
        ] 
    }, 
    
    "shipping": { 
        "type": "FixedAmount", 
        "services": [ 
            { 
              "name": "Entrega Rápida", 
                "price": 2000 
            } 
        ], 
        "address": { 
            "street": "Estrada Caetano Monteiro", 
            "number": "391A", 
            "complement": "BL 10 AP 208", 
            "district": "Badu", 
            "city": "Niterói", 
            "state": "RJ" 
        } 
    }, 
    
    
    "payment": { 
        "status": "Paid", 
        "antifraud": { 
            "description": "Lojista optou não realizar a análise do antifraude." 
        } 
    }, 
   
   
    "customer": { 
        "identity": "12345678911", 
        "fullName": "Fulano da Silva", 
        "email": "exemplo@email.com.br", 
        "phone": "11123456789" 
    }, 
   
   
   
    "links": [ 
        { 
            "method": "GET", 
            "rel": "self", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/054f5b509f7149d6aec3b4023a6a2957" 
        }, 
        { 
            "method": "PUT", 
            "rel": "void", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/054f5b509f7149d6aec3b4023a6a2957/void" 




## Capturar transação

Permite capturar uma transação pelo número do pedido.

**Captura Total**
> `PUT` https://cieloecommerce.cielo.com.br/api/public/v2/orders/`{checkout_cielo_order_number}`/capture 

**Captura Parcial**

> `PUT` https://cieloecommerce.cielo.com.br/api/public/v2/orders/`{checkout_cielo_order_number}`/capture?amount={Valor}


**OBS**: A captura parcial pode ser realizada apenas 1 vez e é exclusiva para cartão de crédito.

**Header:**

```
Authorization: Bearer {access_token}
```

**Resposta**

> HTTP Status: 200 – OK

```
{ 
    "success": true, 
    "status": 2, 
    "returnCode": "6", 
    "returnMessage": "Operation Successful", 
    "links": [ 
        { 
            "method": "GET", 
            "rel": "self", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/a9d517d81fb24b98b2d16eae2744be96" 
        }, 
        { 
            "method": "PUT", 
            "rel": "void", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/a9d517d81fb24b98b2d16eae2744be96/void" 
        } 
    ] 
} 
```

## Cancelar transação

Permite cancelar uma transação pelo número do pedido.

**Cancelamento total**

> `PUT` https://cieloecommerce.cielo.com.br/api/public/v2/orders/`{checkout_cielo_order_number}`/void  

**Camcelamento Parcial**

> `PUT` https://cieloecommerce.cielo.com.br/api/public/v2/orders/`{checkout_cielo_order_number}`/void?amount={Valor}


**OBS**: O cancelamento parcial pode ser realizada apenas após a captura. O cancelamento parcial pode ser realizado inumeras vezes até que o valor total seja cancelado.



**Header:**

```
Authorization: Bearer {access_token}
```

**Resposta**

> HTTP Status: 200 – OK


```
{ 
    "success": true, 
    "status": 2, 
    "returnCode": "6", 
    "returnMessage": "Operation Successful", 
    "links": [ 
        { 
            "method": "GET", 
            "rel": "self", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/a9d517d81fb24b98b2d16eae2744be96" 
        }, 
        { 
            "method": "PUT", 
            "rel": "void", 
            "href": "https://cieloecommerce.cielo.com.br/api/public/v2/orders/a9d517d81fb24b98b2d16eae2744be96/void" 
        } 
    ] 
} 

```
