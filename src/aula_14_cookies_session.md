# PHP: Cookies, Sessões e Login

[Slides desta aula (PDF)](slides/aula_14_cookies_session.pdf)

## Objetivos

Ao final desta aula você deve ser capaz de:

1. Explicar por que o protocolo HTTP precisa de cookies e sessões, e onde cada um guarda os dados;
2. Criar, ler e remover um cookie em PHP;
3. Guardar e ler dados em `$_SESSION`, e encerrar uma sessão;
4. Gravar senhas com `password_hash` e conferi-las com `password_verify`;
5. Construir um login e proteger as páginas e os scripts de uma área administrativa.

## Bibliografia recomendada para o tema:

* [Documentação Oficial do PHP - Cookies](https://www.php.net/manual/pt_BR/features.cookies.php)
* [Documentação Oficial do PHP - Sessões](https://www.php.net/manual/pt_BR/book.session.php)
* [Sobre cookies no protocolo HTTP (Wikipedia)](https://pt.wikipedia.org/wiki/Cookie_HTTP)
* [w3schools - PHP Cookies](https://www.w3schools.com/php/php_cookies.asp)
* [w3schools - PHP Sessions](https://www.w3schools.com/php/php_sessions.asp)

## Código de exemplo

<https://gitlab.com/ds122-alexkutzke/ds122-login-app>

---

## Um contador de visitas

Crie o arquivo `contador.php` e inicie o servidor na pasta dele, com `php -d display_errors=1 -S localhost:8000`:

```php
<?php
session_start();
$_SESSION["visitas"] = ($_SESSION["visitas"] ?? 0) + 1;
?>
<p>Visitas nesta sessão: <?= $_SESSION["visitas"] ?></p>
```

Abra `http://localhost:8000/contador.php` e recarregue a página algumas vezes. O número aumenta, embora uma variável comum do PHP deixe de existir ao fim de cada requisição.

Com as ferramentas do desenvolvedor (`F12`) abertas:

* na aba de rede, a primeira resposta traz o cabeçalho `Set-Cookie: PHPSESSID=...`, e as requisições seguintes trazem `Cookie: PHPSESSID=...`;
* na aba de armazenamento (*Application*, no Chrome, ou *Storage*, no Firefox), o cookie `PHPSESSID` aparece com o mesmo valor;
* numa janela anônima, ou depois de apagar o cookie, o contador volta a 1.

O número de visitas não aparece no cookie: ele fica num arquivo do servidor, chamado `sess_` seguido do valor de `PHPSESSID`. No Linux, o servidor embutido do PHP o guarda em `/tmp`. O conteúdo, depois de duas visitas:

```text
visitas|i:2;
```

## Cookies e sessões

No material do [protocolo HTTP](aula_03_http.md#11-protocolo-sem-estado), vimos que o HTTP é **sem estado** (_stateless_): o servidor trata cada requisição de forma isolada. Para reconhecer um usuário, ele precisa que o cliente reenvie alguma informação em toda requisição.

| | Cookie | Sessão |
|---|---|---|
| Onde ficam os dados | No navegador | No servidor |
| O que trafega na requisição | Os próprios dados | Só o identificador da sessão, num cookie |
| O usuário pode alterar os dados | Sim | Não |
| Uso típico | Preferências, como o tema da página | Usuário autenticado, carrinho de compras |
| Em PHP | `setcookie()` e `$_COOKIE` | `session_start()` e `$_SESSION` |

---

## Cookies em PHP

A função `setcookie()` acrescenta o cabeçalho `Set-Cookie` à resposta. Os quatro primeiros parâmetros são o nome, o valor, o instante em que o cookie expira e o caminho do site em que ele vale.

Criar, em `criar_cookie.php`:

```php
<?php
// Válido por 30 dias, em todo o site
setcookie("tema", "escuro", time() + 60 * 60 * 24 * 30, "/");
?>
<p>Cookie enviado. <a href="ler_cookie.php">Ler</a></p>
```

Ler, em `ler_cookie.php`:

```php
<?php
$tema = $_COOKIE["tema"] ?? "claro";
?>
<p>Tema escolhido: <?= htmlspecialchars($tema) ?></p>
```

Remover, em `remover_cookie.php`, com uma data de expiração no passado e o mesmo caminho usado na criação:

```php
<?php
setcookie("tema", "", time() - 3600, "/");
```

Ao usar `setcookie()`:

* **`setcookie()` vem antes de qualquer saída HTML**, porque o cookie é enviado nos cabeçalhos da resposta, e os cabeçalhos vêm antes do corpo;
* o cookie só aparece em `$_COOKIE` na **próxima** requisição, quando o navegador o envia de volta;
* sem o instante de expiração, o cookie vale até o navegador ser fechado.

### O usuário pode alterar o cookie

No `F12`, troque o valor do cookie `tema` por `<b>azul</b>` e recarregue `ler_cookie.php`. O servidor recebe o valor alterado, e o `htmlspecialchars` impede que ele vire HTML. Um cookie é um dado enviado pelo usuário, como um campo de formulário.

Por isso, um cookie como `admin=1` não protege nada: qualquer visitante pode criá-lo. A informação de quem está autenticado fica na sessão.

---

## Sessões em PHP

`session_start()` procura o cookie `PHPSESSID` na requisição. Se ele existir, o PHP carrega os dados daquela sessão em `$_SESSION`; se não existir, cria uma sessão nova e envia o cookie. Toda página que usa `$_SESSION` chama `session_start()`, uma vez, antes de qualquer saída HTML.

```php
<?php
session_start();

// Gravar
$_SESSION["usuario_nome"] = "Maria Silva";

// Ler, em qualquer página que chame session_start()
echo htmlspecialchars($_SESSION["usuario_nome"] ?? "visitante");
```

### Opções do cookie da sessão

O identificador da sessão precisa ser protegido: com uma cópia dele, outra pessoa acessa o site como se fosse o usuário. Duas opções do cookie ajudam:

| Opção | Efeito |
|---|---|
| `cookie_httponly` | O JavaScript da página não lê o cookie. Um ataque de XSS não consegue copiar o identificador com `document.cookie`. |
| `cookie_samesite` com `"Lax"` | O navegador não envia o cookie num formulário POST que outro site submeta para o seu. |

Num site publicado, com HTTPS, acrescenta-se `cookie_secure`, que impede o envio do cookie por HTTP.

Para não repetir as opções em cada página, o `session_start()` fica num arquivo próprio, incluído com `require_once`.

`admin/sessao.php`:

```php
<?php
session_start([
    "cookie_httponly" => true,
    "cookie_samesite" => "Lax",
]);
```

---

## Senhas: `password_hash` e `password_verify`

O banco de dados não guarda a senha do usuário. Ele guarda um **resumo criptográfico** (_hash_) da senha, do qual não se recupera o texto original. Se o banco vazar, as senhas não vazam com ele.

```php
<?php
echo password_hash("123456", PASSWORD_DEFAULT), "<br>";
echo password_hash("123456", PASSWORD_DEFAULT), "<br>";
```

Uma saída possível. O começo muda conforme a versão do PHP, e o restante muda a cada execução:

```text
$2y$10$q6WA7ddvt4RPkToscD5YPO6OjWIV1oNZaxPvxCrstTStCIi9DXavq
$2y$10$zITCq58z8tNBapehdpRRwuHmEYOwBC5ivKRBfLw/vYF71oULWke9W
```

A mesma senha produz dois hashes diferentes, porque `password_hash` mistura à senha um valor aleatório, chamado _salt_, e o guarda dentro do próprio hash. Por isso, a comparação com `===` não funciona. A função `password_verify($senha, $hash)` lê o _salt_ do hash e devolve `true` quando a senha confere.

* `PASSWORD_DEFAULT` usa, hoje, o algoritmo bcrypt. Como o padrão pode mudar, a coluna da senha é `VARCHAR(255)`;
* não use `md5()` nem `sha1()` para senhas: são rápidas de calcular, e isso facilita testar milhões de senhas por segundo.

---

## Sistema de login

A estrutura segue a que a Entrega 3 pede, com a área administrativa na pasta `admin/`:

```text
ds122-login-app/
├── config.php               dados de conexão
├── conexao.php              conexão com o MySQL
├── criar_bd.php             cria a base de dados
├── criar_tabela.php         cria a tabela de usuários
├── cadastrar.php            cadastra o primeiro usuário
├── index.php                página pública
└── admin/
    ├── sessao.php           inicia a sessão
    ├── verifica_login.php   barra quem não está autenticado
    ├── login.php            formulário e processamento do login
    ├── dashboard.php        página protegida
    └── logout.php           encerra a sessão
```

### Conexão

`config.php`:

```php
<?php
// Ajuste para o seu ambiente. No XAMPP, sem usuário próprio: "root" e senha vazia.
// Não envie senhas reais para um repositório público.
define("DB_HOST", "localhost");
define("DB_USER", "appuser");
define("DB_PASS", "web1");
define("DB_NAME", "minha_aplicacao");
```

`conexao.php`:

```php
<?php
require_once __DIR__ . "/config.php";

try {
    $conn = mysqli_connect(DB_HOST, DB_USER, DB_PASS, DB_NAME);
    mysqli_set_charset($conn, "utf8mb4");
} catch (mysqli_sql_exception $e) {
    // Em produção, mostre uma mensagem genérica, sem detalhes do banco
    die("Erro de conexão: " . $e->getMessage());
}
```

**`try` e `catch`.** Desde o PHP 8.1, um erro do `mysqli` lança uma exceção (_exception_), e o script para com `Fatal error` se ninguém a tratar. O `if (mysqli_connect_error())` do material de PHP e MySQL não chega a ser executado quando a conexão falha. O `try` e o `catch` funcionam como no `fetch`: o erro desvia a execução para o `catch`.

**Sem `?>` no final.** Num arquivo que só tem PHP, omita a tag de fechamento. Linhas em branco depois do `?>` fazem parte da saída HTML, e no `config.php` elas seriam enviadas antes dos cabeçalhos. O login para de funcionar, com o aviso `headers already sent`.

### Tabela e primeiro usuário

`criar_tabela.php`:

```php
<?php
require_once __DIR__ . "/conexao.php";

$sql = "CREATE TABLE IF NOT EXISTS usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    senha VARCHAR(255) NOT NULL,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4";

try {
    mysqli_query($conn, $sql);
    echo "Tabela 'usuarios' pronta!";
} catch (mysqli_sql_exception $e) {
    echo "Erro ao criar a tabela: " . $e->getMessage();
}
```

`cadastrar.php`:

```php
<?php
require_once __DIR__ . "/conexao.php";

$nome  = "Admin";
$email = "admin@exemplo.com";
$senha = "123456";

// O banco guarda o hash, nunca a senha
$hash = password_hash($senha, PASSWORD_DEFAULT);

try {
    $stmt = mysqli_prepare($conn, "INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)");
    mysqli_stmt_bind_param($stmt, "sss", $nome, $email, $hash);
    mysqli_stmt_execute($stmt);
    echo "Usuário cadastrado com sucesso!";
} catch (mysqli_sql_exception $e) {
    // 1062 é o código do MySQL para valor repetido em coluna UNIQUE
    if ($e->getCode() === 1062) {
        echo "Já existe um usuário com este e-mail.";
    } else {
        echo "Erro ao cadastrar: " . $e->getMessage();
    }
}
```

A coluna `email` é `UNIQUE`. Ao executar o `cadastrar.php` pela segunda vez, o MySQL recusa o e-mail repetido, e o `catch` mostra uma mensagem no lugar do erro fatal.

O cadastro não é um formulário público, porque qualquer visitante poderia criar um usuário e entrar na área administrativa. Depois de executá-lo, remova o arquivo do servidor.

### Login

`admin/login.php`:

```php
<?php
require_once __DIR__ . "/sessao.php";

// Quem já está autenticado vai direto para o painel
if (isset($_SESSION["usuario_id"])) {
    header("Location: dashboard.php");
    exit;
}

$erro = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // O conexao.php está na pasta de cima
    require_once __DIR__ . "/../conexao.php";

    $email = trim($_POST["email"] ?? "");
    $senha = $_POST["senha"] ?? "";

    // 1. Busca o usuário pelo e-mail
    $stmt = mysqli_prepare($conn, "SELECT id, nome, senha FROM usuarios WHERE email = ?");
    mysqli_stmt_bind_param($stmt, "s", $email);
    mysqli_stmt_execute($stmt);
    $resultado = mysqli_stmt_get_result($stmt);
    $usuario = mysqli_fetch_assoc($resultado); // null quando o e-mail não existe

    // 2. Confere a senha digitada com o hash guardado no banco
    if ($usuario && password_verify($senha, $usuario["senha"])) {
        // 3. Troca o identificador e grava o usuário na sessão
        session_regenerate_id(true);
        $_SESSION["usuario_id"]   = $usuario["id"];
        $_SESSION["usuario_nome"] = $usuario["nome"];

        header("Location: dashboard.php");
        exit;
    }

    $erro = "E-mail ou senha inválidos.";
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h1>Login</h1>

    <?php if ($erro): ?>
        <p style="color: red;"><?= htmlspecialchars($erro) ?></p>
    <?php endif; ?>

    <form method="post" action="login.php">
        <label for="email">E-mail:</label><br>
        <input type="email" id="email" name="email" required><br><br>

        <label for="senha">Senha:</label><br>
        <input type="password" id="senha" name="senha" required><br><br>

        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

* O `__DIR__` é a pasta do próprio arquivo, `admin/`. O `"/../conexao.php"` sobe um nível para achar a conexão;
* a mensagem de erro é a mesma para e-mail inexistente e senha errada, e não revela quais e-mails estão cadastrados;
* `session_regenerate_id(true)` troca o identificador da sessão no momento do login. A seção sobre fixação de sessão, adiante, mostra por quê.

### Páginas protegidas

`admin/verifica_login.php`:

```php
<?php
require_once __DIR__ . "/sessao.php";

if (!isset($_SESSION["usuario_id"])) {
    header("Location: login.php");
    exit;
}
```

`admin/dashboard.php`:

```php
<?php
require_once __DIR__ . "/verifica_login.php";
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Painel</title>
</head>
<body>
    <h1>Painel</h1>
    <p>Olá, <strong><?= htmlspecialchars($_SESSION["usuario_nome"]) ?></strong>!</p>
    <p>Esta página só aparece para usuários autenticados.</p>

    <p><a href="logout.php">Sair</a></p>
</body>
</html>
```

A verificação é a **primeira linha** de cada arquivo protegido, antes de qualquer HTML.

### O `exit` depois do redirecionamento

`header("Location: login.php")` apenas acrescenta um cabeçalho à resposta. Sem o `exit`, o script continua e envia a página protegida inteira. O navegador segue o redirecionamento e não mostra o conteúdo, mas ele foi enviado.

Para ver, apague temporariamente o `exit` do `verifica_login.php` e peça a página sem estar autenticado. Ajuste o endereço ao do seu servidor:

```bash
curl -i http://localhost:8000/admin/dashboard.php
```

A resposta tem status 302 e, no corpo, o HTML do painel. Com o `exit`, o corpo vem vazio.

### Scripts que alteram dados também precisam da verificação

Esconder o link ou o formulário não protege o script que recebe os dados. Quem souber o endereço pode enviar a requisição direto, com o `curl` ou pelo console do navegador. Todo arquivo da pasta `admin/` começa com a verificação, inclusive os que não mostram nada:

```php
<?php
// admin/excluir.php, admin/salvar.php e os demais
require_once __DIR__ . "/verifica_login.php";
require_once __DIR__ . "/../conexao.php";

// só então lê $_POST ou $_GET e altera o banco
```

As exceções são `sessao.php`, `login.php` e `logout.php`.

### Logout

`admin/logout.php`:

```php
<?php
require_once __DIR__ . "/sessao.php";

// 1. Apaga os dados da sessão
$_SESSION = [];

// 2. Remove o cookie do navegador
setcookie(session_name(), "", time() - 3600, "/");

// 3. Destrói a sessão no servidor
session_destroy();

header("Location: login.php");
exit;
```

### Fixação de sessão

Com o `contador.php` do início da aula, envie um identificador inventado:

```bash
curl -b "PHPSESSID=abc123" http://localhost:8000/contador.php
curl -b "PHPSESSID=abc123" http://localhost:8000/contador.php
```

A saída mostra 1 e, depois, 2: na configuração padrão, o PHP aceita um identificador escolhido pelo cliente. Se um atacante conseguir que a vítima use um identificador que ele conhece, e esse identificador continuar valendo após o login, o atacante entra na conta. O ataque se chama **fixação de sessão** (_session fixation_). Como `session_regenerate_id(true)` cria um identificador novo no login e apaga o antigo, o valor conhecido pelo atacante deixa de servir.

### Fluxo completo

1. O visitante pede `admin/dashboard.php`. O `verifica_login.php` não encontra `usuario_id` na sessão e redireciona para o login.
2. O formulário envia e-mail e senha por POST ao `login.php`.
3. Um *prepared statement* busca o usuário pelo e-mail, e `password_verify` confere a senha.
4. Com a senha correta, o identificador da sessão é trocado, `usuario_id` é gravado em `$_SESSION`, e o navegador é redirecionado ao painel.
5. As demais páginas da pasta `admin/` encontram `usuario_id` e são exibidas.
6. O logout apaga os dados, remove o cookie e destrói a sessão.

---

## Do exemplo para a Entrega 3

O restante da aula é dedicado ao trabalho. Para levar o login ao seu projeto:

1. Crie a tabela `usuarios` e inclua o comando no arquivo `.sql` do repositório.
2. Use os arquivos da pasta `admin/` do exemplo como referência e adapte-os à sua conexão e aos seus nomes.
3. Coloque o `require_once` do `verifica_login.php` na primeira linha da listagem, do cadastro, da edição, da exclusão e da página de mensagens.
4. Teste numa janela anônima, sem login: digite o endereço de cada arquivo da pasta `admin/` e confira que todos levam ao login.
5. Teste a senha errada, o e-mail inexistente e o logout.

Saiba explicar cada linha que usar: a Prova 3 poderá ter uma questão sobre o seu próprio projeto.

## Próximos passos

A Entrega 3 do trabalho (PHP e MySQL) vence em 29/11 (domingo), às 23h59. Não há entrega em atraso: vale o último *commit* enviado até o prazo.

Próxima aula, em 04/12: Prova 3, sobre PHP e MySQL (`mysqli`, CRUD, sessões e login), e encerramento da disciplina.
