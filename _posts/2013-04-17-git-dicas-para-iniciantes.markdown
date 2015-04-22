---
layout: post
title: "Git: Dicas para iniciantes"
date: 2012-12-06 09:13
comments: true
author: João Mello
categories:
- git
---

O objetivo deste post é ajudar quem, como eu, está (muito) longe de ser um expert em git e já tropeçou em algum git rebase, erro de digitação na  descrição do commit, ou coisas do gênero.

Primeiramente é muito importante você se identificar no projeto, então, não se acanhe:
<!-- more -->

{% gist 4217835 arquivo1 %}

**Vamos começar pelos erros mais simples (e mais comuns):**

*"Não gostei da descrição do último commit gostaria de mudá-la"*

{% gist 4217835 arquivo2 %}

*"Gostaria de consertar um bug no arquivo que acabei de fazer commit"*

{% gist 4217835 arquivo3 %}

*"Gostaria de fazer modificações num commit anterior"*

{% gist 4217835 arquivo4 %}

Caso ocorra algum conflito ao reaplicar os commits, os conflitos deverão ser resolvidos manualmente e então  git rebase --continue

*"Notei que cometi um erro num commit 0f0d8a27622e7bf7f008983c4b8ee23bfb9843ab depois de muito tempo e já estou na branch manter"*

{% gist 4217835 arquivo5 %}

*"Gostaria de reverter minhas alterações sem fazer outro commit"*

Em um único arquivo ou diretório:

{% gist 4217835 arquivo6 %}

Último commit:

{% gist 4217835 arquivo7 %}

Bem, por enquanto é só. Espero que tenha ajudado aos iniciantes como eu mesmo fui ajudado na elaboração deste post. Grande abraço até a próxima.