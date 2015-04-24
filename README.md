# Simplifiying Ideas #

Bem vindo ao Simplifying Ideas, o blog da DTM. Sinta-se a vontade para postar o que você quiser. Nossas postagens geralmente giram em torno de linguagens de programação, frameworks, plataformas, e negócios.

## Instalação ##

```
git clone git@github.com:dtmtec/dtmtec.github.io.git simplifiying-ideas
cd simplifying-ideas
bundle install
```

## Configuração ##

Para que sua assinatura apareça no final de cada post, faça o seguinte:

  * Abra o arquivo ```_config.yml```.  No final dele preencha seus dados abaixo do seu nome e certifique-se de que a configuração ```display_signature``` esteja marcada para true.
  * No cabeçalho do arquivo de todo post que fizer, preencha a configuração ```author``` com o seu nome no formato ```nome.sobrenome```.  Qualquer dúvida é só consultar algum artigo existente.
  * Mova sua foto para a pasta ```assets/images/authors``` usando como nome do arquivo o seu nome no formato ```nome.sobrenome```. Hoje o blog está preparado só para fotos **jpg**, sinta-se a vontade para tornar isso mais genérico.

## Novo Artigo ##

1) Suba o servidor com ``` jekyll serve --watch ```

2) Copie algum artigo que esteja dentro da pasta ```_posts/``` e use-o como base para o seu próprio artigo, personalizando as configurações que se encontram no ínicio do arquivo de acordo com o seu post.

3) Para publicar o artigo, pasta subi-lo para o github ```git push origin master```
