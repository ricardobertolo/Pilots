---
layout: manual
title: Zero Auth
description: Manual integração para Pilotos
translated: true
toc_footers: true
categories: manual
sort_order: 4
tags:
  - API e-Commerce Cielo
language_tabs:
  json: JSON
  shell: cURL
---

# Zero Auth

O **Zero Auth** é uma ferramenta de validação de cartões da API Cielo. A validação permite que o lojista saiba se o cartão é valido ou não antes de enviar a transação para autorização, antecipando o motivo de uma provável não autorização..

O **Zero Auth** pode ser usado de 3 maneiras:

1. **Padrão** - Envio de um cartão padrão, sem tokenização ou analises adicionais
2. **Com cartão Tokenizado** - Envio de um TOKEN 3.0 para analise
3. **Com AVS** - Utilizando o AVS Cielo para validar o cartão

É importante destacar que o Zero Auth **não retorna ou analisa** os seguintes itens:

1. Limite de crédito do cartão
2. Informações sobre o portador
3. Não aciona a base bancaria (dispara SMS so portador)

O Zero Auth suporta as seguintes bandeiras:

* Visa
* MasterCard

## Caso de uso

Este é um exemplo de como usar o zero auth para melhorar sua conversão de vendas

O Zero Auth é uma ferramenta da Cielo que permite verificar se um cartão está valido para realizar uma compra antes que o pedido seja finalizado. Ele faz isso simulando uma autorização, mas sem afetar o limite de crédito ou alertar o portados do cartão sobre o teste.

Ela não informa o limite ou características do cartão ou portador, mas simula uma autorização Cielo, validando dados como:

1. Se  o cartão está valido junto ao banco emissor
2. Se o cartão possui limite disponível
3. Se o cartão funciona no Brasil
4. Se o número do cartão está correto
5. Se o CVV é valido

O Zero Auth também funciona com Cartões tokenizados na Api Cielo Ecommerce 

Veja um exemplo de uso: 

**Zero auth como validador de cartão**

Uma empresa de Streaming chamada FlixNet possui um serviço via assinatura, onde além de realizar uma recorrência, ela possui cartões salvos e recebe novas inscrições diariamente. 
Todas essas etapas exigem que transações sejam realizadas para obter acesso a ferramenta, o que eleva o custo da FlixNet caso as transações não sejam autorizadas. 

Como ela poderia reduzir esse custo? Validando o cartão antes de envia-lo a autorizado.

A FlixNet usa o Zero Auth em 2 momento diferente:

* **Cadastro**: é necessário incluir um cartão para ganhar 30 dias grátis no primeiro mês. 
	
O problema é que ao se encerrar esse período, se o cartão for invalido, o novo cadastro existe, mas não funciona, pois, o cartão salvo é invalido. A Flix Net resolveu esse problema testando o cartão com o Zero Auth no momento do cadastro, assim, ela já sabe se o cartão está valido e libera a criação da conta. Caso não o cartão não seja aceito, a FlixNet pode sugerir o uso de um outro cartão.
	
* **Recorrência**: todo mês, antes de realizar a cobrança da Assinatura, a Flixnet testa o cartão com o zero auth, assim sabendo se ele será autorizado ou não.  Isso ajuda o FlixNet a prever quais cartões serão negados, já acionando o assinante para atualização do cadastro antes do dia de pagamento.

## Integração

Para realizar a consulta ao Zero Auth, o lojista deverá enviar uma requisição `POST` para a API Cielo Ecommerce, simulando uma transação. O `POST` deverá ser realizado nas seguintes URL: 

> Sandbox: https://`apisandbox`.cieloecommerce.cielo.com.br/1/`zeroauth`

> Produção: https://`api`.cieloecommerce.cielo.com.br/1/`zeroauth`

Cada tipo de validação necessita de um contrato tecnico diferente. Eles resultarão em _responses diferenciados_.

Abaixo, a listagem de campos da Requisição:

| Paramêtro      | Descrição                                                                                                             | Tipo    | Tamanho | Obrigatório |
|----------------|-----------------------------------------------------------------------------------------------------------------------|---------|---------|:-----------:|
| CardType       | Define o tipo de cartão utilizados:<br><br>*CreditCard*<br>*DebitCard*<br><br>Se não enviado, CreditCard como default | Texto   | 255     | Não         |
| CardNumber     | Número do Cartão do Comprador                                                                                         | Texto   | 16      | sim         |
| Holder         | Nome do Comprador impresso no cartão.                                                                                 | Texto   | 25      | não         |
| ExpirationDate | Data de e validade impresso no cartão.                                                                                | Texto   | 7       | sim         |
| SecurityCode   | Código de segurança impresso no verso do cartão.                                                                      | Texto   | 4       | não         |
| SaveCard       | Booleano que identifica se o cartão será salvo para gerar o CardToken.                                                | Boolean | ---     | Não         |
| Brand          | Bandeira do cartão: <br><br>Visa<br>Master<br>| Texto   | 10      | não         |
| CardToken      | Token do cartão na 3.0                                                                                                | GUID    | 36      | Condicional |

### Requisição

Conteudo do **POST - PADRÃO**

``` json
{
    "CardNumber":"1234123412341231",
    "Holder":"Alexsander Rosa",
    "ExpirationDate":"12/2021",
    "SecurityCode":"123",
    "SaveCard":"false",
    "Brand":"Visa"
}
```

Conteudo do **POST - COM TOKEN**

