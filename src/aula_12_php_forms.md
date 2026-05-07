# Aula 12 - PHP - SuperGlobals, Formulários, Validação e Includes.

## Bibliografia recomendada para o tema:

* [Documentação Oficial do PHP - Variáveis Pré-definidas](https://www.php.net/manual/pt_BR/reserved.variables.php)
* [w3schools - PHP SuperGlobals](https://www.w3schools.com/php/php_superglobals.asp)
* [w3schools - PHP Forms](https://www.w3schools.com/php/php_forms.asp)

## Código de exemplo

<https://gitlab.com/ds122-alexkutzke/ds122-php-forms-example>

---

## Arrays SuperGlobals

Ao iniciar um script PHP, o interpretador instancia automaticamente uma série de Arrays associativos globais, comumente denominados **SuperGlobals**. Essas variáveis estão disponíveis em qualquer escopo do código (dentro ou fora de funções) e carregam informações vitais sobre o ambiente do servidor, cabeçalhos HTTP e, principalmente, os dados enviados pelo usuário na requisição.

Os Arrays SuperGlobals mais utilizados são:

* `$_SERVER`: Informações sobre o ambiente do servidor, cabeçalhos HTTP, caminhos e localização de scripts.
* `$_POST`: Reúne dados enviados por um formulário utilizando o método HTTP POST (dados invisíveis na URL).
* `$_GET`: Reúne dados enviados através da URL (parâmetros de *Query String*) ou formulários com método HTTP GET.
* `$_REQUEST`: Um array que contém o conteúdo combinado de `$_GET`, `$_POST` e `$_COOKIE`. (Recomenda-se usar `$_GET` ou `$_POST` explicitamente por questões de segurança e clareza).
* `$_FILES`: Controla informações sobre arquivos enviados por Upload.
* `$_SESSION`: Informações sobre as sessões ativas do usuário.
* `$_COOKIE`: Cookies enviados pelo navegador do cliente.
* `$GLOBALS`: Acesso a todas as variáveis globais definidas no código (seu uso direto para alterar variáveis de fora de uma função é considerado má prática na programação moderna, devendo-se priorizar o retorno de funções e passagem de parâmetros).
* `$_ENV`: Variáveis de ambiente.

Exemplo de consulta ao array `$_SERVER`:
```php
<?php 
// Exibindo informações sobre o servidor e a requisição atual
echo "Caminho do script: " . htmlspecialchars($_SERVER['PHP_SELF']) . "<br>";
echo "Nome do servidor: " . $_SERVER['SERVER_NAME'] . "<br>";
echo "Host: " . $_SERVER['HTTP_HOST'] . "<br>";
echo "Navegador/User-Agent: " . $_SERVER['HTTP_USER_AGENT'] . "<br>";
?>
```
*Atenção:* O uso de `htmlspecialchars()` no `PHP_SELF` é fundamental para evitar ataques de injeção de código, como veremos na seção de XSS.

---

## Formulários

A forma primária de coletar informações dos usuários em uma aplicação Web clássica é através de formulários HTML. Para que a comunicação entre o front-end e o back-end ocorra, o formulário precisa definir dois atributos cruciais:

1. **`action`**: A URL (recurso ou arquivo PHP) para onde os dados serão enviados.
2. **`method`**: O verbo HTTP utilizado para o envio (`GET` ou `POST`).

Além disso, é obrigatório que cada campo do formulário (`<input>`, `<select>`, `<textarea>`) possua o atributo **`name`**. O PHP utilizará o valor desse atributo como a "chave" no array SuperGlobal para resgatar o dado digitado.

Abaixo, um exemplo de um formulário de busca simples:

```html
<!DOCTYPE html>
<html lang="pt-BR">
  <head>
    <meta charset="utf-8">
    <title>Teste de formulário</title>
  </head>
  <body>
    <form action="form_test.php" method="get">
      <label for="busca">Pesquisar:</label>
      <input type="text" id="busca" name="termo_busca"> 
      <button type="submit">Buscar</button>
    </form>
  </body>
</html>
```

Quando o usuário clicar em "Buscar", o navegador fará uma requisição para `form_test.php?termo_busca=valor_digitado`.

Abaixo, o arquivo `form_test.php` que processa essa requisição:

```php
<?php
  // O operador ?? (Null Coalescing) verifica se o campo foi enviado. 
  // Se não foi, atribui uma string vazia, evitando erros de "Undefined array key".
  $busca = $_GET["termo_busca"] ?? '';
?>

<!DOCTYPE html>
<html lang="pt-BR">
  <head>
    <meta charset="utf-8">
    <title>Resultado da Busca</title>
  </head>
  <body>
    <h2>Você buscou por:</h2>
    <!-- O htmlspecialchars protege contra a execução acidental de tags HTML digitadas pelo usuário -->
    <p><?= htmlspecialchars($busca) ?></p>
  </body>
</html>
```

### Formulários com a mesma página (Self-submitting)

É muito comum que o formulário e o código PHP que o processa estejam no mesmo arquivo. Para isso, omitimos o atributo `action` ou apontamos para o próprio script. Também verificamos o método da requisição para saber se devemos apenas exibir o formulário ou também processar os dados.

```php
<?php
$nome = '';
$mensagem = '';

// Verifica se o formulário foi submetido
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // Resgata o valor. Se não existir, assume string vazia.
    $nome = $_POST['fname'] ?? '';
    
    if (empty(trim($nome))) {
        $mensagem = "O nome é obrigatório!";
    } else {
        $mensagem = "Olá, " . htmlspecialchars($nome) . "!";
    }
}
?>

<!DOCTYPE html>
<html>
<body>

<!-- htmlspecialchars impede que o PHP_SELF seja vetor de ataque -->
<form method="post" action="<?= htmlspecialchars($_SERVER['PHP_SELF']) ?>">
  <label for="fname">Nome:</label>
  <input type="text" id="fname" name="fname" value="<?= htmlspecialchars($nome) ?>">
  <button type="submit">Enviar</button>
</form>

<p><strong><?= $mensagem ?></strong></p>

</body>
</html>
```

---

## Validação

Validação é o processo de verificar se os dados recebidos estão dentro das regras de negócio esperadas e se são seguros. Nunca se deve confiar nos dados enviados pelo usuário.

A validação ocorre em duas camadas:
1. **Front-end (Client-side):** Melhora a experiência do usuário, alertando erros imediatamente antes do envio, poupando processamento do servidor. Pode ser burlada facilmente.
2. **Back-end (Server-side):** É a validação definitiva e obrigatória. Garante a integridade e segurança do sistema, pois não pode ser manipulada pelo usuário.

### Validação Front-end (Client-side)

A forma mais moderna e eficiente de fazer validação no front-end é utilizando os próprios atributos do **HTML5**, sem necessidade imediata de JavaScript.

* Atributos úteis: `required`, `minlength`, `maxlength`, `min`, `max`, `pattern` (Expressões Regulares), e os tipos semânticos (`type="email"`, `type="number"`).

Caso lógicas mais complexas sejam necessárias, utilizamos **JavaScript puro (Vanilla JS)** para interceptar o evento de submissão (`submit`):

```html
<form id="form-test" action="processa.php" method="post">
    <input type="text" name="nome" id="nome_input">
    <span id="erro-nome" style="color: red;"></span>
    <button type="submit">Enviar</button>
</form>

<script>
    document.getElementById("form-test").addEventListener("submit", function(event) {
        const nomeInput = document.getElementById("nome_input").value;
        const erroSpan = document.getElementById("erro-nome");

        // Se estiver vazio, exibe o erro e cancela o envio
        if (nomeInput.trim() === "") {
            erroSpan.textContent = "O nome é obrigatório.";
            event.preventDefault(); // Impede o formulário de ser enviado ao servidor
        } else {
            erroSpan.textContent = "";
        }
    });
</script>
```

### Validação Back-end (Server-side)

No PHP, verificamos se os dados existem, se estão no formato correto e os higienizamos (Sanitização).

Um fluxo clássico de sanitização básica manual envolve a remoção de espaços nas extremidades e a conversão de caracteres especiais HTML:

```php
<?php
function limpa_dado($texto) {
  $texto = trim($texto); // Remove espaços no início e no final
  $texto = htmlspecialchars($texto, ENT_QUOTES, 'UTF-8'); // Converte caracteres como < e > para entidades HTML
  return $texto;
}

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    $nome = limpa_dado($_POST["nome"] ?? '');
    
    if (empty($nome)) {
        echo "O campo nome é inválido ou está vazio.";
    }
}
?>
```

Para validações avançadas e modernas, o PHP oferece a família de funções **`filter_var`**, que já vem com regras prontas para e-mails, URLs e números:

* Documentação: [filter_var](http://php.net/manual/pt_BR/function.filter-var.php)

---

## Segurança: XSS e SQL Injection

A internet é um ambiente hostil. Formulários são a principal porta de entrada para ataques contra aplicações Web.

### Cross-site Scripting (XSS)

Ocorre quando a aplicação recebe dados maliciosos contendo scripts (como JavaScript) e os exibe para outros usuários sem o devido tratamento.

Se um usuário preencher o campo "Nome" com:
`José <script>alert('Roubei seus cookies!');</script>`

E a aplicação fizer apenas `echo $_POST['nome'];`, o navegador interpretará a tag `<script>` e executará o código.

**Como prevenir:** Utilize **SEMPRE** a função `htmlspecialchars()` antes de exibir qualquer dado gerado pelo usuário no HTML. Ela transforma `<` em `&lt;` e `>` em `&gt;`, fazendo o navegador tratar o conteúdo apenas como texto inofensivo.

### SQL Injection (Injeção de SQL)

Consiste no envio de trechos de código SQL malicioso através dos formulários para manipular a estrutura da consulta (Query) feita ao Banco de Dados.

Se o código PHP for escrito assim:
`$sql = "SELECT * FROM Usuarios WHERE id = " . $_POST['id'];`

O usuário pode digitar no campo ID: `105 OR 1=1`.
A consulta final no servidor ficará:
`SELECT * FROM Usuarios WHERE id = 105 OR 1=1;`

Como `1=1` é sempre verdadeiro, o banco retornará os dados de **todos** os usuários, burlando o sistema.

**Como prevenir:** No desenvolvimento back-end moderno, previne-se injeção de SQL utilizando a biblioteca **PDO** com as chamadas **Prepared Statements** (Consultas Preparadas), onde o dado enviado pelo usuário é tratado separadamente da instrução SQL.

---

## Includes e Requires

É uma boa prática modularizar sistemas grandes separando o código em múltiplos arquivos menores (ex: um arquivo para o cabeçalho, um para o rodapé, outro para conexão com o banco).

O PHP possui quatro construções de linguagem para inclusão de arquivos:

* `include 'arquivo.php';`: Tenta incluir e avaliar o arquivo especificado. Se o arquivo não existir, o PHP emite um **Aviso (Warning)**, mas **continua a execução** do restante do script.
* `require 'arquivo.php';`: Tenta incluir o arquivo. Se o arquivo não existir, o PHP emite um **Erro Fatal (Fatal Error)** e **interrompe a execução** do script imediatamente. (Mais seguro para arquivos cruciais).
* `include_once` e `require_once`: Funcionam de forma idêntica aos seus pares, porém o PHP verificará se o arquivo já foi incluído anteriormente durante aquela execução. Se já foi, não o incluirá novamente, evitando redefinição de funções ou variáveis em cascata.

Exemplo de uso prático:

```php
// arquivo: variaveis.php
<?php
$cor = 'verde';
$fruta = 'maçã';
?>

// arquivo: teste.php
<?php
// Tenta imprimir variáveis que ainda não existem
echo "Uma $fruta $cor. <br>"; 

// Inclui o arquivo que contém as variáveis
require 'variaveis.php';

// Agora as variáveis estão disponíveis no escopo
echo "Uma $fruta $cor."; // Saída: Uma maçã verde.
?>
```
