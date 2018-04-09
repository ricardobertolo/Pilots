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

| PROPRIEDADE            | DESCRIÇÃO                                                                                      | TIPO   | TAMANHO | OBRIGATÓRIO |
|------------------------|------------------------------------------------------------------------------------------------|--------|---------|-------------|
| shipping               | Nó contendo informações de entrega do produto                                                  |        |         |             |

### Post de Notificação

## Ativar lojas

# Link de Pagamento

A **API Link de Pagamentos** permite ao lojista criar, editar e consultar links de pagamentos. 

Seu principal objetivo é permitir que lojas possam criar links de pagamento (Botões ou QR Codes), através de seus próprios sistemas, sem a necessidade de acessar o Backoffice do Checkout Cielo e compartilhar com seus clientes.

> **Atenção**: O link de pagamentos não é uma URL DE **PEDIDO/TRANSAÇÃO**. Ele é um "carrinho" que pode ser reutilizado inúmeras vezes.

> **Atenção**: Para receber notificações sobre transações originadas de Links de pagamento é **OBRIGATÓRIO** o cadastro da URL de Notificação.

## Criar um Link

Você pode criar um link para disponibilizá-los aos seus clientes para pagamentos. Para criar diversos links, você pode efetuar várias requisições.

<aside class="request"><span class="method post">POST</span><span class="endpoint">https://cieloecommerce.cielo.com.br/api/public/v1/products/</span></aside>

> Header: `Authorization: Bearer {access_token}`


**Requisição:**

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

| PROPRIEDADE                 | DESCRIÇÃO              | TIPO                                                                                | TAMANHO    | OBRIGATÓRIO |
|-----------------------------|------------------------|-------------------------------------------------------------------------------------|------------|-------------|
| type                        | Tipo de venda a ser realizada através do link de pagamento: <br><br>**Asset** – Material Físico<br>**Digital** – Produto Digital<br>**Service** – Serviço<br>**Payment** – Pagamentos Simples<br>**Recurrent** – Pagamento Recorrente | String                                                                          | 255        | SIM        |
| name                        | Nome do produto        | String                                                                              | 128        | SIM        |
| description                 | Descrição do produto que será exibida na tela de Checkout caso a opção show_description seja verdadeira.Pode-se utilizar o caracter pipe ` | ` caso seja desejável quebrar a linha ao apresentar a descrição na tela de Checkout | String     | 512        |
| showDescription             | Flag indicando se a descrição deve ou não ser exibida na tela de Checkout                                    | String     | --         | Não         |
| price                       | Valor do produto em **centavos**                                                                             | Int        | 1000000    | SIM         |
| expirationDate              | Data de expiração do link. Caso uma data senha informada, o link se torna indisponível na data informada. Se nenhuma data for informada, o link não expira.| String                                                                             | YYYY-MM-DD | Não         |
| weight                      | Peso do produto em **gramas**| String                                                                        | 2000000    | Não         |
| softDescriptor              | Descrição a ser apresentada na fatura do cartão de crédito do portador.| String                              | 13         | Não         |
| maxNumberOf<br>Installments | Número máximo de parcelas que o comprador pode selecionar na tela de Checkout.Se não informado será utilizada as configurações da loja no Checkout Cielo.| int| 2| Não|

**Dados do Frete**

| PROPRIEDADE            | DESCRIÇÃO                                                                                      | TIPO   | TAMANHO | OBRIGATÓRIO |
|------------------------|------------------------------------------------------------------------------------------------|--------|---------|-------------|
| shipping               | Nó contendo informações de entrega do produto                                                  |        |         |             |
| shipping.name          | Nome do frete. **Obrigatório para frete tipo “FixedAmount”**                                   | string | 128     | Sim         |
| shipping.price         | O valor do frete. **Obrigatório para frete tipo “FixedAmount”**                                | int    | 100000  | Sim         |
| shipping.originZipCode | Cep de origem da encomenda. Obrigatório para frete tipo “Correios”. Deve conter apenas números | string | 8       | Não         |
|shipping.type|Tipo de frete.<br>**Correios** – Entrega pelos correios<br>**FixedAmount** – Valor Fixo<br>**Free** - Grátis<br>**WithoutShippingPickUp** – Sem entrega com retirada na loja<br>**WithoutShipping** – Sem entrega<br><br>Se o tipo de produto escolhido for “**Asset**”, os tipos permitidos de frete são: _**“Correios, FixedAmount ou Free”**_.<br><br>Se o tipo produto escolhido for “**Digital**” ou “**Service**”, os tipos permitidos de frete são: _**“WithoutShipping, WithoutShippingPickUp”**_.<br><br>Se o tipo produto escolhido for “**Recurrent**” o tipo de frete permitido é: _**“WithoutShipping”**_.|string|255|Sim|

**Dados da Recorrência**

