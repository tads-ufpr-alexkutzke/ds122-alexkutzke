# Aula 10 - Programação Back-end

## Programação Back-end

**Back-end**, ou *server-side*, é o termo utilizado para fazer referência à parte de uma aplicação Web que é executada na máquina servidor. Em sua essência, envolve as operações relacionadas ao processamento da lógica de negócio e à produção e entrega de arquivos HTML com conteúdo dinâmico. Trata, em grande medida, do gerenciamento e da persistência dos **dados** da aplicação Web. Por essa razão, o back-end atua quase sempre integrado a sistemas de bancos de dados.
Para que uma aplicação Web seja capaz de gerar conteúdo dinâmico baseando-se na arquitetura de renderização no servidor, é necessário o seguinte conjunto:

* Um servidor Web que receba e responda a requisições HTTP/HTTPS;
* Um interpretador ou programa capaz de processar as regras de negócio e produzir o código HTML dinâmico a partir da requisição do cliente;
* Um serviço de banco de dados para armazenar e fornecer os dados requisitados pelo programa.

## Servidores Web

Um **servidor web** é um software que "escuta" constantemente por requisições de rede e as responde à medida que são recebidas. O padrão é que esses servidores escutem requisições HTTP na porta 80 e requisições seguras HTTPS na porta 443.

Portanto, a função básica de um servidor web é: dada uma requisição do cliente, localizar ou processar o recurso solicitado e enviá-lo de volta embutido em uma resposta HTTP.

