---
layout: post
title: "Expressões Regulares"
date: 2012-10-12 22:29
comments: true
author: luis.vasconcellos
read_time: 11 min
categories:
- ruby
- regex
- expressoes regulares
---

Expressões regulares são utilizadas para facilitar a manipulação de texto dentro de uma aplicacão. Os exemplos a seguir mostram de maneira bem simples o que as expressões regulares podem fazer:

- A expressão regular ```DTM``` pega a primeira ocorrência de DTM em uma string.
- A expressão regular ```21/03/2012``` pega a primeira ocorrência dessa data em uma string.

<!-- more -->

Usando expressões regulares também podemos utilizar outros caracteres além de letras e números, como alguns sinais de pontuação que possuem signficados especiais em expressões regulares:
- O ponto . pega qualquer caracter, independente de ser numero, letra ou sinal de pontuação. Da mesma forma, a expressão regular .. pega os primeiros dois caracteres de uma string. Exemplos:

{% gist 3868559 %}

- A contra-barra é utilizada para escapar caracteres especiais, por exemplo: a expressão regular \$ 0\.99 pega exatamente a string "$ 0.99" escapando o ponto e dollar sign, que se fossem utilizados sem a contra-barra teriam um signficado diferente em expressões regulares. Exemplos:

{% gist 3868365 %}

O que torna as expressões regulares um recurso tão poderoso para manipulação de texto é a possibilidade de combinar diferentes tipos de expressões para criar padrões mais complexos. Por exemplo:

- A expressão regular A. vai pegar a string de dois caracteres que comecem com A:

{% gist 3868401 %}

- A expressão regular ..M vai pegar qualquer string de 3 caracters que terminem com M:

{% gist 3868411 %}

Outro recurso das expressões regulares são os sets. Os sets são uteis quando se espera que um mesmo caracter de uma string assuma valores diferentes. Por exemplo:

- A expressão regular [123] vai pegar a primeira ocorrência de um caracter de uma string pode ser tanto "1", "2" ou "3".

- A expressão regular [Mm]r[.] John vai pegar a primeira ocorrência em uma string de "Mr. John", "mr. John", "Mr John" ou "mr John".

A desvantagem de usar sets é que sozinhos eles podem ser tornar trabalhosos no que se refere a quantidade de caracteres que se tem que digitar para pegar strings mais complexas.

## Exemplo Prático ##

Vamos supor que se queira pegar uma string que represente uma placa de carro qualquer no formato "AAA-9999". Ou seja, qualquer string que possua 3 letras maiusculas seguida de 4 numeros e separados por um hifen. Usando-se apenas os sets, a expressão regular seria essa:

`[QWERTYUIOPASDFGHJKLZXCVBNM][QWERTYUIOPASDFGHJKLZXCVBNM][QWERTYUIOPASDFGHJKLZXCVBNM][-][1234567890][1234567890][1234567890][1234567890]`

Exemplos:

{% gist 3868435 %}

Para casos como esse os ranges são utilizados. Com um range, voce pode definir o inicio e o final de um sequencia de caracteres, separados por um hifen. Utilizando-se ranges, a expressão regular acima fica dessa forma:

`[A-Z][A-Z][A-Z][-][0-9][0-9][0-9][0-9]`

Exemplos:

{% gist 3868459 %}

A nossa expressão regular pode ainda ficar melhor. Como alguns sets são muito comuns de serem utilizados, existem atalhos para escreve-los:

- \d pega qualquer numero. Ou seja, a expressao regular \d\d\d\d é equivalante a `[0-9][0-9][0-9][0-9]`

- \w pega qualquer letra, numero ou o underscore. Ou seja, a expressao regular \w\w\w e equivalente a `[A-Za-z0-9_][A-Za-z0-9_][A-Za-z0-9_]`

- \s pega qualquer caractere de espacamento, que incluem o espaco \r, o tab \t e o newline \n

Utilizando o atalho de digito mostrado acima, a nossa expressão regular ficaria dessa forma:

`[A-Z][A-Z][A-Z][-]\d\d\d\d`

Exemplos:

{% gist 3868474 %}

A nossa expressão regular ja melhorou muito desde a sua primeira forma, mas ainda pode ficar melhor. Em expressões regulares existe o que é chamado de operador de intervalo, que define o numero de repetições da menor expressão regular possivel que o antecede, por exemplo:

- A expressão regular [a-z]{3} pega as primeiras 3 letras miniusculas de uma string: "dtm", "def", "abe", etc...

- A expressão regular [0-9]{3,5} pega no minimo os primeiros 3 numeros seguidos e no maximo 5 primeiros numeros seguidos: "153", "2245", "15156", etc...

Utilizando o que foi dito acima, chegamos finalmente na ultima forma da nossa expressão regular:

`[A-Z]{3}[-]\d{4}`

Exemplos:

{% gist 3868484 %}


