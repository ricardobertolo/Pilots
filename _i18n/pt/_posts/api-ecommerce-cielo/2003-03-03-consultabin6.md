---
layout: manual
title: Consulta Bin
description: Manual integração para Pilotos
translated: true
toc_footers: true
categories: manual
sort_order: 6
tags:
  - API e-Commerce Cielo
language_tabs:
  json: JSON
  shell: cURL
---

# Consulta Bin

A “**Consulta de Bins**”  é um serviço de **pesquisa de dados do cartão**, de crédito ou débito, que os lojistas da API Cielo Ecommerce podem utilizar para validar se os dados preenchidos em tela sobre o cartão são válidos.

A “**consulta de Bins”** pe capaz retornar os seguintes dados sobre o cartão:

* **Bandeira do cartão**
* **Tipo de cartão:**Crédito, Débito ou Múltiplo (Crédito e Débito)
* **Nacionalidade do cartão:** estrangeiro ou nacional

Para mais informações sobre a API Cielo Ecommerce (Sandbox e Produção), acesse: <https://developercielo.github.io/Webservice-3.0/>

## Caso de Uso

Este é um exemplo de como usar a consulta bin para identificar o melhor perfil de comprador e ajudar sua conversão.

O **Consulta bin** é uma ferramenta da Cielo que permite identificar:

* Status: Valido ou invalido
* Origem do cartão: emitido no Brasil ou estrangeiro.
* O tipo do Cartão: Crédito/Débito ou ambos.
* A bandeira do cartão: Todas suportadas pela Cielo

Essas informações permitem tomar ações no momento do checkout para melhorar a conversão da loja.

Veja um exemplo de uso: **Consulta Bins + recuperação de carrinho**

Um marketplace chamada Submergível possui uma gama de meios de pagamento disponíveis para que suas lojas possam oferecer ao comprador, mas mesmo com toda essa oferta, ela continua com uma taxa de conversão que 

Conhecendo a função de consulta Bins da API Cielo Ecommerce, como ela poderia evitar a perda de carrinhos?

Ela pode aplicar a Consulta bins e 3 cenários!

1. Impedir erros com tipo de cartão
2. Oferecer recuperação de carrinhos online
3. Alertar sobre cartões internacionais

Impedir erros com tipo de cartão

O Submergível pode usar a consulta bins no carrinho para identificar 2 dos principais erros no preenchimento de formulários de pagamento: 

* **Bandeira errada** e confundir cartão de crédito com débito ○ Bandeiras erradas: Ao preencher o formulário de pagamento, é possível realizar uma consulta e já definir a bandeira correta. Esse é um método muito mais seguro do que sebasear em algoritmos no formulário, pois a base de bins consultada é a da bandeira emissora do cartão.
* **Confusões com cartões:** Ao preencher o formulário de pagamento, é possível realizar uma consulta e avisar ao consumidor se ele está usando um cartão de  débito quando na verdade deveria usar um  de débito

**Oferecer recuperação de carrinhos online**
* O Submergível pode usar a consulta bins no carrinho para oferecer um novo meio de pagamento caso a transação falhe na primeira tentativa.
* Realizando uma consulta no momento de preenchimento do formulário de pagamento, caso o cartão seja múltiplo (Crédito e Débito), o Submergível pode reter os dados do cartão, e caso a transação de crédito falhe, ele pode oferecer automaticamente ao consumidor uma transação de débito com o mesmo cartão.

**Alertar sobre cartões internacionais**
O Submergível pode usar a consulta bins no carrinho para alertar compradores internacionais, que caso o cartão não esteja habilitado para transacionar no Brasil, a transação será negada

## Integração

Basta realizar um `GET` enviado o BIN a nossa URL de consulta:

> https://`apiquerysandbox`.cieloecommerce.cielo.com.br/1/cardBin/`BIN`

### Requisição

**Consulta**:

``` json
https://apiquerysandbox.cieloecommerce.cielo.com.br/1/cardBin/420020
```

### Resposta

``` json
{
"Status": "00",
"Provider": "Visa",
"CardType": "Credit",
"ForeignCard": "true"
}
```

| Paramêtro       | Tipo  | Tamanho | Descrição                                                                                                                                                                                  |
|-----------------|-------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Status**      | Texto | 2       | Status da requisição de análise de Bins: <br><br> 00 – Analise autorizada <br> 01 – Bandeira não suportada <br> 02 – Cartão não suportado na consulta de bin <br> 73 – Afiliação bloqueada |
| **Provider**    | Texto | 255     | Bandeira do cartão                                                                                                                                                                         |
| **CardType**    | Texto | 20      | Tipo do cartão em uso : <br><br> Credito <br> Debito <br>Multiplo                                                                                                                          |
| **ForeingCard** | Texto | 255     | Se o cartão é emitido no exterior (False/True)                                                                                                                                             |

> *Atenção*: Em SANDBOX os valores retornados são simulações e não validações reais de BINS. Deve ser considerado apenas o retorno do Request e o seu formato. Para identificação real dos BINS, o ambiente de Produção deverá ser utilizado

# AVS

O **AVS** é um serviço para transações online onde é realizada uma validação cadastral através do batimento dos dados do endereço informado pelo comprador (endereço de entrega da fatura) na loja virtual, com os dados cadastrais do banco emissor do cartão.
Isso auxilia na redução do risco de chargeback. Deve ser utilizada para análise de vendas, auxiliando na decisão de captura da transação.

## Integração

Para realizar uma transação utilizando o **AVS**, o lojista deverá enviar uma requisição `POST` para a API Cielo Ecommerce,criando uma  transação que contenha o nó **AVS** dentro do nó `Payment.CreditCard`.