Alguns servidores Web amplamente utilizados são:
* Apache HTTP Server ([https://httpd.apache.org/](https://httpd.apache.org/));
* Nginx ([https://www.nginx.com/](https://www.nginx.com/));
* Apache Tomcat ([http://tomcat.apache.org/](http://tomcat.apache.org/));
* Lighttpd ([http://www.lighttpd.net/](http://www.lighttpd.net/)).

### Tradução de URL para arquivos locais ao servidor

No modelo tradicional de entrega de páginas, ao receber uma requisição, o servidor web procura em seus arquivos locais o recurso a ser enviado ao cliente. Por exemplo, ao receber uma requisição GET para a URL `http://example.com/dir1/dir2/arquivoX.html`, o servidor tentará localizar esse arquivo na estrutura de pastas contida dentro de seu **diretório raiz** (*Document Root*).

O **diretório raiz** é a pasta configurada na máquina do servidor onde ficam armazenados os arquivos públicos do site. Em servidores Apache rodando no Linux, o diretório raiz padrão geralmente é o `/var/www/html`. Dessa forma, para a requisição GET citada, o servidor entregaria o arquivo localizado no caminho físico: `/var/www/html/dir1/dir2/arquivoX.html`.

Pontos importantes sobre este processo:
* Por questões de segurança, o servidor web é configurado para negar acesso a qualquer arquivo ou diretório que esteja fora da hierarquia do seu diretório raiz.
* Os recursos entregues não se limitam a arquivos HTML. O servidor web pode entregar imagens, arquivos PDF, folhas de estilo (CSS) e scripts de front-end, dependendo da requisição.

## Páginas dinâmicas

O processo descrito acima refere-se à entrega de recursos estáticos, onde o servidor apenas lê um arquivo do disco e o envia exatamente como foi salvo.

No entanto, aplicações web dependem de conteúdo dinâmico. Em um sistema de notas, por exemplo, o HTML retornado deve conter as informações específicas do usuário logado. Como seria inviável criar um arquivo HTML estático para cada usuário, o servidor precisa gerar esse conteúdo sob demanda.

Para a produção dinâmica, o servidor web trabalha em conjunto com módulos ou interpretadores de linguagem. **Não** é o servidor web (como o Apache) que processa a lógica de negócio ou acessa o banco de dados. O fluxo ocorre da seguinte forma: o servidor web recebe a requisição, identifica que o arquivo solicitado contém código dinâmico e repassa esse arquivo para o interpretador da linguagem.

Historicamente, essa comunicação entre o servidor web e os programas locais era feita através de um padrão chamado **CGI (Common Gateway Interface)**. Esse método era ineficiente, pois abria um novo processo no sistema operacional para cada acesso. Atualmente, os servidores web utilizam módulos integrados (como o `mod_php` no Apache) ou serviços de processamento dedicados (como o FastCGI/PHP-FPM) para executar as linguagens de programação com alto desempenho, gerando o HTML final e devolvendo-o ao servidor web, que o repassa ao cliente.

## PHP

O PHP (PHP: Hypertext Preprocessor) é uma linguagem de script desenhada especificamente para o desenvolvimento Web e que atua do lado do servidor. Sendo de código aberto e amplamente suportada, é uma das linguagens mais tradicionais para a geração de arquivos HTML dinâmicos.

Ao receber uma requisição por um arquivo `.php`, o servidor web aciona o interpretador do PHP. Este interpretador lê o arquivo de cima a baixo, executando exclusivamente os blocos de código delimitados pelas marcações da linguagem. O que estiver fora dessas marcações é tratado como texto puro (geralmente HTML). Ao final do processamento, o PHP devolve um arquivo HTML estático completo para o servidor enviar ao navegador do usuário.

Exemplo de um arquivo `index.php`:

```php
<!DOCTYPE html>
<html>
<body>

<?php
$mensagem = "Meu primeiro script PHP!";
echo "<p>" . $mensagem . "</p>";
?>

</body>
</html>
```

Ao passar o arquivo acima pelo interpretador, a lógica é processada e o cliente (navegador) recebe apenas o seguinte código final:

```html
<!DOCTYPE html>
<html>
<body>

<p>Meu primeiro script PHP!</p>

</body>
</html>
```

## Preparação do ambiente de desenvolvimento no Linux

Para desenvolver localmente, é necessário instalar um servidor web e o interpretador da linguagem. Em distribuições Linux, a abordagem mais comum é a instalação da pilha **LAMP** (Linux, Apache, MariaDB/MySQL e PHP).

### Exibição de erros no PHP

Em ambientes de produção, os erros de código ficam ocultos do usuário final por segurança. Durante o desenvolvimento, é fundamental que o PHP exiba esses erros no navegador para facilitar a correção.

Para ativar a exibição, localize o arquivo de configuração `php.ini`. Em sistemas modernos utilizando Apache, o caminho costuma ser algo como `/etc/php/8.x/apache2/php.ini` (onde `8.x` corresponde à versão instalada).

Abra o arquivo, localize a opção `display_errors` e altere o seu valor para `On`:

```ini
display_errors = On
```

Após salvar o arquivo, é necessário reiniciar o serviço do Apache para que as novas configurações entrem em vigor. Em distribuições Linux atuais, o comando é:

```bash
sudo systemctl restart apache2
```

## Preparação do ambiente de desenvolvimento no Windows

No Windows, a forma mais prática de configurar o ambiente de desenvolvimento é através da instalação de *bundles* (pacotes All-in-One), que instalam e pré-configuram o servidor Web, o banco de dados e o PHP simultaneamente.

Os pacotes mais comuns utilizados para este fim são:

* [XAMPP](https://www.apachefriends.org/index.html) - Apache + MariaDB + PHP + Perl
* [Laragon](https://laragon.org/) - Alternativa moderna e otimizada ao XAMPP/WAMP para Windows.
* [WAMP](http://www.wampserver.com/en/) - Apache + PHP + MySQL

### Modelos atuais: APIs e Arquiteturas Modernas

Embora o modelo de geração de páginas HTML no servidor (conhecido como *Server-Side Rendering* ou SSR) seja o pilar fundamental para o entendimento da arquitetura Web e ainda amplamente utilizado, o desenvolvimento de software moderno adotou também arquiteturas mais descentralizadas.

Para fins de contexto histórico, é importante saber que em disciplinas futuras você estudará um padrão arquitetural diferente, focado em **APIs** e **Single Page Applications (SPAs)**. Nesse cenário, a responsabilidade de gerar o visual da página muda de lugar:

* **API (*Application Programming Interface*):** O Back-end deixa de misturar lógica com HTML. Sua única função passa a ser processar regras de negócio, consultar o banco de dados e devolver apenas os **dados puros**. Esses dados transitam pela rede em um formato de texto universal e leve chamado **JSON** (*JavaScript Object Notation*).
* **SPA (*Single Page Application*):** O Front-end torna-se uma aplicação JavaScript robusta e independente (utilizando tecnologias como React, Angular ou Vue). Ele roda diretamente no navegador do usuário, pede os dados (JSON) para a API e "desenha" as telas dinamicamente, sem a necessidade de recarregar a página a cada clique.
