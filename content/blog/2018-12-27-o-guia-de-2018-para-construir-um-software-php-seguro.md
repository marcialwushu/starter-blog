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

### roave / avisos de segurança ###

O pacote de [alertas de segurança da Roave](https://github.com/Roave/SecurityAdvisories) usa o [repositório Friends of PHP](https://github.com/FriendsOfPHP/security-advisories) para garantir que seu projeto não dependa de pacotes vulneráveis conhecidos.

```php
composer require roave/security-advisories:dev-master   
```

Como alternativa, você pode [enviar seu ```composer.lock``` arquivo para o Sensio Labs](https://github.com/FriendsOfPHP/security-advisories#checking-for-vulnerabilities) como parte de um fluxo de trabalho de rotina de avaliação de vulnerabilidade automatizada para alertá-lo sobre qualquer pacote desatualizado.

### vimeo / salmo ###

[O Psalm](https://github.com/vimeo/psalm) é uma ferramenta de análise estática que ajuda a identificar possíveis bugs no seu código. Embora existam outras boas ferramentas de análise estática (como [Phan](https://github.com/phan/phan) e [PHPStan](https://github.com/phpstan/phpstan), que são excelentes), se você precisar de suporte ao PHP 5, o Psalm é a ferramenta de análise estática para o PHP 5.4+.

Usando o Psalm é relativamente fácil:

```php
# Version 1 doesn't exist yet, but it will one day:
composer require --dev vimeo/psalm:^0

# Only do this once:
vendor/bin/psalm --init

# Do this as often as you need:
vendor/bin/psalm

```

Se você estiver executando isso em uma base de código existente pela primeira vez, poderá ver muito vermelho. A menos que você esteja construindo um aplicativo tão grande quanto o WordPress, é improvável que o esforço necessário para que todos os testes sejam aprovados se qualificará como Herculano.

Independentemente da ferramenta de análise estática que você usa, recomendamos associá-la ao fluxo de trabalho de integração contínua (se aplicável) para que ela seja executada após cada alteração de código.

## HTTPS e segurança do navegador ## 

>Em resumo: HTTPS, [que deve ser testado](https://www.ssllabs.com/) , e [cabeçalhos de segurança](https://securityheaders.io/).

Em 2018, não será mais aceitável que os sites sejam acessíveis por meio de HTTP desprotegido. Felizmente, foi possível obter certificados TLS gratuitamente e renová-los automaticamente, graças ao protocolo ACME e à [autoridade de certificação Let's Encrypt](https://letsencrypt.org/).

Integrar o ACME ao seu servidor da Web é muito fácil.

- [Caddy](https://caddyserver.com/) : Baked-in automaticamente.
- [Apache](https://letsencrypt.org/2017/10/17/acme-support-in-apache-httpd.html) : Em breve estará disponível como mod_md. Até lá, existem tutoriais de alta qualidade disponíveis na Internet.
- [Nginx](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/) : relativamente simples.

Você pode estar pensando: "Ok, eu tenho um certificado TLS. Agora eu tenho que passar horas mexendo na configuração antes que ela seja segura e rápida".

Não! [A Mozilla está de costas](https://mozilla.github.io/server-side-tls/ssl-config-generator/) . Você pode usar o gerador de configuração para criar os [ciphersuites recomendados com](https://wiki.mozilla.org/Security/Server_Side_TLS) base no seu público-alvo.

HTTPS (HTTP sobre TLS) é [absolutamente não negociável](https://stackoverflow.com/a/2336738/2224584) se você quiser que seu site seja seguro. O uso do HTTPS elimina instantaneamente várias classes de ataque a seus usuários (injeção de conteúdo intermediário, escutas telefônicas, ataques de repetição e várias formas de manipulação de sessão que, de outra forma, permitiriam a representação do usuário).

## Cabeçalhos de segurança ##

No entanto, embora colocar HTTPS no seu servidor ofereça inúmeros benefícios de segurança e desempenho para seus usuários, você pode ir além, utilizando outros recursos de segurança do navegador. A maioria deles envolve o envio de um cabeçalho de resposta HTTP junto com seu conteúdo.

- ```Content-Security-Policy```
  - Você deseja esse cabeçalho porque ele oferece um controle refinado sobre os recursos internos e externos que podem ser carregados pelo navegador, o que fornece uma camada poderosa de defesa contra vulnerabilidades de cross-site scripting.
- Consulte o [CSP-Builder](https://github.com/paragonie/csp-builder) para obter uma maneira rápida e fácil de implantar / gerenciar políticas de segurança de conteúdo.
  - Para uma análise mais aprofundada, esta [introdução aos cabeçalhos da Política de Segurança de Conteúdo](https://scotthelme.co.uk/content-security-policy-an-introduction/) por Scott Helme é um excelente ponto de partida.
- ```Expect-CT```
  - Você deseja esse cabeçalho porque ele adiciona uma camada de proteção às autoridades de certificação desonestos / comprometidas, forçando os agentes mal-intencionados a publicar evidências de seus certificados emitidos incorretamente em uma estrutura de dados somente de anexo e verificável publicamente. [Saiba mais sobre ```Expect-CT```](https://scotthelme.co.uk/a-new-security-header-expect-ct/).
  - Defina-a ```enforce,max-age=30``` inicialmente e aumente à ```max-age``` medida que você ganha mais confiança de que esse cabeçalho não causará interrupções no serviço.
  

