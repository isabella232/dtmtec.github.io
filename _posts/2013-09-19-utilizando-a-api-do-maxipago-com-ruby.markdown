---
layout: post
title: "Utilizando a API do Maxipago no Rails"
date: 2013-09-19 15:36
author: joao.mello
comments: true
read_time: 4 min
categories:
- Rails
- Maxipago
- Pagamento Recorrente
---

Maxipago é um gateway de pagamentos com suporte à pagamentos via cartões de crédito, boleto bancário, além de pagamentos recorrentes. Para comunição de dados do Maxipago com sua aplicação são utilizados 3 tipos de requisição e para cada uma delas existe uma API específica, são elas:

<!-- more -->

* Requisição de Cadastro: Efetua operações cadastrais, como salvar um cartão de créditos na base de dados do Maxipago.

* Requisição de Transação: Efetua as operações de pagamento tanto do cartão de crédito e boleto bancário.

* Requisição de consulta: Busca pedidos e transações na base de dados do Maxipago.

## Instalação:

Requisições feitas à API do Maxipago são feitas atraves de XML, porém utilizamos uma gem para este trabalho.

Adicione ao Gemfile da sua aplicação a seguinte linha:

```
gem "maxipago", :git => "git://github.com/bonera/maxipago.git"
```

Instalar o inicializador da GEM:

```
rails generate maxipago:install
```

Esta gem foi baseada no manual da 'API V1.3' e da API de transação 'V3.1.1.15'. Veja o manual com o histórico de revisões e outras informações: (http://www.maxipago.com/docs/maxiPago_API_Ultima.pdf)

Para realizar uma ação usando a bibioteca maxipago é preciso incluir as credenciais do estabelecimento. Qualquer requisição a API do Maxipago precisa ser autenticada.

O local para incluir essas credenciais bem como dados de teste ficam localizado em:

```
config/initializers/maxipago_config.rb
```

Aqui segue um exemplo do seu conteudo:

```ruby
# API Request Config
MP_APIKEY=""
MP_ID=""
MP_URL_TRANSACTION = "https://api.maxipago.net/UniversalAPI/postXML"
MP_URL_API = "https://api.maxipago.net/UniversalAPI/postAPI"
MP_URL_RAPI =  "https://api.maxipago.net/ReportsAPI/servlet/ReportsAPI"
MP_APIVERSION="3.1.1.15"

# Test Card Example
VISA_CARD = "4111111111111111"
EXP_YEAR = "2020"
CVV = "999"
ECOMMIND = "eci"

# Test Urls
URL_TEST_TRANSACTION = "https//testapi.maxipago.net/UniversalAPI/postXML"
URL_TEST_API = "https://testapi.maxipago.net/UniversalAPI/postAPI"
URL_TEST_RAPI = "https://testapi.maxipago.net/ReportsAPI/servlet/ReportsAPI"
```

## Como usar?

A seguir temos alguns passos comuns à utilização de uma api de pagamento. Instanciando um objeto da classe Client para executar os comandos.

```
client = Maxipago::Client.new
=> #<Maxipago::Client:0x007fb1f3d171d8>
```

### Requisição de cadastro:

Para este exemplo, estamos executando o comando de "add_customer", ou seja, adicionando um cliente 'Foo Bar' na base de dados do Maxipago.

As informações sobre os campos obrigatórios estão no [manual da API](http://www.maxipago.com/docs/maxiPago_API_Ultima.pdf).

```ruby
  api = Maxipago::RequestBuilder::ApiRequest.new(MAX_ID, MAX_APIKEY)

  client.use api

  client.execute(command: "add_consumer", customer_id_ext: "1", firstname: "Foo", lastname: "Bar")
```

Mais tarde pode ser necessário adicionar as informações do cartão de crédito para este cliente.

A seguir adicionamos as informações do cartão de crédito ao cliente de customerId '12345'

```ruby
client.execute( command: "add_card_onfile",
               customer_id: "12345",
               credit_card_number: "4111111111111111",
               expiration_month: "07",
               expiration_year: "2017",
               billing_name: "Foo Bar",
               billing_address1: "Rua Amarildo Quintana, 45",
               billing_address2: "103",
               billing_city: "Rio de Janeiro",
               billing_state: "RJ",
               billing_zip: "39040020",
               billing_country: "BR",
               billing_phone: "2188779900",
               billing_email: "foo.bar@exemplo.com.br")
```

## Requisição de transação:

Agora que temos um cliente salvo no Maxipago com suas respectivas informações de pagamento, faremos uma transação (pagamento) avulsa para ele.

Para isto precisamos instanciar um objeto da classe TransactionRequest e utilizaremos o nosso já conhecido customerId e o token trazido ao adicionar os dados do pagamento.


```ruby
transaction = Maxipago::RequestBuilder::TransactionRequest.new(MP_ID, MP_APIKEY)
client.use(transaction)
client.execute( command: "sale",
                reference_num: '298374239847',
                processor_id: '1',
                customer_id: '12345',
                token: '32jk4h2kj3=',
                charge_total: '45.75')
```

Utilizando o mesmo objeto Client, podemos criar um pagamento recorrente. Para isto rodamos o seguinte comando:

Sabendo que nosso client ainda está usando o objeto transaction da classe TransactionRequest

```ruby
client.execute( command: 'recurring',
                start_date: '2013-09-10',
                processor_id: 1,
                reference_num: '298374239848',
                customer_id: '12345',
                token: 32jk4h2kj3=,
                frequency: 1,
                period: 'monthly',
                installments: 10,
                failure_threshold: 1,
                charge_total: '45.75')
```

## Requisição de consulta:

Agora precisamos acompanhar nossa transação nas operadoras, para isto utilizamos a Api de consulta. As operações disponíveis para consulta são:

* ```one_transaction_report```: resgata os detalhes da transação com o transactionId passado.
* ```list_transactions_report```: resgata uma lista de transações filtradas com valores passados.
* Até a criação deste post não existia operação de consulta por orderId, foi anunciada esta operação na próxima release da API.

Para consultar uma transação temos os seguintes passos:

```ruby
rapi = Maxipago::RequestBuilder::RapiRequest.new(MP_ID, MP_APIKEY)

client.use(rapi)

client.execute(command: 'one_transaction_report', transaction_id: '98765')
```

E além disso podemos também consultar uma lista de transações:

```ruby
rapi = Maxipago::RequestBuilder::RapiRequest.new(MP_ID, MP_APIKEY)

client.use(rapi)

client.execute(command: 'list_transactions_report', period: 'today')
```

Neste caso estamos consultando todas as transações do dia de hoje.

Estas são as principais operações da API Maxipago utilizando esta gem. Para mais informações sobre às operações sempre se pode contar com a [Expecificação da API](http://www.maxipago.com/docs/maxiPago_API_Ultima.pdf).
