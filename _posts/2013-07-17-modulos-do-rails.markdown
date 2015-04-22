---
layout: post
title: Do que o Rails é feito?
date: 2013-07-17 11:41
comments: true
categories: rails
---

Nesse artigo iremos explorar como o Rails funciona por dentro, explicando do que ele é feito e como ele está organizado. Vamos ver também os motivos que levaram o [Rails Core Team](http://rubyonrails.org/core), a equipe responsável por guiar o seu desenvolvimento, a estruturá-lo da forma como ele está hoje.<!-- more -->

### Os blocos fundamentais ###

O Rails é um framework modular, ou seja, toda a sua estrutura interna é dividida em módulos distintos, cada um com a sua própria responsabilidade. [Atualmente](https://github.com/rails/rails/tree/v4.0.0), os módulos que compõem o Rails são:

* **ActionMailer**
* **ActionPack**
* **ActionModel**
* **ActiveRecord**
* **ActiveSupport**

Todos esses módulos tem um ponto em comum: Eles organizam a estrutura interna do Rails. Ao abrir o [código atual do Rails](https://gihub.com/rails/rails/tree/v4.0.0), vemos que cada um dos módulos possui seu próprio diretório, o que facilita tanto para dar manuntenção quanto para entender o funcionamento do framework.

### ActiveRecord ###

O ActiveRecord é um ORM (object-relational mapping), ou seja, um mapeador entre objetos e registros de uma tabela, onde cada classe de modelo possui uma tabela correspondente a ela no banco de dados. Cada objeto sabe como se persistir e após a persistência de um objeto, um novo registro é adicionado na tabela referente a sua classe. Através de métodos de consulta, a classe consegue consultar um ou vários registros de uma tabela e retorná-los como objetos.

O ActiveRecord é um padrão que foi catologado pelo Martin Folwer em seu livro [Patterns of Enterprise Application Architeture](http://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420/ref=sr_1_1?ie=UTF8&qid=1373737815&sr=8-1&keywords=patterns+for+enterprise)

O ActiveRecord utilizado pelo Rails é uma implementação desse padrão. Dentro do Rails, o ActiveRecord define a camada de persistência da aplicação sem precisar utilizar praticamente nenhuma configuração. O ActiveRecord oferece uma classe base que quando é extendida, define o mapeamento entre a classe e a tabela correspondente a ela no banco de dados.

O fato do ActiveRecord não precisar de quase nenhuma configuração é graças ao conceito de CoC (convention over configuration), que é usado diversas vezes em todo o Rails. Enquanto outros frameworks objeto-relacional usam arquivos de configuração para fazer todo o mapeamento, o ActiveRecord o faz através de convenções de nomenclatura. Por exemplo, enquanto outros frameworks esperam que seja definido em um arquivo XML que a classe "User" está mapeada para a tabela "users", o Rails apenas assume que isso vai acontecer, ou seja, ele já espera que exista uma tabela "users" para uma classe "User", o que torna a configuração de mapeamento desnecessária.

Uma curiosidade é que o criador do Rails comecou a implementar o ActiveRecord incialmente em PHP, mas as partes mais dinâmicas da interface exigiam muita metaprogaramação, o que fez a sua atenção se voltar para o Ruby, já que a linguagem lida com metaprogramação de modo muito simples.

### ActionPack ###

O ActionPack é o módulo responsável pelo controle das requisições feitas para a aplicação. Ele é que torna possivel a criação de controllers que definem actions, a criação de rotas que mapeiam uma action a uma URL, além de ser o responsável por gerar a resposta da requisição, renderizando views de diversos formatos diferentes.

O ActionPack por sua vez é formado por diferentes módulos, sendo os três principais:

* **ActionDispatch**, que lida com o parse do do conteúdo de request, com os cookies, com a session, entre outros.

* **ActionController**, que fornece o controller básico do qual todos os outros controllers herdam, viabilizando o uso de actions, filters, entre outros.

* **ActionView**, que trabalha com a renderização das views através de templates com o formato ERB (embedded ruby)

### ActiveModel   ###

O prinicipal motivo da criação do ActiveModel foi gerar uma interface entre o ActiveRecord e o ActionPack, eliminando o acoplamento do Rails com a camada de persistência escolhida para a aplicação, facilitando assim a integração dela com outros tipos de bancos de dados que por padrão não são suportados pelo ActiveRecord.

Outro bom motivo que levou o ActiveModel a ser criado foi para tirar do AtiveRecord comportamentos que não estavam relacionados a persistência de um objeto, tais como validação, mensagens de erro, entre outros. O ActiveModel resolveu esse problema extraindo do ActiveRecord tudo que era comum aos modelos de uma aplicação, facilitando a criação de objetos que embora não fossem persistidos, usariam os comportamentos que antes eram exclusivos dos objetos ActiveRecord.

Segue abaixo uma lista dos módulos que são responsabilidade do ActiveModel. Embora não seja o meu objetivo entrar em detalhes sobre eles, o nome de cada um já dá uma idéia do que fazem:

- **ActiveModel::CallBacks**
- **ActiveModel::Dirty**
- **ActiveModel::Errors**
- **ActiveModel::Naming**
- **ActiveModel::Serialization**
- **ActiveModel::Validations**

### ActiveSupport ###

O ActiveSupport é o módulo que fornece extensões para as classes mais utilizadas no dia-a-dia de um desenvolvedor Rails, como String, Hash, Array, Time, etc... Assim como os outros módulos citados nesse post, o ActiveSupport pode ser utilizado também fora de um projeto Rails, para isso basta incluir nas dependências do projeto a gem activesupport.

Segue abaixo a lista das extensões do ActiveSupport para as principais classes do ruby. O nome dos métodos junto com a classe que pertencem já dá uma idéia do que cada um faz, e como o meu objetivo não é entrar nos detalhes de funcionamento deles, a lista possui alguns lins para a documentação de alguns dos métodos mais interessantes:

1) Extensões a todos os objetos

- Object#blank?
- Object#present?
- [Object#presence](http://guides.rubyonrails.org/active_support_core_extensions.html#presence)
- Object#duplicable?
- [Object#try](http://guides.rubyonrails.org/active_support_core_extensions.html#try)
- Object#singleton_class
- Object#class_eval
- Object#acts_like?
- Object#to_param
- Object#to_query
- Object#with_options
- Object#instance_values
- [Object#in?](http://guides.rubyonrails.org/active_support_core_extensions.html#in-questionmark)

2) Extensões de Module

- Module.alias_attribute
- [Module#attr_accessor_with_default](http://guides.rubyonrails.org/active_support_core_extensions.html#attr_accessor_with_default)
- Module#attr_internal
- Module#mattr_accessor
- [Module#parent](http://guides.rubyonrails.org/active_support_core_extensions.html#extensions-to-module-parents)
- Module#parent_name
- Module#parents
- [Module#local_constants](http://guides.rubyonrails.org/active_support_core_extensions.html#constants)
- Module#qualified_const_defined?
- Module#qualified_const_get
- Module#qualified_const_set
- Module#synchronize
- Module#reachable
- Module#anonymous?
- [Module#delegate](http://guides.rubyonrails.org/active_support_core_extensions.html#method-delegation)
- Module#redefine_method

3) Extensões de Class

- [Class#class_attribute](http://guides.rubyonrails.org/active_support_core_extensions.html#class-attributes)
- Class#cattr_accessor
- Class#subclasses
- Class#descendants

4) Extensões de String

- String#squish
- [String#truncate](http://guides.rubyonrails.org/active_support_core_extensions.html#truncate)
- [String#inquiry](http://guides.rubyonrails.org/active_support_core_extensions.html#inquiry)
- String#starts_with?
- String#ends_with?
- String#strip_heredoc

5) Extensões de Array

- Array.wrap
- Array#to
- Array#from
- Array#prepend
- Array#append
- Array#to_sentence
- [Array#to_sentence](http://guides.rubyonrails.org/active_support_core_extensions.html#extensions-to-array-conversions)
- Array#to_formatted_s
- Array#to_xml
- Array#in_groups_of
- Array#in_groups
- Array#split

6) Extensões de Hash

- Hash#to_xml
- Hash#merge
- [Array#reverse_merge](http://guides.rubyonrails.org/active_support_core_extensions.html#merging)
- Hash#deep_merge
- Hash#diff
- Hash#stringify_keys
- Hash#symbolize_keys
- Hash#assert_valid_keys
- [Hash#slice](http://guides.rubyonrails.org/active_support_core_extensions.html#slicing)
- [Hash#extract](http://guides.rubyonrails.org/active_support_core_extensions.html#extracting)

### ActionMailer  ###

O ActionMailer é o módulo responsável pelo serviço de email, possibilitando tanto o envio quanto o recebimento de email da aplicação.

O ActionMailer foi construído ao redor da [mail gem](https://github.com/mikel/mail), e funciona de modo semalhante ao ActionPack: Você define uma classe mailer que tem a mesma função de um controller, com actions e views que correspondem aos templates de email que se deseja definir.

O ActionMailer foi construído ao redor da [mail gem](https://github.com/mikel/mail), e funciona de modo semalhante ao ActionPack: Você define uma classe mailer que tem a mesma função de um controller, com actions e views que correspondem aos templates de email que se deseja definir.

### Conclusão ###

A arquitetura modular do Rails dá ao desenvolvedor o poder de escolha sobre quais partes do Rails a aplicação vai utilizar. Um caso real aonde essa arquitetura é bem aproveitada é uma a aplicação Rails que usa um banco de dados não relacional, como o MongoDB. Nesse caso, a camada de persistência padrão do Rails é trocada, o ActiveRecord é substituido por outra mapeador que saiba lidar com o MongoDB, como o [Mongoid](http://mongoid.org) por exemplo.

Uma outra consequência dessa arquitetura é poder utilizar os módulos do Rails em um projeto ruby qualquer, já que cada um desses módulos estão empacotados dentro de sua própria gem. É possível até mesmo utilizá-los para criar um novo framework!
