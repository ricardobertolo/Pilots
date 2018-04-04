---
layout: manual
title: Abrir Token
description: Manual integração para Pilotos
translated: true
toc_footers: true
categories: manual
sort_order: 1
tags:
  - API e-Commerce Cielo
language_tabs:
  json: JSON
  shell: cURL
---

# Abrir Token

Para consultar uma cartão aberto via API 3.0, é necessario realizar um `GET` utilizando o endpoint abaixo:

<aside class="request"><span class="method get">GET</span> <span class="endpoint">https://apiquery.cieloecommerce.cielo.com.br/1/card/opencard/{token}</span></aside> 

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