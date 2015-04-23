---
layout: post
title: "TDD - Alguns motivos para usá-lo"
date: 2012-10-02 10:10
comments: true
author: Gustavo Kloh
categories:
- TDD
- testes
- best pratices
---

O Test Driven Development - TDD é uma técnica de desenvolvimento de software baseado num ciclo onde o desenvolvedor implementa uma nova funcionalidade começando pelos testes. O TDD foi criado por <a href="http://en.wikipedia.org/wiki/Kent_Beck">Kent Beck</a>, tendo declarado que a técnica encoraja o design de códigos simples e inspira confiança.

<!-- more -->

Inicialmente é escrito um teste pensando em como uma nova funcionalidade será desenvolvida. Esse teste deve falhar, pois nesse estágio é construído o comportamento da nova implementação. Após a falha do teste, o próximo passo consiste em implementar o código que irá satisfazer o comportamento escrito no teste anterior. Em seguida o teste é executado novamente, e aí sim ele deverá passar. Após o comportamento de uma funcionalidade ser definido em seu teste, e este ter passado existe a segurança para um próximo passo que é o refactoring do código desenvolvido, limpando repetições de código e quaisquer coisas que possam ser melhoradas, levando aos padrões de códigos desejados.

Mas por que usar TDD? Existem várias discussões e motivos sobre o uso e o porque do uso da técnica. Dentre eles podemos destacar alguns como:

* *O TDD não é uma técnica para testar software, e sim uma técnica para construir software* - através do TDD são definidos os comportamentos de classes, telas, métodos, etc. Então o desenvolvimento é realizado para satisfazer esses comportamentos, levando à funcionalidades desenvolvidas de forma mais sólida e objetiva.

* *Refactoring* - Um projeto desenvolvido guiado pelos testes fornece a segurança necessária para aumentar a qualidade do código utilizando refactoring. Através do TDD há mais segurança para remover código duplicado, melhorar padrões e alterar o funcionamento do sistema sem alterar o seu comportamento.

* *Documentação para desenvolvedores* - Os testes são escritos para pessoas, e não para os computadores. Dessa forma toda a suíte de testes serve como uma documentação para que se tenha um entendimento rápido e claro do que está desenvolvido.

* *Baixo Acoplamento* - Utilizando TDD o desenvolvedor é forçado a pensar nas responsabilidades individuais de cada método, e não direto na implementação do mesmo. Assim são tomadas primeiramente decisões de design de código gerando um baixo acoplamento do mesmo.

Por fim, o uso da técnica tem como resultado confiabilidade no que foi desenvolvido, reduzindo drasticamente o número de bugs, encorajando a manutenção e evolução apenas do que é necessário.
