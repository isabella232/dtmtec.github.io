---
layout: post
title: "O elemento section do html5"
date: 2012-10-24 14:40
comments: true
author: Rafael Mahler
categories:
- html5
- section
- html
---

Em algum momento você deve ter se perguntado sobre as situações corretas para utilizar as novas tags de html5. Como e onde utilizar `<header>`, `<nav>`, `<section>`, `<article>`... na sua marcação. De fato, você não precisa reaprender html ou jogar fora seu código antigo, que vai continuar funcionando perfeitamente. Mas você pode aprimorar sua marcação, e na velocidade que julgar adequada. Isso porque o html5 não é um pacote fechado, mas sim um conjunto de novas funcionalidades a serem exploradas e experimentadas.

Na verdade, é muito simples entender estes novos elementos semânticos, pois eles se explicam pelo próprio nome que carregam. É fácil entender a que se propõe um `<header>`, e não há mistério algum em como aplicá-lo no seu html.
<!-- more -->
Porém a ideia de que, tomando as devidas precauções de compatibilidade dos browsers, poderíamos simplesmente passar a utilizar as novas tags no lugar de divs, não me parece suficiente para a sua correta aplicação no código. Meu ponto envolve o uso do elemento `<section>`, que pode parecer confuso e de difícil discernimento. Pesquisei sobre algumas práticas de uso do `<section>` e notei que, equivocadamente, este elemento tem sido regularmente usado com propósitos de estilização.

Segundo sua <a target="_blank" title="Definição" href="http://www.whatwg.org/specs/web-apps/current-work/multipage/sections.html#the-section-element">definição</a>, *"o elemento `<section>`section representa uma seção genérica de um documento ou aplicação. Uma seção, neste contexto, é um agrupamento de conteúdo, geralmente com um título."*

Quando temos "agrupamento de conteúdo" com "título", podemos entender que existe um significado que vai além da finalidade de estilização.

Na especificação, ainda vemos que *"exemplos de `<section>` podem ser capítulos, páginas em abas de uma caixa de diálogo, ou as seções numeradas de uma tese. A página inicial de um website pode ser separada em `<section>` para introdução, itens de notícias, informações para contato."*

Me parece tentador, que ao escrever o html, se utilize a tag `<section>` para estilizar determinado agrupamento de conteúdo. Nesse caso, recomenda-se o uso de `<div>` se a finalidade do elemento for a estilização.

A especificação segue:
*"Quando um elemento é necessário apenas para fins de estilo ou como uma conveniência para criação de scripts, os autores são incentivados a usar o elemento `<div>`. A regra geral é que o elemento `<section>` é apropriado somente se o conteúdo deste elemento puder ser listado explicitamente no esboço do documento."*

Ou seja, todo conteúdo dentro do elemento `<section>` deve estar relacionado. Isso reforça a presença do título (heading). Aliás, se no seu elemento `<section>` não existir um `<h1>`, isso é um forte indício de que ele não deve ser um `<section>`.

E se eu quiser estilizar o meu elemento `<section>`? Acredito que isso não seja um problema. É somente uma questão de finalidade de uso da tag.

Outra questão levantada, é quando usar `<article>` no lugar de `<section>`. No curso *Functional HTML5 & CSS3* do <a target="_blank" title="Codeschool" href="http://www.codeschool.com">Codeschool</a>, podemos ver que uma `<section>` pode conter vários elementos `<article>`, pois uma `<section>` é um agrupamento de conteúdo relacionado entre si. Porém, você não precisa, necessariamente, envolver um `<article>` em uma `<section>`. Além disso, uma boa dica para saber quando usar `<article>` é se perguntar se seu conteúdo tem potencial distribuível ou reutilizável (rss, twitter, etc...) ou se pelo menos existe essa intenção.

Espero ter esclarecido um pouco do assunto em torno do elemento `<section>`. Se você tiver informações adicionais sobre seu uso ou alguma correção sobre o que foi exposto aqui, deixe sua contribuição.
