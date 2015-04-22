---
layout: post
title: "Dominando o Rails Console"
date: 2013-09-06 14:19
comments: true
author: Luis Vasconcellos
categories:
- Rails
- Ruby
- Console
---

O rails console pode ser um poderoso aliado do desenvolvimento, mas para se extrair o máximo dele é necessário ir além do básico e conhecer tudo que ele pode oferecer. Nesse artigo vou citar e explicar o funcionamento dos seus comandos mais úteis e menos conhecidos. <!-- more -->

### Buscando um método com grep ###

A busca por métodos é o cenário ideal para usar o grep no rails console. Por exemplo, enquanto estava escrevendo meu artigo anterior eu aprendi um método de String chamado inquiry. Porém quando fui usá-lo eu só me lembrava parcialmente do seu nome. Ao invés de abrir o browser e procurar na internet, eu apenas tive que abrir o rails console:

```
>> "".methods.grep /inq/
=> [:inquiry]
```

Pronto! Sabendo apenas uma parte do nome do método, consegui descobrir seu nome exato. Observe que o grep não é um método exclusivo do rails console. Na verdade [ele está definido na classe Enumerable](http://ruby-doc.org/core-2.0/Enumerable.html#method-i-grep) do ruby. No exemplo eu chamei ele em cima de methods, que retorna uma array de symbols que contém todos os métodos de um dado objeto.

### Capturando o resultado da execução anterior ###

No rails console, o resultado da execução anterior pode ser acessado usando um underscore. Isso é muito útil para recuperar uma referência do que foi retornado anteriormente.

```
>> User.all.map(&:name)
=> ["Johny HT", "Luis", "Petra", "Nemcova"]
>> names = _
=> ["Johny HT", "Luis", "Petra", "Nemcova"]
>> names
=> ["Johny HT", "Luis", "Petra", "Nemcova"]
```

### Descobrindo a localização de um método ###

Essa é uma das funcionalidades mais legais do ruby. O método source_location da classe Object retorna o caminho completo do arquivo e o numero da linha onde um determinado método foi definido. Muito útil para explorar o uma gem que você não conhece.

```
>> helper.method(:simple_form_for).source_location
=> ["/Users/myuser/.rvm/gems/ruby-2.0.0-p0@myproject/gems/simple_form-2.1.0/lib/simple_form/action_view_extensions/form_helper.rb", 20]
```

No exemplo eu perguntei o source_location do método simple_form_for da gem simpleform. Com a localização dele, eu consigo de um modo rápido abrir a gem do simple_form e explorar a implementação desse método.

### Revertando as alterações feitas no banco ###

É possível entrar no rails console em um modo sandbox. Ao utilizar esse modo, todas as alterações feitas no banco de dados são automaticamente revertidas assim que você sair do rails console. Útil para testar a persistência e as validações dos modelos da aplicação sem alterar o que está no banco de dados.

```
rails console --sandbox
```

### Recarregando automaticamente o console ###

O rails console sobe todos os modelos em memória assim que ele é executado. Ou seja, se após subir o rails console você alterar algum modelo, terá que sair dele e entrar novamente, certo? Errado! Basta usar o reload! para recarregar os modelos em memória novamente.

```
reload!
```