# PHP: SuperGlobals, Formulários, Validação e Includes

[Slides desta aula (PDF)](slides/aula_12_php_forms.pdf)

## Objetivos

Ao final desta aula você deve ser capaz de:

1. Ler dados da requisição com `$_GET`, `$_POST` e `$_SERVER`;
2. Ligar um formulário HTML a um script PHP com `action`, `method` e `name`;
3. Escrever um formulário que envia os dados para a própria página;
4. Validar dados no front-end e no back-end;
5. Explicar XSS e SQL Injection, e como prevenir cada um;
6. Dividir o código em arquivos com `include` e `require`.

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
* `$_POST`: Reúne dados enviados por um formulário utilizando o método HTTP POST (os dados vão no corpo da requisição, e não na URL).
* `$_GET`: Reúne dados enviados através da URL (parâmetros de *Query String*) ou formulários com método HTTP GET.
* `$_REQUEST`: Um array que contém o conteúdo combinado de `$_GET` e `$_POST` e, conforme a configuração do servidor (diretiva `request_order` do `php.ini`), também de `$_COOKIE`. (Recomenda-se usar `$_GET` ou `$_POST` explicitamente por questões de segurança e clareza).
* `$_FILES`: Controla informações sobre arquivos enviados por Upload.
* `$_SESSION`: Informações sobre as sessões ativas do usuário.
* `$_COOKIE`: Cookies enviados pelo navegador do cliente.
* `$GLOBALS`: Acesso a todas as variáveis globais definidas no código (seu uso direto para alterar variáveis de fora de uma função é considerado má prática na programação moderna, devendo-se priorizar o retorno de funções e passagem de parâmetros).
* `$_ENV`: Variáveis de ambiente.

Exemplo de consulta ao array `$_SERVER`, num arquivo `server.php`:
```php
<?php 
// Exibindo informações sobre o servidor e a requisição atual
echo "Caminho do script: " . htmlspecialchars($_SERVER['PHP_SELF']) . "<br>";
echo "Nome do servidor: " . $_SERVER['SERVER_NAME'] . "<br>";
echo "Host: " . $_SERVER['HTTP_HOST'] . "<br>";
echo "Navegador/User-Agent: " . $_SERVER['HTTP_USER_AGENT'] . "<br>";
?>
```

Texto exibido pela página, com o servidor embutido (`php -S localhost:8000`) e a requisição feita pelo `curl`. Num navegador, a última linha mostra a identificação do navegador:

