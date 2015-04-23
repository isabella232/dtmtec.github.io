---
layout: post
title: Limpando Um Repositório Git
date: 2013-05-09 15:10
comments: true
author: luis.vasconcellos
read_time: 1 min
categories: git
---

Ao implementar uma funcionalidade envolvendo uma conta na Amazon AWS, percebi que havia comitado o arquivo .env do foreman que continha o access key e o access secret da conta. Eu obviamente não queria isso, pois qualquer pessoa com acesso ao repositório poderia encontrar as credenciais de segurança da conta e apartir daí ter acesso total a ela.

<!-- more -->

O que eu precisava era de uma ferramenta que me permitisse iterar em cima de cada commit do repositório, encontrar um determinado arquivo e remove-lo, e justamente para isso, eis que encontro o  ```git filter-branch```. [Da documentação do git](http://git-scm.com/book/ch6-4.html), temos:

> Isso ocorre com uma certa frequência. Alguém acidentalmente comita um arquivo binário enorme, e você quer removê-lo do repositório inteiro. Talvez você acidentalmente tenha commitado um arquivo que continha uma senha, e agora você quer tornar o projeto open-source. **filter-branch** é a ferramenta que você quer usar para limpar o seu repositório inteiro

## Removendo um arquivo passwords.txt  ##

Nesse exemplo, vamos supor que um arquivo chamado passwords.txt foi comitado e queremos remover do repositorio a referência que qualquer commit possa ter ele:

```
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD
```

## Mudando um endereço de email ##

O comando filter branch pode ser utilizado não só para remoção de arquivos, mas também para alteração de alguma informação do repositório. Nesse exemplo, vamos alterar no repositório um endereço de email que inicialmente era **luis@localhost** para **luis@example.com**:

```
git filter-branch --commit-filter '
    if [ "$GIT_AUTHOR_EMAIL" = "luis@localhost" ];
    then
            GIT_AUTHOR_NAME="Luis Vasconcellos";
            GIT_AUTHOR_EMAIL="luis@example.com";
            git commit-tree "$@";
    else
            git commit-tree "$@";
    fi' HEAD
```

É bom observar que essas modificações mudarão o repositório de uma tal maneira que qualquer push feito imediatamente após essa mudança terá que ser feito com um force, usando a flag -f no final do comando.
