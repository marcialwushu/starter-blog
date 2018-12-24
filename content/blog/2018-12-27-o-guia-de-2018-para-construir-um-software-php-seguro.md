---
title: O Guia de 2018 para construir um software PHP seguro
date: 2018-12-27
tags:
  - PHP
  - Code
published: true
---

À medida que o ano de 2018 se aproxima, os tecnólogos em geral - e os desenvolvedores da Web em particular - devem descartar muitas de suas antigas práticas e crenças sobre o desenvolvimento de aplicativos PHP seguros. Isto é especialmente verdadeiro para quem não acredita que tal façanha seja possível.

Este guia deve servir como um complemento ao e-book, [PHP: The Right Way](http://www.phptherightway.com/) , com uma forte ênfase na segurança e não nos tópicos gerais do programador PHP (por exemplo, estilo de código).

Índice

- [Versões do PHP](#versoes-do-php)
- [Gerenciamento de Dependências com o Composer](#gerenciamento-de-dependencias-com-o-composer)
  - Pacotes Recomendados
- HTTPS e segurança do navegador
  - Cabeçalhos de segurança
  - Integridade de Sub-recursos
  - Relacionamentos de Documentos
- Desenvolvendo Software PHP Seguro
  - Interação de banco de dados
  - Uploads de arquivos
  - Scripts entre sites (XSS)
  - Falsificação de Solicitação Entre Sites (CSRF)
  - Ataques XML (XXE, injeção XPath)
  - Desserialização e injeção de objetos PHP
  - Hashing de Senha
  - Criptografia de Uso Geral
  - Aleatoriedade
  - Solicitações HTTPS do lado do servidor
  - Coisas a evitar
- Casos de Uso Especializados
  - Criptografia pesquisável
  - Autenticação baseada em tokens sem canais laterais
  - Desenvolvendo APIs seguras
  - Log de eventos de segurança com o Chronicle
- Uma palavra do autor
- Recursos
- Traduções deste artigo

## Versões do PHP ##

>Resumindo: a menos que você não possa evitar, você quer rodar o **PHP 7.2** no ano de 2018, e planeja mudar para 7.3 no início de 2019.

O PHP 7.2 foi lançado em 30 de novembro de 2017.

No momento em que este artigo foi escrito, apenas o PHP 7.1 e 7.2 recebem suporte ativo dos desenvolvedores da linguagem de programação PHP, enquanto o PHP 5.6 e 7.0 recebem os patches de segurança por aproximadamente mais um ano.

Alguns sistemas operacionais fornecem suporte de longo prazo para versões do PHP não suportadas, mas essa prática é geralmente considerada prejudicial. Em particular, seu mau hábito de retroceder patches de segurança sem incrementar os números de versão torna difícil raciocinar sobre a segurança de um sistema que recebe apenas sua versão do PHP.

Conseqüentemente, independentemente das promessas feitas por outro fornecedor, você deve sempre se esforçar para [executar somente o suporte ativo](http://php.net/supported-versions.php) do PHP a qualquer momento, se puder ajudá-lo. Dessa forma, mesmo que você acabe com uma versão somente de segurança por um tempo, um esforço consistente para atualizar manterá sua vida livre de surpresas desagradáveis.

## Gerenciamento de Dependências com o Composer ##

>Em suma: use o Composer.

A solução de gerenciamento de dependências de última geração para o ecossistema PHP é o [Composer](https://getcomposer.org/) . PHP: O Caminho Certo tem uma seção inteira dedicada a [começar com o Composer](http://www.phptherightway.com/#dependency_management), que é altamente recomendável.

Se você não estiver usando o Composer para gerenciar suas dependências, você acabará em uma situação em que uma das bibliotecas de software das quais você depende ficarão gravemente desatualizadas e, em seguida, uma exploração de uma vulnerabilidade em versões mais antigas. versões começa a circular entre criminosos de computador.

Importante : Lembre-se sempre de [manter suas dependências atualizadas](http://www.phptherightway.com/#updating-your-dependencies) enquanto desenvolve o software. Felizmente, este é um one-liner:

```php
composer update
```

Se você está fazendo algo particularmente especializado que requer o uso de extensões PHP (que são escritas em C), você não pode instalá-las com o Composer. Você também precisará do PECL.

## Pacotes Recomendados ##

Independentemente do que você está construindo, você certamente irá se beneficiar dessas dependências. Isto é uma adição ao que a maioria dos desenvolvedores de PHP recomenda (PHPUnit, PHP-CS-Fixer, etc.).





