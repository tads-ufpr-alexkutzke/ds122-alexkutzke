# Aulas 13 - PHP - Manipulação de bancos de dados

## Bibliografia recomendada para o tema:

* [w3schools - PHP+Mysql](https://www.w3schools.com/php/php_mysql_intro.asp).

## Acesso ao MySQL

Para acessar o banco de dados MySQL a partir de um script PHP temos as seguintes
opções:

1. Biblioteca (ou extensão) `mysqli` no formato procedural;
2. Biblioteca (ou extensão) `mysqli` no formato orientado a objetos;
3. Biblioteca `PDO` (PHP Data Objects).

Durante a disciplina, utilizaremos a primeira, pois não faz uso de orientação a
objetos.

A opção PDO pode ser uma ótima alternativa quando se tem a necessidade de
utilizar outros bancos de dados (Postgresql, por exemplo), pois tal biblioteca é
capaz de se conectar com 12 bancos diferentes.
A biblioteca Mysqli, por sua vez, é capaz de se conecta apenas com o banco de
dados MySQL.

Para realizar manipulação de dados do MySQL através da biblioteca Mysqli, faz-se
uso das funções providas pela biblioteca. Seguem abaixo links para exemplos das
principais funções:

* [Conexão](https://www.w3schools.com/php/php_mysql_connect.asp);
* [Criação de Database](https://www.w3schools.com/php/php_mysql_create.asp);
* [Criação de Tabela](https://www.w3schools.com/php/php_mysql_create_table.asp);
* [Inserção de dados](https://www.w3schools.com/php/php_mysql_insert.asp);
* [Inserção de dados: uso do lastId](https://www.w3schools.com/php/php_mysql_insert_lastid.asp);
* [Inserção de múltiplos registros](https://www.w3schools.com/php/php_mysql_insert_multiple.asp);
* [Prepared Statements](https://www.w3schools.com/php/php_mysql_prepared_statements.asp);
* [Seleção de dados](https://www.w3schools.com/php/php_mysql_select.asp);
* [Remoção de dados](https://www.w3schools.com/php/php_mysql_delete.asp);
* [Atualização de dados](https://www.w3schools.com/php/php_mysql_update.asp);
* [Limit](https://www.w3schools.com/php/php_mysql_select_limit.asp);

## Exemplos Mysqli

Para exemplos de arquivos utilizando o mysqli acesse: <https://gitlab.com/ds122-alexkutzke/ds122-php-mysqli-example>

## Prática (não precisa entregar)

Criar a aplicação My Tasks, disponível no seguinte repositório:

https://gitlab.com/ds122-alexkutzke/ds122-my-tasks-app2

A partir do código inicial, disponível em:

https://gitlab.com/ds122-alexkutzke/ds122-my-tasks-app-starter

### Video aula sobre criação da aplicação My Tasks

https://youtu.be/5XLp6uZKEug

## Instalação MySQL em sistema UNIX (debian based)

Para instalar o MySQl e a extensão do PHP no ubuntu, basta executar o seguinte comando:

```bash
sudo apt-get install mysql-server php8.1-mysql
```

Para padronização, escolha como senha para o usuário root: `web1`.

O comando abaixo deve ser executado para tornar a instalação mais segura:

```bash
sudo mysql_secure_installation
```

Aplique a resposta "yes" para todas as questões, exceto para a primeira (alteração da senha de root).

Reinicie o servidor apache2 para os novos dados sejam recarregados.

```bash
sudo service apache2 restart
```

Para testar a instalação, acesso o terminal do mysql a partir do seguinte
comando:

```bash
sudo mysql -uroot -p
```

Para que não seja necessário o acesso com o `sudo`, ou super usuário, repita o comando acima e, no prompt do mysql, digite os seguintes comandos:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'SUA NOVA SENHA';
FLUSH PRIVILEGES;
```

No lugar de `SUA NOVA SENHA`, insira a senha desejada para acessar o mysql. Sugiro que a senha seja `web1`.

