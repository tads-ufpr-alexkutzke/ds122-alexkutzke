# Aula 10 - Programação Back-end

## Programação Back-end

**Back-end**, ou server side, é o termo utilizado para se fazer referência à
parte de uma
aplicação Web que é executada na máquina servidor. Ou seja, em geral, envolve 
as operações relacionadas ao processamento/produção/entrega de arquivos HTML de conteúdo
dinâmico. Tratam, em grande medida, de detalhes dos **dados** da aplicação Web.
Por essa razão, aparecem, com frequência, atrelados a sistemas de bancos de
dados.

Para que uma aplicação Web seja capaz de gerar conteúdo dinâmico, é necessário,
no mínimo, o seguinte:

* Um servidor Web que responda a requisições HTTP;
* Um programa de computador capaz de produzir conteúdo dinâmico dado uma
  requisição do cliente;
* Se necessário, um servço de banco de dados acessado pelo programa citado
  acima.

## Servidores Web

Um **servidor web** nada mais é do que um programa que "escuta" constantemente
por requisições HTTP e as responde na medida em que são recebidas. Em geral,
tais servidores "escutam" na porta 80, porém isso é um parâmetro configurável.

Portanto, o serviço de um servidor web é: dada uma requisição no formato HTTP,
enviar o recurso solicitado ao cliente em uma resposta também HTTP.

Alguns servidores Web conhecidos são: 
* Apache (https://httpd.apache.org/);
* Nginx (https://www.nginx.com/);
* Puma (http://puma.io/);
* Apache Tomcat (http://tomcat.apache.org/);
* Lighttpd (http://www.lighttpd.net/).

### Tradução de URL para arquivos locais ao servidor

Ao receber uma requisição, o servidor web procura em seus arquivos locais o
recurso a ser enviado como resposta ao cliente. Por exemplo, ao receber uma
requisição GET para a url `http://example.com/dir1/dir2/arquivoX.html`, o
servidor irá tentar localizar um arquivo no seguinte caminho
`dir/dir2/arquivoX.html` dentro de seu **diretório raiz**.

O **diretório raiz** de um servidor web é um local na máquina que roda o servidor
onde estarão todos os recursos disponibilizados por ele. Por exemplo, servidores
Apache possuem como diretório rraiz o seguinte caminho: `/var/www`. Dessa forma,
ao receber a requisição GET acima, o servidor apache a responderia enviando o
seguinte arquivo local ao cliente: `/var/www/dir1/dir2/arquivoX.html`.

É importante salientar dois pontos:

* Por questões claras de segurança, é proibido ao servidor web entregar
  qualquer recurso que não esteja dentro (ou abaixo) de seu diretório raiz;
* Os recursos entregues por um servidor web podem ser de QUALQUER tipo. Ou seja,
  **NÃO** estão restritos a arquivos HTML, por exemplo. Assim, uma requisição
GET para `http://example.com/dir1/dir2/arquivoX.pdf` é perfeitamente possível.

## Páginas dinâmicas

No exemplo anterior, o servidor web entrega ao cliente recursos estáticos. Em
outras palavras, apenas envia arquivos na forma em que estão salvos em seu disco
local.

Atualmente, aplicações web produzem conteúdo dinâmico. Por exemplo: cada usuário
de uma aplicação é capaz de ver apenas seus registros pessoais, ou, em um site
de notícias, as manchetes mais importantes variam durante o dia. Tal
comportamento não seria possível, ou pelo menos seria muito desgastante,
utilizando apenas arquivos estáticos. Por essa razão, servidores web receberam a
funcionalidade de entregar conteúdos dinâmicos para suas requisições.

Para que isso ocorra, servidores web precisam trabalhar em conjunto com outros
programas capazes de produzir esse contéudo dinâmico. É importante notar que
**não** é tarefa do servidor web criar conteúdo dinâmico. Ele apenas recebe
requisições e entrega respostas. Para a produção dinâmica de conteúdo, o
servidor web repassa a requisição a um programa de computador que retorna o
conteúdo a ser enviado ao cliente.

Pela necessidade de produzir conteúdos dinâmicos para Web, em meados de 1997,
servidores Web iniciaram a implementação de um padrão chamada CGI (Common
Gateway Interface). Esse padrão especifica como um servidor web pode executar
comandos na máquina local para que, então, conteúdos dinâmicos sejam produzidos.
Dessa forma, é possível, por exemplo, a partir de uma requisição HTTP, o
servidor web executar um programa escrito em linguagem C, o qual produz um HTML
como resultado, e enviar este HTML de volta ao cliente. Pode-se realizar esse
processo com qualquer programa de qualquer linguagem.

Embora o CGI tenha sido utilizado por muito tempo, hoje ele está sendo
substituído por módulos de cada linguagem que rodam diretamente no servidor web.
Com isso, ganha-se em desempenho, já que o servidor consegue executar programas
"quase que nativamente".

## PHP

O PHP (PHP: Hypertext Preprocessor) é uma linguagem de script de propósito
geral, mas que se adequa muito bem às necessidades da programação Web. Além
disso, o PHP é de código aberto, o que significa que pode ser utilizada de
maneira gratuita. Com essa linguagem de programação é possível gerar arquivos
HTMLs dinâmicos.

Ao receber uma requisição HTTP, o servidor web irá procurar pelo recurso
solicitado. Se tal recurso for um arquivo PHP, o servidor irá, então, repassar
esse arquivo ao interpretador de PHP. Após interpretar o arquivo e executar o
código nele inserido, o interpretador irá retornar um arquivo HTML pronto a ser
enviado ao cliente.

Em essência, o PHP é um preprocessador de arquivos de texto. A questão é que
esses arquivos de texto podem ser arquivos HTML.

Por exemplo, um arquivo PHP simples, `index.php`, poderia ser o seguinte:

```php
<!DOCTYPE html>
<html>
<body>

<?php
echo "My first PHP script!";
?>

</body>
</html>
```

Ao passar o arquivo acima ao interpretador PHP, será retornado o seguinte
conteúdo:

```html
<!DOCTYPE html>
<html>
<body>

My first PHP script!

</body>
</html>
```

## Preparação do ambiente de desenvolvimento no Linux

Apenas consulte como instalar um servidor web (apache, por exemplo)
na sua distribuição. Além disso, não se esqueça de instalar o módulo
de PHP para o servidor web.

Em geral, você pode buscar pela sigla LAMP (Linux, Apache, MariaDB e PHP).

### Exibição de erros no PHP

Por vezes, para que o PHP exiba mensagens de erro é necessário alterar uma pequena
configuração. Basta encontrar o arquivo de configuração do seu php (geralmente está em algum local como: `/etc/php5/apache2/php.ini`.

Em seguida, procure a opção `display_errors` e a ative da seguinte maneira:

```bash
display_errors = On
```

Para concluir, reinicie o servidor apache2. Por exemplo:

```bash
sudo service apache2 restart
```

## Preparação do ambiente de desenvolvimento no Windows

Existem diversas maneiras para instalar um servidor Web e o PHP no Windows. Porém, a mais simples é utilizar
bundles (pacotes) que instalam um conjunto de softwares de uma só
vez. Exemplos de bundles são:

* [XAMPP](https://www.apachefriends.org/index.html) - Apache + MariaDB + PHP + Perl 
* [WAMP](http://www.wampserver.com/en/) - Apache + PHP + MySQL;
