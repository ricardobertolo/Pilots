---
layout: manual
title: Abrir Token
description: Manual integração para Pilotos
translated: true
toc_footers: true
categories: manual
sort_order: 2
tags:
  - API e-Commerce Cielo
language_tabs:
  json: JSON
  shell: cURL
---

# Abrir Token

Para consultar uma cartão aberto via API 3.0, é necessario realizar um `GET` utilizando o endpoint abaixo:

> Para utilizar esta funcionalidade é necessario que a loja possua uma permissão especial concedida pelo time de Produtos Cielo..

## Request 

|Header          |Descrição                                                                          |
|----------------|-----------------------------------------------------------------------------------|
|`MerchantId`    |Identificador do estabelecimento, fornecido no momento do credenciamento.          |
|`MerchantKey`   |Chave do estabelecimento, fornecida no momento do credenciamento.                  |

<aside class="request"><span class="method get">GET</span> <span class="endpoint">https://apiquery.cieloecommerce.cielo.com.br/1/card/{token}?masked=false</span></aside> 

## Response

``` json
{  
   "CardNumber": "Cartão aberto", 
   "Holder": "Portador do cartão", 
   "ExpirationDate": "12/2018" 
}
``` 

|Parametro       |Descrição                            |
|----------------|-------------------------------------|
|`CardNumber`    |Número do cartão tokenizado          |
|`Holder`        |Nome do portador tokenizado          |
|`ExpirationDate`|Data de validade do cartão tokenizado|