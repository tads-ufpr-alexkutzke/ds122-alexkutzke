# Aula 12 - PHP - SuperGlobals, Formulários, Validação e Includes.

## Bibliografia recomendada para o tema:

* [w3schools - PHP SuperGlobals](https://www.w3schools.com/php/php_superglobals.asp);
* [w3schools - PHP Forms](https://www.w3schools.com/php/php_forms.asp);

## Código de exemplo

<https://gitlab.com/ds122-alexkutzke/ds122-php-forms-example>

## Arrays SuperGlobals

Ao iniciar um script PHP, o interpretador instancia uma série de Arrays globais,
comumente denominados SuperGlobals. Essas variáveis, acessíveis em qualquer
parte do código, trazem diferentes informações, desde valores de variáveis globais até informações sobre a requisição sendo respondida pelo servidor WEB. Os Arrays SuperGlobals mais utilizados são os seguintes:

* `$GLOBALS`: Variáveis globais do código;
* `$_SERVER`: Informações sobre cabeçalhos, caminhos e localização de scripts;
* `$_REQUEST`: Reuni dados enviados por formulário;
* `$_POST`: Reuni dados enviados através do método POST;
* `$_GET`: Reuni dados enviados através do método GET;
* `$_FILES`: Controla informações sobre arquivos de Upload;
* `$_ENV`: Variáveis de ambiente;
* `$_COOKIE`: Cookies enviados para a aplicação;
* `$_SESSION`: Informações sobre as sessões abertas.

Para mais informações, consulte https://php.net/manual/pt_BR/reserved.variables.php

Exemplos de uso de Arrays SuperGlobals:

```php
<?php 
$x = 75; 
$y = 25;
 
function addition() { 
    $GLOBALS['z'] = $GLOBALS['x'] + $GLOBALS['y']; 
}
 
addition(); 
echo $z; 
?>

<?php 
echo $_SERVER['PHP_SELF'];
echo "<br>";
echo $_SERVER['SERVER_NAME'];
echo "<br>";
echo $_SERVER['HTTP_HOST'];
echo "<br>";
echo $_SERVER['HTTP_REFERER'];
echo "<br>";
echo $_SERVER['HTTP_USER_AGENT'];
echo "<br>";
echo $_SERVER['SCRIPT_NAME'];
?>
```

## Formulários

A forma mais comum para receber/coletar informações de usuários em uma
aplicação web é por meio de formulários. Como visto durante as aulas de HTML,
sabemos que, para um formulário funcionar, são necessários, pelo menos, dois
atributos: `action` e `method`. A seguir temos o exemplos de um formulário
simples:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Teste de formulário</title>
  </head>
  <body>
    <form action="form_test.php" method="get">
      <input type="text" name="busca" value=""> 
      <input type="submit">
    </form>
  </body>
</html>
```

No exemplo acima, o formulário, ao ser submetido, fará uma requisição com o
método GET para o recurso chamado `form_test.php` no mesmo servidor/domínio da
página acima. É importante que todos os campos do formulário tenha o atributo
`name`. Caso contrário, os campos sem tal atributo terão seus valores ignorados
durante a requisição ao servidor.

Abaixo, um possível exemplo para o recurso `form_test.php`.


```php
<?php
  $busca = $_GET["busca"];
?>

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Teste formulário</title>
  </head>
  <body>
    <?= $busca ?>
  </body>
</html>
```

O código acima apenas exibe a informação passada pelo usuário através do
formulário. 

Com PHP, para consultar as informações transmitidas por meio de formulários, 
em geral, consultamos o array SuperGlobals referente ao método utilizado:
`$_POST` ou `$_GET`. 

Além disso, é bastante comum definirmos no script PHP diferentes ações de acordo com
o método de requisição. Para isso, podemos utilizar a informação contida em 
`$_SERVER["REQUEST_METHOD"]`:

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST['fname'];
    echo $name;
}
?>
```

Abaixo, um exemplo de formulário que envia os dados para a própria paǵina
(atenção ao valor de `$_SERVER['PHP_SELF']`.

```php
<html>
<body>

<form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
  Name: <input type="text" name="fname">
  <input type="submit">
</form>

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // collect value of input field
    $name = $_POST['fname'];
    if (empty($name)) {
        echo "Name is empty";
    } else {
        echo $name;
    }
}
?>

</body>
</html>
```

## Validação

Validação é o ato de verificar se os dados enviados/recebidos estão de acordo
com o esperado. Isso garante que o sistema funcione corretamente e que não sofra
com ataques de segurança.

No desenvolvimento Web, a validação pode ocorrer em 2 momentos: no servidor
(back-end, após os dados serem recebidos ou no cliente (front-end), antes 
dos dados serem enviados ao servidor.

Como exemplo de ações de validação, temos:

* Verificação se campos obrigatórios foram preenchidos;
* Verificação se os campos foram preenchidos com dados corretos (idade sem
  letras, nome apenas com letras, data no formato esperado, etc.);
* Verificação se os dados preenchidos não representam ameaças de segurança ao
  sistema;
* Verificação se os dados não irão corromper a integridade do Banco de dados;
* Entre outros.

### Validação Back-end

Validação back-end, ou server-side, é a validação que ocorre no servidor.
No nosso caso, tal validação é utilizada com recursos da linguagem PHP. Um
exemplo de função simples, para validação de campos de texto pode ser o
seguinte:

```php
<?php
function verifica_campo($texto){
  $texto = trim($texto);
  $texto = stripslashes($texto);
  $texto = htmlspecialchars($texto);
  return $texto;
}
?>
```

Pode-se, ainda, utilizar a função `empty` para verificar se um campo foi
preenchido ou não. Por exemplo:

```php
<?php
if(empty($_POST["nome"])){
  $erro_nome = "Nome é obrigatório.";
  $erro = true;
}
?>
```

Deve-se tomar muito cuidado para prevenir a integridade dos dados 
(principalmente antes de salvá-los no Banco da Dados) e ataques de segurança (ver XSS abaixo).

Mais funções para validação com PHP:

* `preg_match`: [(w3schools)](https://www.w3schools.com/php/php_form_url_email.asp) [(php.net)](http://php.net/manual/en/function.preg-match.php);
* `filter_var`: [(w3schools)](https://www.w3schools.com/php/func_filter_var.asp) [(php.net)](http://php.net/manual/en/function.filter-var.php);

#### Upload de arquivos

* https://www.w3schools.com/php/php_file_upload.asp

### Validação Front-end

Validação front-end, ou client-side, é a validação que ocorre ainda no cliente.
Ou seja, deve ocorrer antes da requisição HTTP ser executada. Previne que dados incorretos
sejam enviados ao servidor e possibilita que uma carga desnecessária de dados seja enviada
ao servidor.

Tal validação é, geralmente, realizada com com JavaScript, como abaixo:

```javascript
$(function(){
  $("#form-test").on("submit",function(){
    nome_input = $("input[name='nome']");

    if(nome_input.val() == "" || nome_input.val() == null)
    {
      $("#erro-nome").html("O nome eh obrigatorio");
      return(false);
    }

    return(true);
  });
});

```

Atenção deve ser dada aos `return's` no código acima. O trecho apresentado
trata do evento `submit` do formulário identificado por `#form-test`. Quando um
evento retorna um valor `false` o processo do evento será interrompido. Ou seja,
no caso acima, de uma submissão de formulário, a submissão será interrompida e
nenhum dado será enviado ao servidor. Dessa forma, previne-se o envio
desnecessário de informações.

Atributos de formulário (ver: maxlenght, min, max e required):

* https://www.w3schools.com/html/html_form_attributes.asp

Outros métodos para validação com Javascript:

* `setCustomValidity`: [(w3schools)](https://www.w3schools.com/js/js_validation_api.asp);

### Cross-site scripting (XSS)

O ambiente Web pode ser considerado um dos mais hostis no que se refere a
questões de segurança. Isso ocorre pois, em geral, as aplicações Web estão disponíveis a
qualquer computador conectado à internet. Em outras palavras, qualquer pessoa no
mundo pode acessar e tentar realizar ataques a qualquer aplicação Web na
internet.

Dentre as tentativas de ataque mais comuns está o **Cross-site scripting
(XSS)**. Esse tipo de ataque trata de envio de scripts externos para serem
executados em uma aplicação Web.

Por exemplo, considere uma aplicação Web que apresenta uma página com o perfil
de usuários. Durante o cadastro, os usuários preenchem o formulário com vários
dados pessoais, como nomes, idade, email, etc. Agora suponha que em um desses
campos, o usuário preenchesse o seguinte: 

**Nome:** `José da Silva <script>alert('Site hackeado');</script>`

Sem qualquer cuidado de segurança, essa aplicação Web estaria vulnerável ao
ataque acima sempre que a página de perfil do usuário acima fosse acessada.

Outro exemplo bastante comum é o uso de **SQL Injection**, ou injeção de SQL.
Esse tipo de ataque, uma forma específica de XSS, consiste no envio de
trechos de código (SQL) que exibem informações sensíveis ou que alterem permanentemente os dados do Banco de Dados
da aplicação Web. Por exemplo, suponha que em uma aplicação que busca por
usuários em um Banco de Dados, o usuário preencha o seguinte:

**UserId:** `105 OR 1=1`

Se, em algum momento, a aplicação utilizar o valor digitado pelo usuário para
formular um `SELECT`, poderíamos ter algo parecido:

```sql
SELECT UserId, Name, Password FROM Users WHERE UserId = 105 or 1=1;
```

O que retornaria os dados de todos os usuários do banco.

Ataques XSS são extremamente comuns na internet. Por essa razão, qualquer
aplicação Web deve tomar os cuidados necessários para evitá-los. A ação mais
simples é a validação dos dados enviados por usuários.

## Includes e Requires

Assim como na linguagem C, é possível incluir arquivos PHP em outros. Dessa
forma, podemos organizar o código em módulos específicos. Para realizar tal
inclusão, temos acesso às seguintes diretrizes do PHP:


* `include 'foo.php';`: inclui o arquivo `foo.php`. Caso o arquivo não exista,
  apresenta uma mensagem de `warning` mas não interrompe a execução do script;
* `include_once 'foo.php';`: inclui o arquivo `foo.php` e garante que, se o arquivo 
  já tenha sido incluído no script anteriormente, ele não seja incluido novamente. 
  Caso o arquivo não exista,  apresenta uma mensagem de `warning` mas não interrompe 
  a execução do script;
* `require 'foo.php';`: inclui o arquivo `foo.php`. Caso o arquivo não exista,
  apresenta uma mensagem de `erro` e **interrompe a execução do script**;
* `require_once 'foo.php';`: inclui o arquivo `foo.php` e garante que, se o arquivo 
  já tenha sido incluído no script anteriormente, ele não seja incluido novamente. 
  Caso o arquivo não exista,  apresenta uma mensagem de `erro` e **interrompe 
  a execução do script**;

```php
vars.php
<?php

$color = 'green';
$fruit = 'apple';

?>

test.php
<?php

echo "A $color $fruit"; // A

include 'vars.php';

echo "A $color $fruit"; // A green apple

?>
```