```text
Caminho do script: /server.php
Nome do servidor: localhost
Host: localhost:8000
Navegador/User-Agent: curl/8.18.0
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

É muito comum que o formulário e o código PHP que o processa estejam no mesmo arquivo. Para isso, omitimos o atributo `action` ou apontamos para o próprio script. Também verificamos o método da requisição para saber se devemos apenas exibir o formulário ou também processar os dados. O primeiro acesso à página é um GET; o envio do formulário, um POST.

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

O atributo `value` do campo devolve o que foi digitado, e o usuário não precisa preencher de novo.

---

## Validação

Validação é o processo de verificar se os dados recebidos estão dentro das regras de negócio esperadas e se são seguros. Nunca se deve confiar nos dados enviados pelo usuário.

A validação ocorre em duas camadas:
1. **Front-end (Client-side):** Melhora a experiência do usuário, alertando erros imediatamente antes do envio, poupando processamento do servidor. Pode ser burlada facilmente.
2. **Back-end (Server-side):** É a validação definitiva e obrigatória. Garante a integridade e segurança do sistema, pois não pode ser manipulada pelo usuário.

### Validação Front-end (Client-side)

A forma mais moderna e eficiente de fazer validação no front-end é utilizando os próprios atributos do **HTML5**, sem necessidade imediata de JavaScript.

* Atributos úteis: `required`, `minlength`, `maxlength`, `min`, `max`, `pattern` (Expressões Regulares), e os tipos semânticos (`type="email"`, `type="number"`).

```html
<input type="email" name="email" required>
<input type="text" name="nome" required minlength="3" maxlength="60">
<input type="number" name="quantidade" min="1" max="10">
```

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

A sanitização básica manual remove os espaços nas extremidades com `trim()`. A conversão dos caracteres especiais do HTML, com `htmlspecialchars()`, fica para a hora de exibir o dado, como mostra a seção de XSS. Feita na entrada, ela gravaria no banco de dados `&lt;` no lugar de `<`.

```php
<?php
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    $nome = trim($_POST["nome"] ?? ''); // Remove espaços no início e no final

    if (empty($nome)) {
        echo "O campo nome é inválido ou está vazio.";
    } else {
        // Converte caracteres como < e > para entidades HTML, na hora de exibir
        echo "Olá, " . htmlspecialchars($nome) . "!";
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

**Como prevenir:** No desenvolvimento back-end moderno, previne-se injeção de SQL utilizando as chamadas **Prepared Statements** (Consultas Preparadas), onde o dado enviado pelo usuário é tratado separadamente da instrução SQL. Elas existem tanto na extensão **mysqli**, que usaremos na aula de PHP e MySQL, quanto na **PDO**.

---

## Includes e Requires

É uma boa prática modularizar sistemas grandes separando o código em múltiplos arquivos menores (ex: um arquivo para o cabeçalho, um para o rodapé, outro para conexão com o banco).

O PHP possui quatro construções de linguagem para inclusão de arquivos:

* `include 'arquivo.php';`: Tenta incluir e avaliar o arquivo especificado. Se o arquivo não existir, o PHP emite um **Aviso (Warning)**, mas **continua a execução** do restante do script.
* `require 'arquivo.php';`: Tenta incluir o arquivo. Se o arquivo não existir, o PHP emite um **Erro Fatal (Fatal Error)** e **interrompe a execução** do script imediatamente. (Mais seguro para arquivos cruciais).
* `include_once` e `require_once`: Funcionam de forma idêntica aos seus pares, porém o PHP verificará se o arquivo já foi incluído anteriormente durante aquela execução. Se já foi, não o incluirá novamente, evitando redefinição de funções ou variáveis em cascata.

Exemplo de uso prático, com dois arquivos na mesma pasta. Arquivo `variaveis.php`:

```php
<?php
$cor = 'verde';
$fruta = 'maçã';
?>
```

Arquivo `teste.php`:

```php
<?php
// Tenta imprimir variáveis que ainda não existem
echo "Uma $fruta $cor. <br>"; 

// Inclui o arquivo que contém as variáveis
require 'variaveis.php';

// Agora as variáveis estão disponíveis no escopo
echo "Uma $fruta $cor."; // Saída: Uma maçã verde.
?>
```

No primeiro `echo`, as variáveis ainda não existem. O PHP emite `Warning: Undefined variable $fruta` (e o mesmo para `$cor`) e imprime `Uma  .`, com as duas variáveis vazias.

---

## Exercícios

**Estes exercícios não valem nota** e não têm entrega. O conteúdo cai na Prova 3.

Os exercícios 1 a 4 continuam o `catalogo.php` da parte prática do material de [PHP](aula_11_php.md), na pasta `aula-php/`. Os demais criam arquivos novos na mesma pasta. Inicie o servidor dentro dela, como na aula anterior:

```bash
php -d display_errors=1 -S localhost:8000
```

**1.** No `catalogo.php`, troque a linha `$categoria = "Bebidas";` pela leitura de `$_GET["categoria"]`, com o operador `??` e a string vazia como valor padrão. Quando `$categoria` estiver vazia, mostre todos os produtos: a condição do `if` passa a ter duas partes, ligadas por `||`. Faça o mesmo na contagem de `$mostrados`. Confira os três endereços:

* `http://localhost:8000/catalogo.php`: três produtos;
* `http://localhost:8000/catalogo.php?categoria=Bebidas`: dois produtos;
* `http://localhost:8000/catalogo.php?categoria=Doces`: um produto.

**2.** Acima dos cartões, acrescente um `<h1>` com a categoria escolhida, como `Categoria: Bebidas`, que só aparece quando `$categoria` não está vazia. Abra `http://localhost:8000/catalogo.php?categoria=<i>Bebidas</i>` e confira que o título aparece em itálico: a tag digitada no endereço chegou ao HTML. Aplique `htmlspecialchars` à categoria no `<h1>`, recarregue e confira, com `Ctrl+U`, que a tag chega ao navegador como `&lt;i&gt;`.

**3.** No topo da página, acrescente um formulário com método GET e sem `action`, com um `<select name="categoria">` com as opções `Todas` (valor vazio), `Bebidas` e `Doces`, e um botão `Filtrar`. Escolha uma categoria, envie e confira, na barra de endereços, o mesmo formato do exercício 1. Depois, faça a opção escolhida continuar selecionada após o envio, com o atributo `selected` e um `if` na sintaxe alternativa:

```php
<option value="Bebidas" <?php if ($categoria === "Bebidas"): ?>selected<?php endif; ?>>Bebidas</option>
```

**4.** Acrescente ao formulário do exercício 3 um campo `<input type="text" name="busca">` e mostre apenas os produtos cujo nome contém o texto digitado, com `str_contains`. Para ignorar maiúsculas, compare o nome e a busca convertidos com `strtolower`. Mantenha o texto digitado no campo após o envio, com o atributo `value` e `htmlspecialchars`. Confira:

* `?categoria=Bebidas&busca=mate` mostra só o Chá mate;
* com a busca vazia, aparecem todos os produtos. Explique por quê, a partir do que `str_contains` devolve quando o texto procurado é vazio;
* a busca por `CAFÉ` não encontra o Café em grão. Explique por quê, a partir do que o material de PHP diz sobre `strtolower`.

**5.** Crie o `contato.php`, uma página com formulário que envia os dados para ela mesma, com método POST e três campos: nome, e-mail e mensagem (esta num `<textarea>`). No topo do arquivo, quando a requisição for POST, valide os dados no servidor:

* o nome não pode ficar vazio depois do `trim`;
* o e-mail precisa ser válido. `filter_var($email, FILTER_VALIDATE_EMAIL)` devolve `false` para um e-mail inválido;
* a mensagem precisa ter ao menos 10 caracteres, medidos com `strlen`.

Guarde cada erro num array `$erros`, com `$erros[] = "Informe o nome.";`, e mostre os erros numa lista `<ul>` acima do formulário. Quando houver erro, os campos devem continuar preenchidos com o que foi digitado. Quando não houver erro, mostre, no lugar do formulário, a mensagem `Obrigado, Ana! Recebemos sua mensagem.`, com o nome digitado. Confira três envios: o formulário vazio (três erros), o e-mail `ana@` (um erro, e o nome continua no campo) e todos os campos corretos.

**6.** No `contato.php`, acrescente o atributo `required` aos três campos e troque o campo do e-mail para `type="email"`. Confira que o navegador não envia mais o formulário vazio. Em seguida, abra as ferramentas do desenvolvedor (`F12`), apague o atributo `required` do campo do nome na aba de elementos e envie o formulário com o nome vazio. Confira que a validação do servidor, do exercício 5, continua mostrando o erro.

**7.** No `contato.php`, retire temporariamente o `htmlspecialchars` da mensagem de agradecimento e envie como nome `<script>alert('XSS')</script>`, com os demais campos corretos. Confira que a caixa de diálogo aparece. Devolva o `htmlspecialchars`, envie de novo e confira, com `Ctrl+U`, como o nome chegou ao navegador.

**8.** Troque temporariamente o método do formulário do `contato.php` para `get` e envie os dados. Observe a barra de endereços e responda:

* por que a mensagem de agradecimento não aparece, mesmo com todos os campos corretos?
* por que um formulário de contato ou de login usa o método POST?

Devolva o método `post` ao final.

**9.** Divida o HTML repetido das duas páginas em dois arquivos:

* `cabecalho.php`, com o `<!DOCTYPE html>`, o `<head>`, a abertura do `<body>` e um `<nav>` com links para `catalogo.php` e `contato.php`. O `<title>` mostra a variável `$titulo`: `<title><?= $titulo ?></title>`;
* `rodape.php`, com um `<footer>` e o fechamento do `<body>` e do `<html>`.

No `catalogo.php` e no `contato.php`, defina `$titulo` no bloco do topo e troque o começo e o fim do HTML por `require "cabecalho.php";` e `require "rodape.php";`. Confira que o título da aba muda de uma página para a outra e que um link novo acrescentado ao `<nav>` aparece nas duas. Por fim, troque no `contato.php` o nome do arquivo do cabeçalho por um que não existe, como `cabecalhox.php`, e compare a página com `require` e com `include`. Desfaça a troca ao final.

### Desafio

Acrescente ao formulário do catálogo um campo `<input type="number" name="max" min="0" step="0.01">` e mostre apenas os produtos cujo preço com desconto é menor ou igual ao valor informado. Valide o valor no servidor com `filter_var($_GET["max"], FILTER_VALIDATE_FLOAT)`, que devolve `false` quando o texto não é um número: nesse caso, ignore o filtro e mostre um aviso na página. Com o campo vazio, não aplique o filtro. Confira `?max=10` (só o Pão de mel) e `?max=abc`, digitado direto na barra de endereços, que o `type="number"` do formulário não impede. Teste também `?max=10,5` e explique o resultado.

---

## Próximos passos

Próxima aula, em 13/11: PHP e MySQL. Conexão com `mysqli`, *prepared statements* e operações CRUD.

A Entrega 3 do trabalho (PHP e MySQL) vence em 27/11, às 23h59. Não há entrega em atraso: vale o último *commit* enviado até o prazo.