|PROPRIEDADE|DESCRIÇÃO|TIPO|TAMANHO|OBRIGATÓRIO|
|-----------|---------|----|-------|-----------|
|recurrent|Nó contendo informações da recorrência do pagamento.Pode ser informado caso o tipo do produto seja “Recurrent”|-|-|
|recurrent.interval|Intervalo de cobrança da recorrência.<br><br>**Monthly** - Mensal<br>**Bimonthly** - Bimensal<br>**Quarterly** - Trimestral<br>**SemiAnnual** - Semestral<br>**Annual** – Anual<br>|string|128|Não|
|recurrrent.endDate|Data de término da recorrência. Se não informado a recorrência não terá fim, a cobrança será realizada de acordo com o intervalo selecionado indefinidamente.|string|128|Não|


**Resposta**
```
"HTTP Status": 201 – Created
```
```
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

| PROPRIEDADE | TIPO   | DESCRIÇÃO                                                                                                                     |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------|
| id          | guid   | Identificador único do link de pagamento.Pode ser utilizado para consultar, atualizar ou excluir o link.                      |
| shortUrl    | string | Representa o link de pagamento que ao ser aberto, em um browser, apresentará a tela do Checkout Cielo.                        |
| links       | object | Apresenta as operações disponíveis e possíveis (RESTful hypermedia) de serem efetuadas após a criação ou atualização do link. |

## Consultar um Link

Consultar um link existente pelo seu identificador.
**Definição**

> `GET`: https://cieloecommerce.cielo.com.br/api/public/v1/products/`{id}` 


**Header:**

```
Authorization: Bearer {access_token}
```

**Resposta**
```
HTTP Status: 200 – OK
```
```
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
**OBS**: Mesmos dados retornados na resposta de criação do link.

## Atualizar um Link

Atualiza um link pelo seu identificador.
**Definição**

> `PUT`: https://cieloecommerce.cielo.com.br/api/public/v1/products/`{id}` 


**Header:**

```
Authorization: Bearer {access_token}
```

**Requisição**
```
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

 
**Resposta**
```
HTTP Status: 200 – OK
```

```
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
**OBS**: Mesmos dados retornados na resposta de criação do link.

## Excluir um Link

Exclui um link pelo seu identificador.

**Definição**

> `DELETE`: https://cieloecommerce.cielo.com.br/api/public/v1/products/`{id}` 


**Header:**

```
Authorization: Bearer {access_token}
```

**Resposta**

```
HTTP Status: 204 – No Content
```

### Códigos de Status HTTP

| CÓDIGO                      | DESCRIÇÃO                                                                                                  |
|-----------------------------|------------------------------------------------------------------------------------------------------------|
| 200 - OK                    | Tudo funcionou corretamente.                                                                               |
| 400 – Bad Request           | A requisição não foi aceita. Algum parâmetro não foi informado ou foi informado incorretamente.            |
| 401 - Unauthorized          | O token de acesso enviado no header da requisição não é válido.                                            |
| 404 – Not Found             | O recurso sendo acessado não existe. Ocorre ao tentar atualizar, consultar ou excluir um link inexistente. |
| 500 – Internal Server Error | Ocorreu um erro no sistema.                                                                                |

# Controle transacional 

A **API de Controle Transacional** permite ao lojista realizar operações sobre os pedidos que antes eram possíveis somente através do Backoffice do Checkout Cielo. 

As operações possíveis de serem realizadas são: 
* **Consulta** – consultar uma transação
* **Captura** – capturar uma transação com valor total
* **Cancelamento** – cancelar uma transação com valor total

Seu principal objetivo é permitir que lojas e plataformas possam automatizar as operações através de seus próprios sistemas. 

> Endereço: https://cieloecommerce.cielo.com.br/api/public/v2/orders/  

## Pré-requisitos

Para realizar o controle transacional no Checkout Cielo é OBRIGATÓRIO que a loja possua um dos dois modelos de notificação abaixo configurado:

* URL de Notificação via **POST**
* URL de Notificação via **JSON**

A notificação é obrigatorio pois todos os comandos da API (Consulta / Captura / Cancelamento) usam o identificado unico da transação, chamado de `Checkout_Cielo_Order_Number`.

O `Checkout_Cielo_Order_Number` é um identificado único gerado apenas quando o pagamento é *finalizado na tela transacional*. Ele é enviado apenas pela *URL de Notificação* e não pelo Response da criação da tela transacional. 

## Consultar transação

Permite consultar uma transação pelo número do pedido gerado pelo Checkout Cielo

> `GET` https://cieloecommerce.cielo.com.br/api/public/v2/orders/`{checkout_cielo_order_number}`

**Header:**

```
Authorization: Bearer {access_token}
```


**Resposta**

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
```
HTTP Status: 200 – OK
```

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
