---
layout: post
title: "Introdução ao Backbone.js"
date: 2012-08-28 13:30
comments: true
author: Felipe Mesquita
categories:
- Backbone
- Javascript
---

**O que é Backbone.js?**

Backbone.js é um framework Javascript que facilita a manipulação de dados no front end de uma aplicação web, separando a visualização da manipulação dos dados. Para isso, fornece componentes para uma estrutura MVC, são eles: Model, View e Collection.

**Configurando um projeto para utilizar Backbone.js**

A única dependência do Backbone.js é a biblioteca <a href="http://underscorejs.org/">Underscore</a>. Para uma simples utilização do framework, basta utilizar a biblioteca dentro do seu HTML. <!-- more -->

{% gist 3489262 %}

**Backbone.Model**

Modelos são componentes que possuem uma estrutura para manipulação de dados.

Abaixo, um exemplo simples de criação de um modelo:

{% gist 3489288 %}

Para manipular os atributos de um modelo, utilizamos os métodos get() e set(). Vejamos um pouco mais abaixo:

{% gist 3489306 %}

Podemos definir valores padrão para os atributos dos modelos, além de poder manipular dados no momento da inicialização de um modelo.

{% gist 3489313 %}

**Backbone.View**

O componente View é utilizado para organizar a lógica de interface.

Abaixo, um exemplo simples de criação de uma View:

{% gist 3489317 %}

Para iniciar a View:

{% gist 3489338 %}

O atributo el representa o elemento DOM que a View utilizará para renderizar o layout. Existem outros dois atributos que podem ser utilizados para definir o DOM a ser utilizado pela View, são eles: tagName, id e className.

{% gist 3489347 %}

Neste caso, o elemento DOM utilizado pela View será um div com a classe projects.

{% gist 3505950 %}

Como foi dito anteriormente, o objetivo da View é organizar a lógica de interface utilizando os dados contidos em modelos. A seguir, um exemplo da View fazendo uso de dados contidos no Model.

{% gist 3505953 %}

Para este exemplo, temos o resultado abaixo:

{% gist 3505955 %}

Outro recurso bastante utilizado para exibição de dados é a biblioteca de templates. Abaixo, passamos a utilizar templates para renderizar as informações do projeto.

{% gist 3505960 %}

No exemplo acima, utilizamos a biblioteca de templates do próprio <a href="http://underscorejs.org/">Underscore</a>. Porém, pode ser utilizada qualquer biblioteca de templates, como por exemplo <a href="http://mustache.github.io/">Mustache</a>.

Uma funcionalidade bastante comum nas Views é a utilização de eventos, que são definidos através de um hash chamado events. Veja mais abaixo:

{% gist 3505967 %}

Este post é uma sequência de posts sobre o funcionamento do framework Backbone.js. Na sequência, explicaremos o funcionamento de Collections e integração com projetos REST.