# PHP: Manipulação de Bancos de Dados

## Bibliografia recomendada para o tema:

* [Documentação Oficial do PHP - Extensão Mysqli](https://www.php.net/manual/pt_BR/book.mysqli.php)
* [w3schools - PHP+MySQL](https://www.w3schools.com/php/php_mysql_intro.asp)
* [PHP The Right Way - Bancos de Dados](https://phptherightway.com/#databases)

---

## Acesso ao MySQL

Para acessar o banco de dados MySQL a partir de um script PHP, temos as seguintes opções:

1. Biblioteca (ou extensão) `mysqli` no formato procedural;
2. Biblioteca (ou extensão) `mysqli` no formato orientado a objetos;
3. Biblioteca `PDO` (PHP Data Objects).

Durante a disciplina, utilizaremos a **mysqli procedural**, pois não depende de conceitos de orientação a objetos — que serão estudados em disciplinas futuras. O foco aqui está na lógica de manipulação de dados.

> **Nota sobre PDO:** O PDO é a recomendação moderna da comunidade PHP. Ele oferece suporte a **12 bancos de dados** diferentes (MySQL, PostgreSQL, SQLite, etc.) e utiliza *Prepared Statements* com *bind* de parâmetros nomeados, oferecendo maior segurança contra SQL Injection. Quando você avançar em Programação Orientada a Objetos, migrar de mysqli procedural para PDO será um passo natural. Uma introdução ao PDO é apresentada ao final desta aula.

---

## Conexão com o Banco de Dados

Antes de qualquer operação, é necessário estabelecer uma conexão com o servidor MySQL. A função `mysqli_connect()` recebe os parâmetros de acesso e retorna um *link* de conexão.

**Boas práticas:**
- Armazene os dados de conexão (host, usuário, senha, nome do banco) em **variáveis** ou em um arquivo de configuração separado. Assim, se os dados mudarem, você altera em um único lugar.
- Utilize `mysqli_connect_error()` para verificar se a conexão foi bem-sucedida.
- É possível definir o charset da conexão com `mysqli_set_charset()` para `utf8mb4`, que oferece suporte completo a caracteres especiais e emojis.

```php
<?php
// Dados de conexão — em um projeto real, guarde em arquivo separado (ex: config.php)
$host   = "localhost";
$user   = "appuser";
$pass   = "web1";
$dbname = "minha_aplicacao";

// Estabelece a conexão
$conn = mysqli_connect($host, $user, $pass, $dbname);

// Verifica se houve erro
if (mysqli_connect_error()) {
    die("Erro de conexão: " . mysqli_connect_error());
}

// Define o charset para utf8mb4 (suporte a acentos e emojis)
mysqli_set_charset($conn, "utf8mb4");

echo "Conectado com sucesso!";
?>
```

> Em projetos maiores, isole a conexão em um arquivo `conexao.php` e utilize `require 'conexao.php';` nos demais scripts. Assim você evita repetir o código de conexão em cada página.

---

## Criando um Banco de Dados

A criação do banco pode ser feita via phpMyAdmin (XAMPP) ou diretamente pelo terminal. Para criar via PHP, utilizamos `mysqli_query()`:

```php
<?php
$host   = "localhost";
$user   = "appuser";
$pass   = "web1";

// Estabelece a conexão sem uma base de dados
$conn = mysqli_connect($host, $user, $pass);

// Verifica se houve erro
if (mysqli_connect_error()) {
    die("Erro de conexão: " . mysqli_connect_error());
}

$sql = "CREATE DATABASE IF NOT EXISTS minha_aplicacao CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci";

if (mysqli_query($conn, $sql)) {
    echo "Banco de dados criado com sucesso!";
} else {
    echo "Erro ao criar banco: " . mysqli_error($conn);
}

mysqli_close($conn);
?>
```

---

## Criando um arquivo de credenciais

```php
<?php
$host   = "mysql";
$user   = "usuario";
$pass   = "senha";
$dbname = "minha_aplicacao";
?>
```

---

## Criando uma Tabela

Após selecionar o banco, criamos as tabelas necessárias. Abaixo, um exemplo de tabela `tarefas` que será usada nos exemplos seguintes:

```php
<?php
require 'credentials.php';

$conn = mysqli_connect($host, $user, $pass, $dbname);

if (mysqli_connect_error()) {
    die("Erro de conexão: " . mysqli_connect_error());
}

$sql = "CREATE TABLE IF NOT EXISTS tarefas (
    id INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(255) NOT NULL,
    descricao TEXT,
    concluida TINYINT(1) DEFAULT 0,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4";

if (mysqli_query($conn, $sql)) {
    echo "Tabela 'tarefas' criada com sucesso!";
} else {
    echo "Erro ao criar tabela: " . mysqli_error($conn);
}

mysqli_close($conn);
```

---

## CRUD — Operações Fundamentais

CRUD é o acrônimo para as quatro operações básicas de persistência de dados:

| Operação | SQL    | Descrição                |
|----------|--------|--------------------------|
| Create   | INSERT | Inserir um novo registro |
| Read     | SELECT | Consultar registros      |
| Update   | UPDATE | Atualizar um registro    |
| Delete   | DELETE | Remover um registro      |

---

### Criando arquivo de conexão genérico

```php
<?php
require_once 'credentials.php';

// Estabelece a conexão
$conn = mysqli_connect($host, $user, $pass, $dbname);

// Verifica se houve erro
if (mysqli_connect_error()) {
    die("Erro de conexão: " . mysqli_connect_error());
}
?>
```

---

### Create — Inserindo Dados (INSERT)

Para inserir registros, utiliza-se `mysqli_query()` com um comando `INSERT INTO`.

```php
<?php
require 'conexao.php';

$titulo    = "Estudar PHP";
$descricao = "Revisar os conceitos de manipulação de arrays e strings";

// Monta a query — ATENÇÃO: esta forma é vulnerável a SQL Injection (veja seção de segurança)
$sql = "INSERT INTO tarefas (titulo, descricao) VALUES ('$titulo', '$descricao')";

if (mysqli_query($conn, $sql)) {
    echo "Tarefa inserida com sucesso! ID: " . mysqli_insert_id($conn);
} else {
    echo "Erro ao inserir: " . mysqli_error($conn);
}

mysqli_close($conn);
?>
```

> `mysqli_insert_id()` retorna o ID auto-incremento gerado na última inserção — útil quando a tabela possui chave primária automática.

#### Inserção com Prepared Statements (Forma Segura)

*Prepared Statements* (consultas preparadas) separam a estrutura da query dos valores fornecidos pelo usuário, **eliminando o risco de SQL Injection**.

```php
<?php
require 'conexao.php';

$titulo    = "Estudar PHP";
$descricao = "Revisar os conceitos de manipulação de arrays e strings";

// 1. Prepara a query com placeholders ?
$stmt = mysqli_prepare($conn, "INSERT INTO tarefas (titulo, descricao) VALUES (?, ?)");

// 2. Associa os parâmetros — 's' = string, 'i' = integer, 'd' = double, 'b' = blob
mysqli_stmt_bind_param($stmt, "ss", $titulo, $descricao);

// 3. Executa
if (mysqli_stmt_execute($stmt)) {
    echo "Tarefa inserida com sucesso! ID: " . mysqli_stmt_insert_id($stmt);
} else {
    echo "Erro ao inserir: " . mysqli_stmt_error($stmt);
}

mysqli_stmt_close($stmt);
mysqli_close($conn);
?>
```

> Sempre que os valores da query vierem de fontes externas (formulários, URLs, APIs), utilize Prepared Statements. É a principal defesa contra SQL Injection.

---

### Read — Consultando Dados (SELECT)

Para consultar registros, executa-se um `SELECT` e recupera-se os resultados com `mysqli_fetch_assoc()`.

#### Listar todos os registros

```php
<?php
require 'conexao.php';

$sql = "SELECT id, titulo, descricao, concluida, data_criacao FROM tarefas ORDER BY data_criacao DESC";
$result = mysqli_query($conn, $sql);

// Verifica se há registros
if (mysqli_num_rows($result) > 0) {
    while ($row = mysqli_fetch_assoc($result)) {
        echo "<strong>#{$row['id']} — {$row['titulo']}</strong><br>";
        echo "{$row['descricao']}<br>";
        echo "Concluída: " . ($row['concluida'] ? 'Sim' : 'Não') . "<br>";
        echo "<hr>";
    }
} else {
    echo "Nenhuma tarefa encontrada.";
}

mysqli_free_result($result);
mysqli_close($conn);
?>
```

#### Exibindo os dados em uma tabela HTML

É muito comum combinar PHP e HTML para exibir dados em formato tabular:

```php
<?php require 'conexao.php'; ?>

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Minhas Tarefas</title>
    <style>
        table { border-collapse: collapse; width: 100%; }
        th, td { border: 1px solid #ccc; padding: 8px; text-align: left; }
        th { background-color: #f0f0f0; }
    </style>
</head>
<body>
    <h1>Lista de Tarefas</h1>

    <table>
        <tr>
            <th>ID</th>
            <th>Título</th>
            <th>Descrição</th>
            <th>Concluída</th>
            <th>Data</th>
        </tr>

        <?php
        $sql = "SELECT * FROM tarefas ORDER BY id ASC";
        $result = mysqli_query($conn, $sql);

        if (mysqli_num_rows($result) > 0):
            while ($row = mysqli_fetch_assoc($result)):
        ?>
            <tr>
                <td><?= $row['id'] ?></td>
                <td><?= htmlspecialchars($row['titulo']) ?></td>
                <td><?= htmlspecialchars($row['descricao']) ?></td>
                <td><?= $row['concluida'] ? '✅' : '❌' ?></td>
                <td><?= $row['data_criacao'] ?></td>
            </tr>
        <?php
            endwhile;
        else:
        ?>
            <tr><td colspan="5">Nenhuma tarefa cadastrada.</td></tr>
        <?php endif; ?>
    </table>

    <?php mysqli_close($conn); ?>
</body>
</html>
```

#### Consultar um único registro por ID

```php
<?php
require 'conexao.php';

$id = $_GET['id'] ?? 0;

// Prepared Statement para busca por ID
$stmt = mysqli_prepare($conn, "SELECT * FROM tarefas WHERE id = ?");
mysqli_stmt_bind_param($stmt, "i", $id);
mysqli_stmt_execute($stmt);
$result = mysqli_stmt_get_result($stmt);

if ($row = mysqli_fetch_assoc($result)) {
    echo "<h2>{$row['titulo']}</h2>";
    echo "<p>{$row['descricao']}</p>";
} else {
    echo "Tarefa não encontrada.";
}

mysqli_stmt_close($stmt);
mysqli_close($conn);
?>
```

#### Limit e Paginação

Para limitar o número de registros retornados, utiliza-se a cláusula `LIMIT`:

```php
$sql = "SELECT * FROM tarefas ORDER BY data_criacao DESC LIMIT 5";
```

Para paginação, combina-se `LIMIT` com `OFFSET`:

```php
$pagina        = $_GET['pagina'] ?? 1;
$por_pagina    = 5;
$offset        = ($pagina - 1) * $por_pagina;

$sql = "SELECT * FROM tarefas ORDER BY id ASC LIMIT $por_pagina OFFSET $offset";
```

---

### Update — Atualizando Dados (UPDATE)

Assim como nas inserções, recomenda-se o uso de Prepared Statements para garantir segurança:

```php
<?php
require 'conexao.php';

$id        = 1;
$titulo    = "Estudar PHP — Revisado";
$concluida = 1; // true

$stmt = mysqli_prepare($conn, "UPDATE tarefas SET titulo = ?, concluida = ? WHERE id = ?");
mysqli_stmt_bind_param($stmt, "sii", $titulo, $concluida, $id);

if (mysqli_stmt_execute($stmt)) {
    // mysqli_stmt_affected_rows retorna o número de linhas afetadas
    if (mysqli_stmt_affected_rows($stmt) > 0) {
        echo "Tarefa atualizada com sucesso!";
    } else {
        echo "Nenhuma tarefa foi alterada (ID não encontrado ou dados iguais).";
    }
} else {
    echo "Erro ao atualizar: " . mysqli_stmt_error($stmt);
}

mysqli_stmt_close($stmt);
mysqli_close($conn);
?>
```

---

### Delete — Removendo Dados (DELETE)

A exclusão também deve ser feita com Prepared Statements, especialmente quando o identificador vem do usuário (ex: via URL):

```php
<?php
require 'conexao.php';

// Em uma aplicação real, o ID viria de $_GET ou $_POST
$id = $_GET['id'] ?? 0;

$stmt = mysqli_prepare($conn, "DELETE FROM tarefas WHERE id = ?");
mysqli_stmt_bind_param($stmt, "i", $id);

if (mysqli_stmt_execute($stmt)) {
    if (mysqli_stmt_affected_rows($stmt) > 0) {
        echo "Tarefa removida com sucesso!";
    } else {
        echo "Nenhuma tarefa encontrada com esse ID.";
    }
} else {
    echo "Erro ao remover: " . mysqli_stmt_error($stmt);
}

mysqli_stmt_close($stmt);
mysqli_close($conn);
?>
```

> Sempre lembre-se de adicionar uma cláusula `WHERE` ao comando `DELETE`.

---

## SQL Injection — O Perigo das Queries Não Tratadas

Na aula 12, vimos que SQL Injection consiste no envio de trechos de SQL malicioso através de campos de formulário ou parâmetros de URL.

**Exemplo de código vulnerável:**

```php
// NUNCA FAÇA ISSO em produção:
$id = $_GET['id'];
$sql = "SELECT * FROM tarefas WHERE id = $id";
$result = mysqli_query($conn, $sql);
```

Se o usuário acessar `?id=1 OR 1=1`, a query será:

```sql
SELECT * FROM tarefas WHERE id = 1 OR 1=1
```

Como `1=1` é sempre verdadeiro, **todos os registros** serão retornados.

**Solução — Prepared Statements:**

```php
$stmt = mysqli_prepare($conn, "SELECT * FROM tarefas WHERE id = ?");
mysqli_stmt_bind_param($stmt, "i", $id);
mysqli_stmt_execute($stmt);
```

Com Prepared Statements, o valor `1 OR 1=1` é tratado **apenas como dado**, nunca como parte do comando SQL. O banco entende que é uma string, não uma condição lógica.

| Tipo | Caractere | Exemplo de uso |
|------|-----------|----------------|
| String  | `s` | Títulos, nomes, textos |
| Integer | `i` | IDs, quantidades, booleanos |
| Double  | `d` | Preços, valores decimais |
| Blob    | `b` | Arquivos, imagens |

---

## PDO (PHP Data Objects)

> Esta seção é informativa. O PDO utiliza Programação Orientada a Objetos e será abordado com mais profundidade quando você tiver essa base. O objetivo aqui é apresentar a alternativa para que você conheça o caminho recomendado pela comunidade PHP.

### Por que o PDO é recomendado?

1. **Portabilidade:** o mesmo código funciona com MySQL, PostgreSQL, SQLite e outros 9 bancos.
2. **Named Parameters:** os placeholders têm nomes (`:nome`), deixando o código mais legível que `?`.
3. **Fetch Modes flexíveis:** é possível buscar dados como array associativo, objeto, ou instância de classe.
4. **Tratamento de exceções:** erros são reportados como `PDOException`, que podem ser capturadas com `try/catch`.

### Comparação: mysqli procedural vs. PDO

**mysqli procedural — Prepared Statement:**

```php
$stmt = mysqli_prepare($conn, "INSERT INTO tarefas (titulo, descricao) VALUES (?, ?)");
mysqli_stmt_bind_param($stmt, "ss", $titulo, $descricao);
mysqli_stmt_execute($stmt);
```

**PDO — Prepared Statement com named parameters:**

```php
$pdo = new PDO("mysql:host=localhost;dbname=minha_aplicacao;charset=utf8mb4", "appuser", "web1");
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

$stmt = $pdo->prepare("INSERT INTO tarefas (titulo, descricao) VALUES (:titulo, :descricao)");
$stmt->execute([
    ':titulo'    => $titulo,
    ':descricao' => $descricao,
]);
```

---

## Instalação do MySQL

### Windows (XAMPP)

A maioria dos alunos utiliza o **XAMPP**, que já inclui o **MariaDB** (compatível com MySQL) e a extensão PHP necessários. Não é preciso instalar nada adicional.

- O gerenciamento do banco pode ser feito pelo **phpMyAdmin**, disponível em `http://localhost/phpmyadmin`.
- O XAMPP inicia o MySQL/MariaDB automaticamente pelo painel de controle.

### Linux (Debian/Ubuntu)

Para instalar o MySQL e a extensão do PHP no Ubuntu, execute:

```bash
sudo apt-get install mysql-server php-mysql
```

> O pacote `php-mysql` instala automaticamente a extensão compatível com sua versão do PHP. Para descobrir sua versão, execute `php -v`.

Após a instalação, execute o assistente de segurança:

```bash
sudo mysql_secure_installation
```

Responda **"yes"** para todas as questões, **exceto** para a primeira (alteração da senha de root), caso queira manter a senha já configurada.

Reinicie o servidor web:

```bash
sudo service apache2 restart
```

Para testar a instalação, acesse o terminal do MySQL:

```bash
sudo mysql -uroot -p
```

### Criando um usuário para a aplicação

Por boas práticas, não se utiliza o usuário `root` nas aplicações. Crie um usuário específico:

```sql
CREATE USER 'appuser'@'localhost' IDENTIFIED WITH mysql_native_password BY 'sua_senha_aqui';
GRANT ALL PRIVILEGES ON *.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

No lugar de `'sua_senha_aqui'`, defina uma senha de sua preferência. Para o ambiente de desenvolvimento local, pode-se utilizar uma senha simples como `web1`.

> **Nota:** o método `mysql_native_password` é adequado para ambientes de desenvolvimento. Em produção, o MySQL 8+ utiliza `caching_sha2_password`, que é mais seguro.

