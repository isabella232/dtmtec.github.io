---
layout: post
title: "Closures na linguagem Ruby"
date: 2012-11-21 12:05
comments: true
author: Julian Gutierrez
categories:
- ruby
- closures
---

Este artigo tem como objetivo estudar o funcionamento de closures na linguagem Ruby. O conceito de closure, elaborado na década de 60 define funções que estão associadas ao contexto no qual foram declaradas, conhecendo suas variáveis. Além disso, tais funções podem ser passadas integralmente como argumentos, retornadas de outra função ou atribuídas à variáveis, sem que sejam executadas. Quando uma linguagem permite essa abordagem flexível de funções, diz-se que trata funções como first-class citizens. No Ruby, é possível realizar tais operações através de blocos, Procs e lambdas.
<!-- more -->
Blocos:

Qualquer método em Ruby pode receber um bloco em sua chamada. Um bloco pode ser declarado envolvendo um trecho de código com chaves ou com um bloco do..end. Esse techo de código poderá executado através da chamada yield, no método que o bloco fora passado.

{% gist 4124963 %}

Um bloco pode ser utilizado para implementar uma closure? Vejamos esse exemplo:

{% gist 4125525 %}

Note que o valor de context dentro do bloco é o que foi definido fora do método, independente se o método redefiniu a variável. Com isso verifica-se a característica do bloco de trazer consigo todo o ambiente (variáveis) dentro do qual foi declarado, servindo como uma jaula dentro do ambiente dentro do qual foi executado.

Procs:

Entretanto, isso não define rigorosamente uma closure, porque o bloco define apenas uma função anônima, sendo que ainda não é possível guardar sua definição numa variável por exemplo. É para isso que o Ruby define o objeto Proc, que permite ao desenvolvedor de fato trabalhar as funções como first class citizens. A execução de um objeto Proc pode ser disparada através do método call.

{% gist 4125675 %}

Neste exemplo, é mais evidente que my_proc não poderia assumir o valor de context que foi definido dentro do método, pois trata-se apenas de um objeto que desconhece o contexto em que está inserido. No entanto, por se tratar de uma closure, a variável context foi referenciada no momento da declaração de my_proc, estando sempre visível dentro do Proc.

É preciso enfatizar que apesar de serem facilmente confundidos um bloco não é um Proc. Um Proc é um objeto (por isso é escrito com letra maiúscula), e um bloco, à despeito da máxima que tudo é objeto no Ruby, não é!

Evidentemente é possível passar variáveis para um bloco ou um Proc no momento de sua execução. Essas variáveis no entanto devem ser definidas na declaração do bloco ou Proc.

{% gist 4125807 %}

{% gist 4125821 %}

Note que a variável context é sobrescrita. Você poderá passar quantas variáveis quiser e nem o bloco nem o Proc irão verificar se o número de variáveis passadas em sua chamada correspondem ao que foi definido em suas declarações.

{% gist 4125954 %}

Lambdas:

O código do Proc é executado normalmente. Caso você queira garantir que o número de variáveis passadas seja igual ao que foi definido, existe um Proc que executa essa verificação e lança uma exceção de número de argumento caso seja encontrada a disparidade. Essa Proc pode ser criado através da chamada lambda.

{% gist 4126021 %}

Uma outra característica do lambda é o funcionamento do retorno. Uma chamada return dentro de um lambda irá retornar para o contexto dentro do qual foi executado e passar para a próxima instrução. Isso não é verdade para objetos criados com Proc.new, nesse caso o retorno fica associado ao contexto em que foi declarado, retornando o método imediatamente.

{% gist 4126279 %}

Esse artigo buscou introduzir o conceito de closure, utilizando blocos, Procs e lambdas. Para maiores detalhes sobre a utilização específica desses recursos recomenda-se a leitura de outros diversos artigos disponíveis na web.