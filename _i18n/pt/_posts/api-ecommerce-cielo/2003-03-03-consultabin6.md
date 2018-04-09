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
O serviço retornar os seguintes dados sobre o cartão:

* **Bandeira do cartão**
* **Tipo de cartão:**Crédito, Débito ou Múltiplo (Crédito e Débito)
* **Nacionalidade do cartão:** estrangeiro ou nacional

Essas informações permitem tomar ações no momento do checkout para melhorar a conversão da loja.

## Caso de Uso

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

### Request

Basta realizar um `GET` enviado o BIN a nossa URL de consulta:

<aside class="request"><span class="method get">GET</span><span class="endpoint">https://`apiquerysandbox`.cieloecommerce.cielo.com.br/1/cardBin/`BIN`</span></aside>

``` json
https://apiquerysandbox.cieloecommerce.cielo.com.br/1/cardBin/420020
```

|Campo|Descrição|
|-----|---------|
|`BIN`|6 primeiros dígitos do cartão|

### Response

``` json
{
"Status": "00",
"Provider": "Visa",
"CardType": "Credit",
"ForeignCard": "true"
}
```

| Paramêtro     | Tipo  | Tamanho | Descrição                                                                                                                                                                                  |
|---------------|-------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Status`      | Texto | 2       | Status da requisição de análise de Bins: <br><br> 00 – Analise autorizada <br> 01 – Bandeira não suportada <br> 02 – Cartão não suportado na consulta de bin <br> 73 – Afiliação bloqueada |
| `Provider`    | Texto | 255     | Bandeira do cartão                                                                                                                                                                         |
| `CardType`    | Texto | 20      | Tipo do cartão em uso : <br><br> Credito <br> Debito <br>Multiplo                                                                                                                          |
| `ForeingCard` | Texto | 255     | Se o cartão é emitido no exterior (False/True)                                                                                                                                             |

> **Atenção**: Em SANDBOX os valores retornados são simulações e não validações reais de BINS. Deve ser considerado apenas o retorno do Request e o seu formato. Para identificação real dos BINS, o ambiente de Produção deverá ser utilizado.
