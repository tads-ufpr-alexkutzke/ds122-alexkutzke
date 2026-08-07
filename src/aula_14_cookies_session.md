# PHP: Cookies, Sessions e Login

## Bibliografia recomendada para o tema:

* [Documentação Oficial do PHP — Cookies](https://www.php.net/manual/pt_BR/features.cookies.php)
* [Documentação Oficial do PHP — Sessões](https://www.php.net/manual/pt_BR/book.session.php)
* [Sobre cookies no protocolo HTTP (Wikipedia)](https://pt.wikipedia.org/wiki/Cookie_HTTP)
* [w3schools — PHP Cookies](https://www.w3schools.com/php/php_cookies.asp)
* [w3schools — PHP Sessions](https://www.w3schools.com/php/php_sessions.asp)

---

## Por que precisamos de Cookies e Sessões?

O protocolo HTTP é **stateless** (sem estado): cada requisição é independente — o servidor não sabe se duas requisições consecutivas vieram do mesmo navegador. Isso funciona bem para páginas estáticas, mas não para aplicações que precisam lembrar quem é o usuário.

Cookies e Sessões resolvem esse problema, permitindo que o servidor **mantenha estado** entre requisições:

- **Cookies:** pequenos arquivos de texto armazenados no **navegador** do cliente.
- **Sessões:** dados armazenados no **servidor**, indexados por um identificador único (Session ID) que trafega como cookie.

---

## Cookies no Protocolo HTTP

Do site [Wikipedia — Cookie HTTP](https://pt.wikipedia.org/wiki/Cookie_HTTP):

> Um cookie é um pequeno pacote de dados enviados de um website para o navegador do usuário quando o usuário visita o site. Cada vez que o usuário visita o site novamente, o navegador envia o cookie de volta para o servidor para notificar atividades prévias do usuário. Os cookies foram designados para ser um mecanismo confiável para que sites se lembrem de informações da atividade do usuário, como senhas gravadas, itens adicionados no carrinho de compras em uma loja online, links que foram clicados anteriormente, entre outros.

### Cookies no PHP — `setcookie()`

A criação de um cookie em PHP utiliza a função `setcookie()`, que recebe os seguintes parâmetros:

```php
setcookie(
    string $name,       // Nome do cookie (obrigatório)
    string $value,      // Valor a armazenar
    int    $expires,    // Timestamp de expiração (padrão: 0 = expira ao fechar o navegador)
    string $path,       // Caminho no site onde o cookie é válido (padrão: diretório atual)
    string $domain,     // Domínio onde o cookie é válido
    bool   $secure,     // Se true, só envia por HTTPS
    bool   $httponly     // Se true, inacessível via JavaScript (protege contra XSS)
);
```

Apenas o parâmetro `$name` é obrigatório.

#### Criando um cookie

```php
<?php
// Cookie "usuario" com valor "João", válido por 30 dias, disponível em todo o site
setcookie("usuario", "João", time() + (86400 * 30), "/");
?>
```

> **Importante:** `setcookie()` deve ser chamada **antes de qualquer saída HTML**, pois o cookie é enviado nos cabeçalhos da resposta HTTP.

#### Lendo um cookie — `$_COOKIE`

O array superglobal `$_COOKIE` contém os cookies enviados pelo navegador na requisição atual.

**Atenção:** um cookie definido com `setcookie()` **não estará imediatamente disponível** em `$_COOKIE` na mesma requisição — ele só aparecerá na **próxima** requisição, quando o navegador o reenviar.

```php
<?php
// Página 1: definir_cookie.php
setcookie("usuario", "João", time() + (86400 * 30), "/");
?>
<!DOCTYPE html>
<html><body>
  <p>Cookie definido! <a href="ler_cookie.php">Ver cookie</a></p>
</body></html>
```

```php
<?php
// Página 2: ler_cookie.php — acessada APÓS definir o cookie
?>
<!DOCTYPE html>
<html><body>
<?php if (isset($_COOKIE["usuario"])): ?>
  <p>Bem-vindo de volta, <?= htmlspecialchars($_COOKIE["usuario"]) ?>!</p>
<?php else: ?>
  <p>Cookie não encontrado.</p>
<?php endif; ?>
</body></html>
```

#### Alterando e removendo um cookie

Para **alterar** o valor, basta chamar `setcookie()` novamente com o mesmo nome e novo valor.

Para **remover**, defina uma data de expiração no passado:

```php
// Remove o cookie "usuario"
setcookie("usuario", "", time() - 3600, "/");
```

### Parâmetros de segurança

| Parâmetro   | O que faz |
|-------------|-----------|
| `secure`    | O cookie só é enviado em conexões HTTPS. Essencial em produção. |
| `httponly`  | Impede que JavaScript acesse o cookie (`document.cookie`), mitigando roubo via XSS. |
| `SameSite`  | Controla se o cookie é enviado em requisições cross-site. Use `Lax` (padrão seguro) ou `Strict`. |

**Exemplo de cookie seguro:**

```php
// Cookie com flags de segurança ativas (PHP 7.3+ para SameSite no array de opções)
setcookie("usuario", "João", [
    "expires"  => time() + (86400 * 30),
    "path"     => "/",
    "secure"   => true,   // Apenas HTTPS
    "httponly" => true,   // Inacessível via JavaScript
    "samesite" => "Lax",  // Proteção contra CSRF
]);
```

> Para ambiente de desenvolvimento local (`localhost` sem HTTPS), mantenha `secure` como `false`.

---

## Sessões

Enquanto cookies armazenam dados no **navegador** (visíveis e modificáveis pelo usuário), sessões armazenam dados no **servidor**. O navegador guarda apenas um identificador (Session ID) em um cookie.

### Fluxo de uma sessão

1. O PHP gera um **Session ID** único.
2. O Session ID é enviado ao navegador como cookie (`PHPSESSID`).
3. Os dados da sessão ficam armazenados no servidor (arquivo ou banco).
4. A cada requisição, o navegador reenvia o Session ID, e o PHP recupera os dados correspondentes.

### Iniciando uma sessão — `session_start()`

```php
<?php
// Sempre no topo do arquivo, antes de qualquer saída HTML
session_start();
?>
```

> Use `session_status()` para verificar se já existe uma sessão ativa e evitar chamar `session_start()` duas vezes:

```php
if (session_status() === PHP_SESSION_NONE) {
    session_start();
}
```

### Gravando e lendo dados da sessão — `$_SESSION`

O array superglobal `$_SESSION` funciona como um array associativo comum — você grava e lê valores livremente:

```php
<?php
session_start();

// Gravando dados (ex: após login bem-sucedido)
$_SESSION["usuario_id"]    = 42;
$_SESSION["usuario_nome"]  = "Maria Silva";
$_SESSION["logado_em"]     = time();
?>
```

```php
<?php
// Lendo dados em qualquer outra página
session_start();

if (isset($_SESSION["usuario_id"])) {
    echo "Bem-vinda, " . htmlspecialchars($_SESSION["usuario_nome"]) . "!";
} else {
    echo "Você não está logado.";
}
?>
```

### Encerrando uma sessão (logout)

```php
<?php
session_start();

// 1. Limpa todas as variáveis da sessão
$_SESSION = [];

// 2. Remove o cookie de sessão do navegador
if (ini_get("session.use_cookies")) {
    $params = session_get_cookie_params();
    setcookie(session_name(), "", time() - 3600,
        $params["path"], $params["domain"],
        $params["secure"], $params["httponly"]
    );
}

// 3. Destrói a sessão no servidor
session_destroy();

// 4. Redireciona para a página de login
header("Location: login.php");
exit;
?>
```

### Segurança: `session_regenerate_id()`

Após o login, é boa prática **regenerar o Session ID** para evitar *Session Fixation* (quando um atacante força o uso de um ID conhecido):

```php
session_regenerate_id(true); // true = apaga o arquivo de sessão antigo
```

---

## Sistema de Login com Sessões e Banco de Dados

Abaixo, um exemplo completo de sistema de login que utiliza **mysqli + Prepared Statements** e sessões PHP.

### Estrutura de arquivos

```
/
├── config.php          # Dados de conexão
├── conexao.php         # Conexão com MySQL
├── criar_tabela.php    # Script para criar a tabela de usuários
├── cadastrar.php       # Cadastro de usuário (com hash de senha)
├── login.php           # Formulário + processamento de login
├── dashboard.php       # Página protegida (só acessa logado)
└── logout.php          # Encerra a sessão
```

### `config.php` — Dados de conexão

```php
<?php
// Arquivo de configuração — não commitar credenciais reais em repositórios públicos
define("DB_HOST", "localhost");
define("DB_USER", "appuser");
define("DB_PASS", "web1");
define("DB_NAME", "minha_aplicacao");
?>
```

### `conexao.php` — Conexão com o banco

```php
<?php
require_once __DIR__ . "/config.php";

$conn = mysqli_connect(DB_HOST, DB_USER, DB_PASS, DB_NAME);

if (mysqli_connect_error()) {
    die("Erro de conexão: " . mysqli_connect_error());
}

mysqli_set_charset($conn, "utf8mb4");
?>
```

### `criar_tabela.php` — Tabela de usuários

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

mysqli_query($conn, $sql);
echo "Tabela 'usuarios' pronta!";
mysqli_close($conn);
?>
```

### `cadastrar.php` — Cadastro com senha segura

```php
<?php
require_once __DIR__ . "/conexao.php";

$nome  = "Admin";
$email = "admin@exemplo.com";
$senha_plana = "123456";

// NUNCA armazene a senha em texto plano — use password_hash()
$senha_hash = password_hash($senha_plana, PASSWORD_DEFAULT);

$stmt = mysqli_prepare($conn, "INSERT INTO usuarios (nome, email, senha) VALUES (?, ?, ?)");
mysqli_stmt_bind_param($stmt, "sss", $nome, $email, $senha_hash);

if (mysqli_stmt_execute($stmt)) {
    echo "Usuário cadastrado com sucesso!";
} else {
    echo "Erro: " . mysqli_stmt_error($stmt);
}

mysqli_stmt_close($stmt);
mysqli_close($conn);
?>
```

> **`password_hash()`**: função nativa do PHP que gera um hash criptográfico seguro usando o algoritmo bcrypt (ou Argon2, se disponível). **Nunca** utilize `md5()` ou `sha1()` para senhas — são facilmente quebráveis. Para verificar a senha, utilize `password_verify()`.

### `login.php` — Formulário e processamento

```php
<?php
session_start();

// Se já estiver logado, redireciona
if (isset($_SESSION["usuario_id"])) {
    header("Location: dashboard.php");
    exit;
}

$erro = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    require_once __DIR__ . "/conexao.php";

    $email = $_POST["email"] ?? "";
    $senha = $_POST["senha"] ?? "";

    if (empty($email) || empty($senha)) {
        $erro = "Preencha todos os campos.";
    } else {
        // Prepared Statement para buscar o usuário pelo email
        $stmt = mysqli_prepare($conn, "SELECT id, nome, senha FROM usuarios WHERE email = ?");
        mysqli_stmt_bind_param($stmt, "s", $email);
        mysqli_stmt_execute($stmt);
        $result = mysqli_stmt_get_result($stmt);

        if ($usuario = mysqli_fetch_assoc($result)) {
            // Verifica a senha com password_verify()
            if (password_verify($senha, $usuario["senha"])) {
                // Login bem-sucedido → regenera ID e grava sessão
                session_regenerate_id(true);
                $_SESSION["usuario_id"]   = $usuario["id"];
                $_SESSION["usuario_nome"] = $usuario["nome"];

                mysqli_stmt_close($stmt);
                mysqli_close($conn);

                header("Location: dashboard.php");
                exit;
            }
        }

        // Login falhou (email não encontrado OU senha incorreta)
        $erro = "Email ou senha inválidos.";
        mysqli_stmt_close($stmt);
        mysqli_close($conn);
    }
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
        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email" required><br><br>

        <label for="senha">Senha:</label><br>
        <input type="password" id="senha" name="senha" required><br><br>

        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

### `dashboard.php` — Página protegida

```php
<?php
session_start();

// Proteção: se não estiver logado, redireciona para o login
if (!isset($_SESSION["usuario_id"])) {
    header("Location: login.php");
    exit;
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Painel</title>
</head>
<body>
    <h1>Painel do Usuário</h1>
    <p>Bem-vindo(a), <strong><?= htmlspecialchars($_SESSION["usuario_nome"]) ?></strong>!</p>
    <p>Esta página só pode ser acessada por usuários autenticados.</p>

    <p><a href="logout.php">Sair</a></p>
</body>
</html>
```

### `logout.php` — Encerrando a sessão

```php
<?php
session_start();

// Limpa variáveis da sessão
$_SESSION = [];

// Remove o cookie PHPSESSID do navegador
if (ini_get("session.use_cookies")) {
    $params = session_get_cookie_params();
    setcookie(session_name(), "", time() - 3600,
        $params["path"], $params["domain"],
        $params["secure"], $params["httponly"]
    );
}

// Destrói a sessão no servidor
session_destroy();

// Redireciona para o login
header("Location: login.php");
exit;
?>
```

### Fluxo completo

1. O usuário acessa `login.php` e preenche email/senha.
2. O PHP busca o email no banco com **Prepared Statement** (protegido contra SQL Injection).
3. `password_verify()` compara a senha digitada com o hash armazenado.
4. Se válido: `session_regenerate_id()` + grava dados em `$_SESSION` + redireciona para `dashboard.php`.
5. Toda página protegida verifica `$_SESSION["usuario_id"]` e redireciona para `login.php` se ausente.
6. O logout limpa `$_SESSION`, remove o cookie e destrói a sessão.

---

## Referência completa do exemplo

O código completo deste sistema de login está disponível em:

<https://gitlab.com/ds122-alexkutzke/ds122-login-app>