``` json
{
  "CardToken":"23712c39-bb08-4030-86b3-490a223a8cc9",
    "SaveCard":"false",
    "Brand":"Visa"
}
```

### Resposta

A resposta sempre retorna se o cartão pode ser autorizado no momento. Essa informação apenas significa que o _cartão está valido a transacionar_, mas não necessariamente indica que um determinado valor será autorizado.

Abaixo os campos retornados após a validação:


| Paramêtro     | Descrição                                                                       | Tipo    | Tamanho |
|---------------|---------------------------------------------------------------------------------|---------|:-------:|
| Valid         | Situação do cartão:<br> **True** – Cartão válido<BR>**False** – Cartão Inválido | Boolean | ---     |
| ReturnCode    | Código de retorno                                                               | texto   | 2       |
| ReturnMessage | Mensagem de retorno                                                             | texto   | 255     |

Response: **POSITIVA - Cartão Válido**

``` json
{
        "Valid": true,
        "ReturnCode": “00”,
        "ReturnMessage", “Transacao autorizada”
}
```

> Consulte <https://developercielo.github.io/Webservice-3.0/#códigos-de-retorno-das-vendas> para visualizar a descrição dos códigos de retorno. 

> O código de retorno **85 representa sucesso no Zero Auth**, os demais códigos são definidos de acordo com a documentação acima.

Response: **NEGATIVA - Cartão Inválido**

``` json
{
       "Valid": false,
       "ReturnCode": "57",
       "ReturnMessage": "Autorizacao negada"
}
```

Response: **NEGATIVA - Cartão com bandeira inválida**

``` json
  {    
      "Code": 57,     
      "Message": "Bandeira inválida"   
  }
```

Caso ocorra algum erro no fluxo, onde não seja possível validar o cartão, o serviço irá retornar erro: 
* 500 – Internal Server Erro

## Zero Auth com AVS

O **AVS** é um serviço para transações online onde é realizada uma validação cadastral através do batimento dos dados do endereço informado pelo comprador (endereço de entrega da fatura) na loja virtual, com os dados cadastrais do banco emissor do cartão.
Isso auxilia na redução do risco de chargeback. Deve ser utilizada para análise de vendas, auxiliando na decisão de captura da transação.

**Regras do AVS**

* Disponível apenas para as bandeiras Visa, Mastercard e AmEx.
* Produtos permitidos: somente crédito.
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
* Quando o campo não for aplicável (exemplo: complemento), deve ser enviada preenchia com NULL ou N/A
* Necessário habilitar a opção do AVS no cadastro. Para habilitar a opção AVS no cadastro ou consultar os bancos participantes, entre em contato com o Suporte Cielo eCommerce


| Paramêtro      | Descrição                                       | Tipo  | Tamanho | Obrigatório |
|----------------|-------------------------------------------------|-------|:-------:|:-----------:|
| Avs.Cpf        | CPF do portador                                 | texto | 11      | Não         |
| Avs.ZipCode    | CEP do endereço de cobrança do portador         | texto | 8       | Não         |
| Avs.Street     | Logradouro do endereço de cobrança do portador  | texto | 50      | Não         |
| Avs.Number     | Número do endereço de cobrança do portador      | texto | 6       | Não         |
| Avs.Complement | Complemento do endereço de cobrança do portador | texto | 30      | Não         |
| Avs.District   | Bairro do endereço de cobrança do portador      | texto | 20      | Não         |

Conteudo do **POST - COM AVS**

``` json
{
    "CardType": "CreditCard",
    "CardNumber":"1234123412341231",
    "Holder":"Teste Holder",
    "ExpirationDate":"12/2021",
    "SecurityCode":"123",
    "SaveCard":"false",
    "Brand":"Visa",
    "Avs":{
        "Cpf": "12387719719",
        "ZipCode": "20241180",
        "Street": "Rua da Glória",
        "Number": "214",
        "Complement": "ap 203",
        "District": "Rio de Janeiro"
    }
}
```

Response: **POSITIVA - Com AVS**

``` json
{
       "Valid": true,
       "ReturnCode": "85",
       "ReturnMessage": "Transacao autorizada",
       "AvsCepReturnCode": "I",
       "AvsAddressReturnCode": "I"
}
```

Conteudo do Response

| Paramêtro            | Descrição                                                             | Tipo    | Tamanho |
|----------------------|-----------------------------------------------------------------------|---------|---------|
| Valid                | Situação do cartão:<br> **True** – Cartão válido<BR>**False** – Cartão Inválido | Boolean | ---     |
| ReturnCode           | Código de retorno                                                     | texto   | 2       |
| ReturnMessage        | Mensagem de retorno                                                   | texto   | 255     |
| AvsCepReturnCode     | Situação do CEP enviado:<br><br>**C** - Confere<br>**N** - Não confere<br>**I** - Indisponível<br>**T** - Temporariamente indisponível<br>**X** - Serviço não suportado para esta Bandeira<br>**E** - Dados enviados incorretos. Verificar se todos os campos foram enviados<br> | Texto   | 1       |
| AvsAddressReturnCode | Analise do endereço enviado:<br><br>**C** - Confere<br>**N** - Não confere<br>**I** - Indisponível<br>**T** - Temporariamente indisponível<br>**X** - Serviço não suportado para esta Bandeira<br>**E** - Dados enviados incorretos. Verificar se todos os campos foram enviados<br> | Texto   | 1       |
