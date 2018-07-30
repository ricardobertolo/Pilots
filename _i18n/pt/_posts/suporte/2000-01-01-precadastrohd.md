---
layout: tutorial
title:  Pré Cadastro no Admin
description: Manual PC
categories: tutorial
sort_order: 2
tags:
  - Suporte & HelpDesk
---

# O que é o pré-Cadastro Cielo?

## Descrição

O pré cadastro Cielo é uma API disponibilizada pela Cielo para que plataformas possam enviar dados cadastrais a um Banco de dados apartado do SEC. Uma vez recebidos, esses dados ficarão disponiveis para avaliação da equipe de credenciamento Cielo.
Se todas as informações forem válidas, a equipe de credenciamento poderá ativar o pré-cadastro, gerando uma loja e-commerce coEC e Chave de produção valida.

## Objetivos

O Pré cadastro cobre/atinge 3 objetivos:

|Objetivo|Descrição|
|--------|---------|
|**Aumentar canais de credenciamento**| A API será oferecida a Plataformas e parceiros Cielo para que esses possam criar em seus sites formularios de credenciamento. O Plataforma fára o processo de coletar dados como **Informações bancarias, dados do lojista e dados da loja**, auemnto a capilaridade de pontos de credenciamento Cielo|
|**Catalogar e padronizar Plataformas**| Lojas criadas pela API de pré cadastro serão vinculadas automaticamente a sua plataforma de origem, assim permitindo uma melhor rastreabilidade de cadastros|
|**Facilitar a criação de lojas**|Como as informações de cadastro ja estarão registradas na base de dados, o HD Cielo apenas precisará confirmar se o EC ja existe no SEC, assim completando os campos EC + Chave de Produção. Não será necessario criar uma loja e-commerce na 3.0 ou Checkout |

> O Pré cadastro está disponivel para **Checkout Cielo** e **Api Cielo E-commerce (3.0)**

## Como funciona?

O Pré-cadastro é uma API que salva dados de lojas em uma base de dados apartada do SEC ou de outros produtos ecommerce. Esses dados serão posteriormente atualizados pelo HD Cielo com um EC e Chave de produção. Quando atualizados, a API de pré-cadastro acionará a base de dados 3.0 ou Checkout Cielo, criando uma loja na respectiva base. 

Em si o Pré-cadastro funciona seguinto as etapas abaixo:

| Etapa |  Ambiente  | Ação                                                                                                                                  |
|:-----:|:----------:|---------------------------------------------------------------------------------------------------------------------------------------|
|   1   | Plataforma | A **Plataforma** oferece um formulário a ser preenchido pelo Lojista que deseja uma Afiliação.                                        |
|   2   | Plataforma | A **Plataforma** acionará a **API P.Cadastro**, criando o registro na base apartada                                                   |
|   3   |    Admin   | O HD Cielo Buscará diariamente lojas na base do P.Cadastro; Com os dados disponiveis, o HD pesquisará no SEC a existência do lojista. |
|  3.1  |     SEC    | Existem 3 Cenarios a serem tratados: <BR><BR>  **Lojista não tem cadastro Cielo** - HD deve criar um EC no SEC alinhado com a solução cadastrada no P.Cadastro (3.0 ou Checkout)  <BR> **Lojista possui EC Fisico** - HD deve criar/Duplicar o EC no SEC alinhado com a solução cadastrada no P.Cadastro (3.0 ou Checkout)<BR>**Lojista ja possui EC na Solução** - HD deve duplicar o EC e gerar um novo cadastro na solução (Vincular ao EC original como Cadeia) <br> |
|   4   |    Admin   | Inserir o EC + Chave de produção no P.Cadastro. A Ativação será completada quando as duas chaves forem salvas                         |
|   5   |    Admin   | Uma vez ativado o P.Cadastro, basta dar continuidade com a ativação da loja 3.0/Checkout no Admin como uma loja padrão                |

# Manipulando cadastros

## Pesquisar

Para pesquisar uma loja Pré-cadastro, basta acessar a tela de pesquisa dentro da área Cielo no Admin.

IMAGEM

Nessa tela é possivel pesquisar p.Cadastros com bases em 3 tipos de informação:

|informação|Descrição|
|-|-|
|**Data de criação**|Data que o registro de da loja foi criada na base de P.Cadastro|
|**Tipo de integração**|Qual o tipo de cadastro será originado|
|**Plataforma**||

## Ativar

# Plataformas

## Plataformas integradas
