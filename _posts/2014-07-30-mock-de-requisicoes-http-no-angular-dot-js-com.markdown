---
layout: post
title: "Mock de requisições HTTP no Angular.js"
date: 2014-07-30 01:18
author: carlos.figueiredo
comments: true
read_time: 4 min
categories:
- Angular.js
- UnitTest
- javascript
---

Angular.js se tornou um framework MVC javascript muito popular. E existem muitos artigos sobre suas facilidades, advindas de seus controllers, suas diretivas, services e factories. Porém pouco se fala em relação a testes unitários para aplicações Angular.js, e isso que pretendo mudar com este artigo. Este artigo vai tratar basicamente de como testar unitariamente seu app feito em Angular.js.

<!-- more -->

## Preparação de ambiente de teste

Para começar, é preciso primeiro montar seu ambiente de testes.

Existem várias formas de se montar o ambiente de teste, uma das mais simples, é fazer o clone do projeto [angular-seed](https://github.com/angular/angular-seed), que já vem com um ambiente pré-configurado, porém ainda possível customizar de acordo com sua necessidade.


Na prática, deste projeto, nós precisamos somente dos seguintes arquivos:

    app
      js  //onde ficarão o app javascript
    test
      unit  //onde ficarão nossos testes
      karma.conf.js
    package.json
    bower.json
    .bowerrc

Para preparar o ambiente com este projeto só é preciso ter o [node.js](http://nodejs.org) instalado. Todos os requisitos restantes serão instalados quando após executar o comando `npm install`

Agora que estamos com o ambiente pronto, podemos começar nossos testes.

Neste ambiente, a sintaxe de testes é a do nosso amado [Jasmine](http://jasmine.org) então não difere muito do que já estamos habituados a fazer, e caso não esteja, vale a pena consultar a documentação do jasmine para aprender como testar o javascript.

Para testar uma aplicação Angular.js precisamos instanciar a aplicação que iremos testar passando module("myApp") para o beforeEach do describe.


```javascript
describe("MyAngularTutorial", function() {
  beforeEach(module("tutorialApp"));

  describe("ContactsCtrl", function() {

    ...

  })

})
```

E para nosso primeiro teste, vamos testar se quando dispararmos a funcao getContacts() ela resgata uma lista de contatos e armazena numa variavel do scope. Esta variável só deverá ser definida caso consigamos obter com sucesso um json com a list de contatos do endereço /contacts.


## Mock de requisicoes HTTP com $httpBackend

Para fazer as requisições, a própria documentação do Angular, mostra como usar o $http no controller, porém para os testes, não poderemos usar este módulo, pois não queremos fazer requisições reais. Para fazer o mock dessas requisições, precisamos usar o módulo $httpBackend.

Este módulo implementa algumas funções para simular requisições GET, POST, PUT, JSONP de duas formas:

### Função ```$httpBackend.when("METHOD", path).respond(data)```

Este é um método que serve para configurar uma resposta do backend quando ocorrer determinada requisição. As requisições não precisam ser feitas, mas é preciso configurar o retorno esperado.

A mesma definição com o método when pode atender a mesma requisição várias vezes, e não depende da ordem de execução.


No nosso exemplo, usamos para determinar que quando for feito uma requisição GET para /contacts, esperamos que seja retornado do servidor, um array de contatos.


```javascript
describe("MyAngularTutorial", function() {
  beforeEach(module("tutorialApp"));

  describe("ContactsCtrl", function() {
    var ctrl, scope, httpBackend, contacts;

    beforeEach(inject(function($httpBackend, $rootScope, $controller) {

      scope = $rootScope.$new();
      ctrl = $controller('ContactsCtrl', {$scope: scope});

      httpBackend = $httpBackend;
      httpBackend.when("GET", '/contacts').respond([{"id": 1}, {"id": 2}])  //define behavior

    }));

    //describe("getContacts()")
```


### Função ```$httpBackend.expect("METHOD", path)``` - (expectativa de requisição)

Esta é um método que é usado para estabelecer que é obrigatório que determinada requisição seja feita. Caso a requisição não seja feita, um erro será lançado.
Este método também permite encadear um .respond(data) para se especificar um retorno, porém não é necessário.
Caso um existe uma expectativa e uma definição que conflitem, a expectativa terá prioridade.

No nosso exemplo, usamos a expectativa para determinar que esperamos que seja feita uma requisição para /contacts e que ocorra um erro caso ela não ocorra.

```javascript
    describe("getContacts()", function() {

      it('should get place a list of contacts on scope.contacts', function() {
        scope.getContacts();

        httpBackend.expectGET('/contacts');
        expect(scope.contacts).toBeDefined();
      });
    });
```

## Implementando as funcionalidades

Até agora só descrevi como estão os testes. Porém mesmo após terminarmos o código da nossa aplicação, os testes vão falhar, com o erro **Expected undefined to be defined.**

```javascript
var tutorialApp = angular.module('tutorialApp', []);

tutorialApp.controller('ContactsCtrl', function ($scope, $http) {

  $scope.getContacts = function() {
    return $http.get('/contacts').success(function (data) {
      $scope.contacts = data;
    })
  }
});
```

Pois apesar de implementarmos o acesso ao path '/contacts', este código ainda não está sendo executado no momento dos testes. Logo não está definindo a variável $scope.contacts.

Isso porque, as expectativas e definiçoes de **expect** e **when** são assíncronas, enquanto nosso teste roda síncronamente.

Portanto para forçao a execução de uma requisição no momento desejado, usamos o método $httpBackend.flush()

Quando inserimos este método no nosso teste, ele vai forçar que as requisições que estejam pendentes naquele momento, sejam executadas ou concluídas.

```javascript
      it('should get place a list of contacts on $scope.contacts', function() {
        scope.getContacts(); //A requisição foi iniciada mas esta aguardando ser fechada

        httpBackend.flush()  // Fecha a unica requisicao pendente

        httpBackend.expectGET('/contacts');  //fica pendente mas ninguem verifica

        expect(scope.contacts).toBeDefined();

      });
```

#### Código pronto e testes passam... mas pelos motivos errados!


```bash
INFO [watcher]: Changed file "/Users/cefigueiredo/dev/tutorials-posts-stuff/testing-angular/app/js/app.js".
Chrome 36.0.1985 (Mac OS X 10.9.2): Executed 1 of 1 SUCCESS (0.342 secs / 0.032 secs)
```

## Verificação de pendencias

Eu intencionamente inseri um bug no nosso código.

Pois como eu disse antes, o método **expect** depende da ordem em que está sendo executado. E como chamamos o método getContacts() e fechamos a requisiçao com o $httpBackend.flush() antes de configurarmos a expectativa de requisição, então esta expectativa não vai ser satisfeita, mas ainda não incluímos nenhum mecanismo que valide se existe alguma expectativa não satisfeita ou não.

Como expects são assíncronos, é preciso que algum dispositivo que avise que ele já deveria ter sido encerrado caso fosse satisfeito. Da mesma forma, deveríamos haver um dispositivo para verificar se existe alguma requisição que espera um flush().

Existem 2 métodos responsáveis por essas verificações, e que devem ser executados no final de cada teste. Os mecanismos responsáveis por isso são:


```javascript
    } //beforeEach

    afterEach(function () {
      httpBackend.verifyNoOutstandingExpectation();
      httpBackend.verifyNoOutstandingRequest();
    })

    describe("getContacts()", function() {
```

O método `$httpBackend.verifyNoOutstandingExpectation()` é o responsável por validar se existe alguma expectativa que não foi atendida, e por lançar o erro `Unsatisfied requests: METHOD path`

Já o método `$httpBackend.verifyNoOutstandingRequest()` é o responsável por verificar se existe algum requisição onde é necessário encerrá-la com o método flush().

Após inserir este código, verificamos que ainda existe um erro que estava mascarado pela assincronicidade do expect. O erro é: `Error: Unsatisfied requests: GET /contacts`


No nosso caso a solução é simples. Basta fazermos com que a expectativa seja configurada antes de finalizarmos a requisição com o flush.


Com isso, podemos dizer que nosso pequeno app apesar de simples, está bem testado, e conseguimos garantir sem funcionamento.
Os outros métodos do módulo $http (POST, PUT, JSONP) possuem um comportamento completamente análogo, e necessitam dos mesmos cuidados.


O exemplo completo pode ser encontrado no meu [Github](https://github.com/cefigueiredo/tutorials-posts-stuff/tree/master/testing-angular)

Referência: [Angular.js API Reference - $httpBackend](https://docs.angularjs.org/api/ngMock/service/$httpBackend)

