---
layout: post
title: "Criando um componente com Backbone.js"
date: 2012-09-11 16:42
comments: true
author: Vicente Mundim
read_time: 26 min
categories:
- Backbone
- Javascript
---

Como vimos no nosso último post, <a href="http://backbonejs.org">Backbone.js</a> nos ajuda a criar <a href="http://en.wikipedia.org/wiki/Rich_Internet_application">interfaces ricas na web</a> usando apenas *JavaScript*. Vamos ver agora um exemplo da criação de um componente de seleção genérico usando **Backbone.js**, junto com o adapter <a href="https://github.com/jeromegn/Backbone.localStorage">Backbone.localStorage</a> para fazer a persistência de dados no próprio cliente.

<!-- more -->

Essa aplicação poderia ter sido implementada usando apenas HTML e JavaScript, mas na maioria dos casos o que queremos é saber como integrar um componente **Backbone.js** com uma aplicação web comum, como **Rails** ou **Django**. Este tutorial vai descrever algumas técnicas que utilizamos quando queremos criar componentes desse tipo, integrados às nossas aplicações.

## O componente ##

Neste exemplo, vamos implementar uma interface para que, dado um conjunto de filmes, um usuário possa selecionar seus filmes favoritos, manter essa lista persistida mesmo ao recarregar a página. A idéia não é criar uma aplicação completa, com persistência em banco de dados, autenticação, etc, mas mostrar como isso poderia ser feito usando **Backbone.js** e uma pequena camada de **Rails**.

## Setup ##

Para quem prefere devorar o código, basta clonar o repositório <a href="https://github.com/vicentemundim/multi-select-backbone">https://github.com/vicentemundim/multi-select-backbone</a> e rodar os passos básicos para qualquer aplicação rails: criar um gemset com o <a href="http://rvm.io">RVM</a>, rodar bundle install e por fim os testes com rake jasmine:ci. Por fim, inicie o servidor rails e aponte seu browser para <a href="http://localhost:3000">http://localhost:3000</a>, você deverá ver a aplicação rodando, e poderá adicionar filmes à lista de favoritos, e esta será mantida mesmo após recarregar a página.


## Entendendo o código ##

O componente que iremos criar precisará ser alimentado com uma lista de filmes. Além disso precisamos que uma página seja renderizada para que o componente possa fazer sua mágica. Isso é bem comum em aplicações que usam o **Backbone.js**, mas como passar esses dados para o componente durante a renderização da página?

Poderíamos simplesmente adicionar uma tag `<script>` e <a href="http://backbonejs.org/#FAQ-bootstrap">carregar</a> esses dados nas collections e models do Backbone, mas isso iria sujar o nosso código HTML com um pedaço de código JavaScript, e por mais que funcione não queremos misturar as coisas. O que vamos fazer é implementar uma solução <a href="http://en.wikipedia.org/wiki/Unobtrusive_JavaScript">não intrusiva</a>.

Basicamente o que iremos fazer é renderizar, na própria *view* do **Rails**, no elemento `<div>` que engloba a lista de filmes disponíveis, a lista de filmes num formato **JSON** no atributo `data-available`. Como estamos usando o *doctype* **HTML5**, podemos fazer uso dos atributos `data-xxx` para passar dados entre a view e nosso código JavaScript. Depois, quando o nosso componente estiver sendo inicializado, ele irá carregar a lista a partir desse atributo. O **jQuery** tem uma funcionalidade bem legal quando usamos atributos `data-xxx`, que é já fazer um parse de dados no formato **JSON** dentro desses atributos. Isso deixa essa parte do código bem limpa, algo como:

{% gist 3679604 available-data.js %}

De fato é isso que fazemos, primeiro temos um controller com o seguinte código:

{% gist 3679604 home_controller.js %}

Criamos uma lista de “pseudo-models” representando nossos filmes. Poderíamos ter carregado esses dados de um banco de dados, de uma API remota, enfim, basta termos uma lista de objetos.

Já a nossa view, possui o seguinte código:

{% gist 3679604 index.html.erb %}

Criamos alguns elementos para compor a página, e a estrutura necessária para nosso componente: uma tag `div.multi-select` com duas outras tags `div.available` e `div.selected`. A tag `div.available` irá exibir os filmes disponíveis dentro do `ul`, e irá conter a lista de filmes disponíveis no atributo `data-available`, além de possuir um template, que será apenas uma tag `<script>` com um type text/html. Essa técnica vem se tornando comum, o que acontece é que essa tag `<script>` não é interpretada pelo browser, mas podemos pegar seu conteúdo via JavaScript e utilizá-lo como template. A tag `div.selected` também possui um `ul` e um template.

Nosso componente será ativado dessa forma:

{% gist 3679604 application.js %}

Essa é outra técnica interessante que utilizamos quando temos uma série de componentes numa aplicação e queremos ativá-los apenas em páginas onde sejam necessários. Apenas habilitamos o componente em página onde houver um elemento com o atributo `data-enable-multi-select`. Passamos então esse elemento para o componente, junto com novas instâncias das *collections* Movies e FavoriteMovies. Ambas essas *collections* irão ser carregadas pelo componente com os models do tipo Movie. Definimos essas collections assim:

{% gist 3679604 models.js %}