Vale Destacar que o AVS deve ser utilizado valendo-se das regras abaixo:

**Regras do AVS**

* Produtos permitidos: somente crédito.
* Disponível apenas para as bandeiras **Visa, Mastercard e Amex**.
* O retorno da consulta ao AVS é separado em dois itens: CEP e endereço.
* Cada um deles pode ter os seguintes valores: 

| Valor | Descrição                                                              |
|:-----:|------------------------------------------------------------------------|
| C     | Confere                                                                |
| N     | Não confere                                                            |
| I     | Indisponível                                                           |
| T     | Temporariamente indisponível                                           |
| X     | Serviço não suportado para esta Bandeira                               |
| E     | Dados enviados incorretos. Verificar se todos os campos foram enviados |

* É necessário que todos os campos contidos no nó AVS sejam preenchidos para que a analise seja realizada.
* Quando o campo não for aplicável (exemplo: complemento), deve ser enviada preenchido com NULL ou N/A
* Necessário habilitar a opção do AVS no cadastro. Para habilitar a opção AVS no cadastro ou consultar os bancos participantes, entre em contato com o Suporte Cielo eCommerce

Conteudo do **Nó AVS**


| Paramêtro      | Descrição                                       | Tipo  | Tamanho | Obrigatório |
|----------------|-------------------------------------------------|-------|:-------:|:-----------:|
| Avs.Cpf        | CPF do portador                                 | texto | 11      | Não         |
| Avs.ZipCode    | CEP do endereço de cobrança do portador         | texto | 8       | Não         |
| Avs.Street     | Logradouro do endereço de cobrança do portador  | texto | 50      | Não         |
| Avs.Number     | Número do endereço de cobrança do portador      | texto | 6       | Não         |
| Avs.Complement | Complemento do endereço de cobrança do portador | texto | 30      | Não         |
| Avs.District   | Bairro do endereço de cobrança do portador      | texto | 20      | Não         |

### Request

``` json
{
   "MerchantOrderId":"2014111703",
   "Payment":{
     "Type":"CreditCard",
     "Amount":15700,
     "Installments":1,
     "SoftDescriptor":"123456789ABCD",
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Teste Holder",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "Avs":{
        	"Cpf": "10939107716",
        	"ZipCode": "24320570",
        	"Street": "Estrada Caetano Monteiro",
        	"Number": "391",
        	"Complement": "Bl",
        	"District": "Niteroi"
    	}
     }
   }
}
```

### Response

``` json
{
    "MerchantOrderId": "2014111703",
    "Customer": {
        "Name": "[Guest]"
    },
    "Payment": {
        "ServiceTaxAmount": 0,
        "Installments": 1,
        "Interest": 0,
        "Capture": false,
        "Authenticate": false,
        "Recurrent": false,
        "CreditCard": {
            "CardNumber": "455187******0181",
            "Holder": "Teste Holder",
            "ExpirationDate": "12/2021",
            "SaveCard": false,
            "Brand": "Visa",
            "Avs": {
                "Cpf": "10939107716",
                "ZipCode": "24320570",
                "Street": "Estrada Caetano Monteiro",
                "Number": "391",
                "Complement": "Bl",
                "District": "Niteroi",
                "Status": 9,
                "ReturnCode": "I"
            }
        },
        "Tid": "10447480686IHEHPA33B",
        "ProofOfSale": "279003",
        "SoftDescriptor": "123456789ABCD",
        "Provider": "Cielo",
        "Eci": "7",
        "VelocityAnalysis": {
            "Id": "467a37d0-435e-4729-b1b4-6a2c4ea7360e",
            "ResultMessage": "Accept",
            "Score": 0
        },
        "PaymentId": "467a37d0-435e-4729-b1b4-6a2c4ea7360e",
        "Type": "CreditCard",
        "Amount": 15700,
        "ReceivedDate": "2017-08-22 15:45:06",
        "Currency": "BRL",
        "Country": "BRA",
        "ReturnCode": "14",
        "ReturnMessage": "Autorizacao negada",
        "Status": 3,
        "Links": [
            {
                "Method": "GET",
                "Rel": "self",
                "Href": "https://apiquery.cieloecommerce.cielo.com.br/1/sales/467a37d0-435e-4729-b1b4-6a2c4ea7360e"
            }
        ]
    }
}
```

| Paramêtro      | Descrição                                       | Tipo  | Tamanho |
|----------------|-------------------------------------------------|-------|:-------:|
| Avs.Cpf        | CPF do portador                                 | texto | 11      | 
| Avs.ZipCode    | CEP do endereço de cobrança do portador         | texto | 8       | 
| Avs.Street     | Logradouro do endereço de cobrança do portador  | texto | 50      |
| Avs.Number     | Número do endereço de cobrança do portador      | texto | 6       | 
| Avs.Complement | Complemento do endereço de cobrança do portador | texto | 30      |
| Avs.District   | Bairro do endereço de cobrança do portador      | texto | 20      |
| AvsCepReturnCode     | Situação do CEP enviado:<br><br>**C** - Confere<br>**N** - Não confere<br>**I** - Indisponível<br>**T** - Temporariamente indisponível<br>**X** - Serviço não suportado para esta Bandeira<br>**E** - Dados enviados incorretos. Verificar se todos os campos foram enviados<br> | Texto   | 1       |
| AvsAddressReturnCode | Analise do endereço enviado:<br><br>**C** - Confere<br>**N** - Não confere<br>**I** - Indisponível<br>**T** - Temporariamente indisponível<br>**X** - Serviço não suportado para esta Bandeira<br>**E** - Dados enviados incorretos. Verificar se todos os campos foram enviados<br> | Texto   | 1       |
