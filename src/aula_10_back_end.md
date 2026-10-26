# Programação Back-end

[Slides desta aula (PDF)](slides/aula_11_php.pdf), que cobrem também o material de [PHP](aula_11_php.md).

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
* Nginx ([https://nginx.org/](https://nginx.org/));
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

## Preparação do ambiente de desenvolvimento

Para executar PHP no computador, são necessários o interpretador da linguagem e um servidor web que repasse a ele as requisições.

### Servidor embutido do PHP

O interpretador do PHP traz um servidor web de desenvolvimento, iniciado pelo terminal. É o servidor usado nas aulas. Dentro da pasta do projeto, execute:

```bash
php -d display_errors=1 -S localhost:8000
```

e abra no navegador `http://localhost:8000/arquivo.php`. Quando o endereço não tem nome de arquivo, o servidor procura um `index.php` e depois um `index.html` na pasta. `Ctrl+C` encerra o servidor.

A opção `-d display_errors=1` faz o PHP mostrar as mensagens de erro na própria página. Sem ela, conforme a configuração instalada, a página com erro pode chegar em branco; a mensagem continua aparecendo no terminal onde o servidor está rodando, junto com o status de cada requisição.

A extensão Live Server e o `python3 -m http.server`, usados no material de Fetch API e JSON, entregam o arquivo `.php` sem executá-lo: o navegador mostra o código ou oferece o arquivo para *download*. Para páginas PHP, use o servidor embutido.

O manual do PHP avisa que esse servidor foi feito para desenvolvimento e não deve ser usado em rede pública ([documentação](https://www.php.net/manual/pt_BR/features.commandline.webserver.php)).

Para instalar o interpretador no Linux:

```bash
sudo apt install php-cli     # Debian e Ubuntu
sudo dnf install php-cli     # Fedora
```

Confira a instalação com `php -v`, que mostra a versão.

### Pilha LAMP no Linux

Em servidores Linux, a instalação mais comum é a pilha **LAMP** (Linux, Apache, MariaDB/MySQL e PHP), em que o Apache recebe as requisições e repassa os arquivos `.php` ao interpretador.

#### Exibição de erros no PHP

Em ambientes de produção, os erros de código ficam ocultos do usuário final por segurança. Durante o desenvolvimento, o PHP deve exibir esses erros no navegador, para facilitar a correção.

Para ativar a exibição, localize o arquivo de configuração `php.ini`. Em sistemas que usam Apache, o caminho costuma ser algo como `/etc/php/8.x/apache2/php.ini` (onde `8.x` corresponde à versão instalada).

Abra o arquivo, localize a opção `display_errors` e altere o seu valor para `On`:

```ini
display_errors = On
```

Após salvar o arquivo, é necessário reiniciar o serviço do Apache para que as novas configurações entrem em vigor. Em distribuições Linux atuais, o comando é:

```bash
sudo systemctl restart apache2
```

### Pacotes para Windows

No Windows, a forma mais prática de configurar o ambiente é instalar um pacote que reúne servidor web, banco de dados e PHP já configurados:

* [XAMPP](https://www.apachefriends.org/index.html) - Apache + MariaDB + PHP + Perl
* [Laragon](https://laragon.org/) - alternativa ao XAMPP para Windows.

O XAMPP também permite usar o servidor embutido: na instalação padrão, o interpretador fica em `C:\xampp\php\php.exe`, e o comando do servidor embutido vira `C:\xampp\php\php.exe -d display_errors=1 -S localhost:8000`.

## Renderização no servidor, APIs e SPAs

O modelo estudado nesta parte da disciplina é a renderização no servidor (*Server-Side Rendering*, SSR): o back-end monta o HTML completo de cada página e o envia ao navegador. Ele continua muito usado e é a base para entender a arquitetura Web.

Há outro modelo, em que a geração do visual muda de lugar. A turma já viu metade dele no material de Fetch API e JSON, quando a página pediu o `produtos.json` com `fetch` e montou os cartões pelo DOM:

* **API (*Application Programming Interface*):** o back-end processa as regras de negócio, consulta o banco de dados e devolve apenas os dados, em geral em JSON, sem HTML. No lugar do `produtos.json` fixo, a resposta é gerada por um programa a cada requisição.
* **SPA (*Single Page Application*):** o front-end é uma aplicação JavaScript que roda no navegador, pede os dados à API com `fetch` e monta as telas pelo DOM, sem recarregar a página a cada clique. Bibliotecas como React, Angular e Vue são usadas nesse modelo.

Esse segundo modelo é assunto de disciplinas seguintes do curso.