Garantimos que ambas as coleções irão ser ordenadas pelo título implementando o método `comparator`. Esse método deve retornar o valor que será usado para comparação entre os modelos. Retornamos o `title`, assim ambas as *collections* serão ordenada alfabeticamente pelo título do filme. Os filmes favoritos deverão ser persistidos, para isso adicionamos um `localStorage` à *collection* FavoriteMovies, e garantimos que sempre que um modelo for adicionado à *collection* ele será persistido no `localStorage`, e sempre que for removido, será removido também do `localStorage`. Um ponto que deve ser considerado aqui é que não há persistência de *collections*, mas sim de models. Uma *Collection* no **Backbone.js** apenas nos auxilia a manipular uma lista de modelos.

Por fim, definimos a *view* `MultiSelect`, que irá gerenciar toda a lógica de renderização, assim como irá tratar os eventos que vierem a acontecer tanto na interface do usuário (UI) quanto nos *models* e *collections*. Para simplificar essa *view* iremos criar outras duas *views* `AvailableView` e `SelectedView`. O **Backbone.js** não nos força a termos apenas 1 *view*, podemos compor uma *view* com diversas outras, ou simplesmente fazer com que uma *view* fale com outra através de eventos, etc, somos livres para criarmos e relacionarmos essas *views* da melhor forma possível.

Vamos começar pela *view* `MultiSelect`:

{% gist 3679604 multi-select.js %}

A principal função dessa *view* é gerenciar a troca de mensagens entre as listas e suas *views* correspondentes. Escutamos, por exemplo, o evento `reset` da *collection* de filmes selecionados, para remover os itens selecionados da lista de filmes disponíveis. Note que o método `remove` da *Backbone.Collection* pode receber tanto um *model* como um array de *models*, mas não uma outra *Backbone.Collection*, por isso chamamos o método `toArray`. Além desse evento, criamos as duas *views*, passando a *collection* apropriada a cada uma, e o container apropriado. É importante que, em componentes genéricos, usemos sempre o método `$(selector)` no escopo de nosso elemento container, por isso sempre usamos o método `Backbone.View#$(selector)` e não usamos direto o **jQuery**. Escutamos a dois eventos personalizados dessas *views* `multi-select:add` e `multi-select:remove, e então movemos o *model* de uma lista para outra.

Por fim temos as outras *views*:

{% gist 3679604 available-selected.js %}

Essas *views* são bem parecidas, ambas irão carregar o template, e renderizar o conteúdo quando um *model* for adicionado à *collection* ou esta for reiniciada. A renderização de ambas também é similar, ambas irão renderizar o template dentro da tag `ul` dentro do container apropriado, passando um objeto com uma chave *collection* contendo a representação JSON dos *models* da *collection*. A única diferença entre elas é que a *view* `AvailableView` irá inicializar os dados através do *parse* do atributo `data-available` no seu elemento container, enquanto que a *view* `SelectedView` irá chamar o método `fetch` para carregar os dados da *collection*, no nosso caso, do `localStorage`.

Por fim, as *views* são configuradas para escutar eventos de clique nos botões de adicionar ou remover, disparando o evento personalizado `multi-select:add` ou `multi-select:remove`. Esses eventos, como vimos anteriormente são escutados pela *view* `MultiSelect` que irá então mover os modelos de uma lista para outra. Ao fazer isso, os modelos irão disparar eventos de `add` e `remove` para as *views*, que irão renderizar suas *collections* novamente, atualizando a tela. Embora a *view* `MultiSelect` conheça e mantenha uma referência direta para as outras duas *views*, estas se comunicam com aquela apenas através de eventos, ou seja, conseguimos desacoplar esses objetos.

## Melhorando o código ##

Como vimos, ambas as *views* tem um comportamento semelhante, com pequenas variações. Podemos usar o conceito de herança para melhorar o código e reduzir a redundância. Para isso criaremos uma classe base `BaseContainerView` que irá implementar o método render, assim como carregar o template e escutar aos eventos da *collection*:

{% gist 3679604 base-container-view.js %}

E reescrevemos as views assim:

{% gist 3679604 refactor-available-selected.js %}

Note que agora herdamos de `BaseContainerView` ao invés de `Backbone.View`, simples assim.

## Conclusão ##

Com este exemplo mostramos como criar um componente genérico usando **Rails** e **Backbone.js**. Usamos um `localStorage` para salvar os filmes favoritos, mas poderíamos facilmente ter criado uma API com **Rails** e feito a intergração. Do ponto de vista do código do componente, apenas mudaríamos a *collection* `FavoriteMovies` para não utilizar mais o `localStorage`, além de especificar a *URL* do serviço. Todo o resto permaneceria inalterado. Conseguimos, através de uma <a href="http://en.wikipedia.org/wiki/Event-driven_programming">arquitetura orientada a eventos</a> diminuir o acoplamento entre os componentes e com isso torná-los mais genéricos e reutilizáveis.

Há muito mais coisas que o **Backbone.js** pode fazer, como o uso da <a href="http://backbonejs.org/#History">History API</a> através de seu <a href="http://backbonejs.org/#Router">router</a>. Poderíamos ainda explorar outras formas de trabalhar com models e collections. Isso ficará para um próximo post!
