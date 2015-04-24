---
layout: post
title: "Visão geral sobre REST"
date: 2012-11-07 22:53
comments: true
tags: featured
author: otavio.cardoso
read_time: 2 min
categories:
- rest
- http
---

REST significa **RE**presentational **S**tate **T**ransfer, Transferência de Estado Representacional. Para entender de onde veio esse nome, temos que conhecer primeiro os conceitos básicos do REST.

<!-- more -->

## O que é REST? ##

REST é um modelo de desenvolvimento para sistemas distribuídos, que serviu de molde para o protocolo HTTP.

## Por que "REST"? ##

Utilizaremos como exemplos dos conceitos o contexto de uma pizzaria. Sim, porque como é um modelo, REST não é limitado à web. Vamos a eles:

1. *Recursos (pizzas, bebidas)*: são os elementos de informação na aplicação;
2. *Identificadores de recurso (calabresa, presunto)*: são usados para identificar recursos específicos para interações no sistema;
3. *Representações de recurso (prato com fatia, cortada à francesa)*: são as formas dos recursos que podem ser obtidas pelos componentes do sistema;
4. *Metadados (ingredientes, modo de servir)*: informações descritivas sobre recursos ou representações;

Portanto, em uma aplicação que segue este modelo, o foco é voltado para os recursos da aplicação. Numa aplicação dita RESTful, uma **representação** de um recurso é enviada da aplicação servidor para a aplicação cliente, **transferindo** esta última de um **estado para outro**.

Usando o exemplo da pizzaria: os recursos são as pizzas. Quando a bandeja do faminto (nossa aplicação cliente) acessa balcao.da.pizzaria.com/pizzas/calabresa, um prato com uma fatia (a representação do recurso chamado calabresa) é transferido do balcão (nossa aplicação servidora) para a bandeja. Isso altera o estado da bandeja de "vazia" para "com prato cheio".

## E por que REST? ##

Pelas características nas quais o modelo REST se baseia, vários objetivos desejados para aplicações distribuídas são alcançados. Estas características são:

- *Arquitetura Cliente/Servidor (garçom, schifaizfavoire)*: como a arquitetura cliente/servidor naturalmente leva à definição de uma interface, a aplicação cliente independe de plataforma e é possível a evolução das duas partes, cliente e servidor, desde que a interface permaneça a mesma. Tal arquitetura também permite a simplificação dos componentes do servidor. Respectivamente, obtemos maior portabilidade, extensibilidade e escalabilidade;

- *Sem estado (anarquia)*: cada requisição do cliente para o servidor "se basta", ou seja, contém toda informação necessária para ser atendida pelo servidor. Sem precisar guardar nenhuma informação de estado, o servidor fica mais escalável por ser mais simples e não precisar gerenciar os estados de vários clientes. Além disso, o sistema fica mais confiável, visto que facilita a recuperação de falhas;

- *Cacheamento (diga não à chapinha)*: permitindo que uma cache seja mantida no cliente, podemos eliminar interações com requisições equivalentes, permitindo que o servidor fique mais livre e, portanto, o sistema fica mais escalável. Também temos aumento na performance percebida pelo usuário;

- *Interface uniforme (as mina pira nos milico)*: São os verbos, as operações que podem ser aplicadas aos recursos. No modelo REST a interface bem definida permite maior extensibilidade do sistema.

- *Camadas (como os ogros e as cebolas)*: Adicionar camadas entre cliente e servidor permite escalabilidade, uma vez que esses intermediários permitem balanceamento de carga. Além disso, os intermediários podem manter caches, melhorando a performance geral do sistema, e implementar políticas de segurança, melhorando a confiabilidade.

- *Código sob demanda (não consegui pensar em nada aqui)*: as funcionalidades do cliente podem ser aumentadas por envio de código sob demanda, que pode vir na forma de applets ou scripts, para serem executados. Com isso, são os componentes do cliente que podem ser simplificados e melhorar a extensibilidade. Porém, como isso reduz a visibilidade, ou seja, monitorar as ações no sistema fica mais difícil, essa característica é opcional.

## REST in peace ##

Com esse post, descrevemos brevemente o modelo REST, esclarecendo o porquê de sua aplicabilidade em sistemas distribuídos, como sistemas web.
