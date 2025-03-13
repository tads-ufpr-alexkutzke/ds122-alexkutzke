# Aulas 14 - PHP - Cookies, Sessions e Login.

## Bibliografia recomendada para o tema:

* [Sobre cookies no protocolo HTTP](https://pt.wikipedia.org/wiki/Cookie_HTTP);
* [w3schools - PHP Cookies](http://www.w3schools.com/php/php_cookies.asp);
* [w3schools - PHP Sessions](http://www.w3schools.com/php/php_sessions.asp).

## Cookies no Protocolo HTTP

Do site [Wikipedia - Cookie HTTP](https://pt.wikipedia.org/wiki/Cookie_HTTP):

> Um cookie é um pequeno pacote de dados enviados de um website para o navegador do usuário quando o usuário visita o site. Cada vez que o usuário visita o site novamente, o navegador envia o cookie de volta para o servidor para notificar atividades prévias do usuário. Os cookies foram designados para ser um mecanismo confiável para que sites se lembrem de informações da atividade do usuário, como senhas gravadas, itens adicionados no carrinho de compras em uma loja online, links que foram clicados anteriormente, entre outros.

### Cookies no PHP

A criação de um Cookie por meio da linguagem PHP depende do uso da função `setcookie`.
Essa função recebe, no máximo 7 parâmentros:

```php
setcookie(name, value, expire, path, domain, secure, httponly);
```

Entretanto, apenas o parâmentro `name` (nome do Cookie a ser criado) é obrigatório. O segundo parâmetro, `value`, indica o valor a ser armazenado no cookie. O terceiro, `expire`, por sua vez, marca a data de validade (em segundos) do cookie. Após tal data, o cookie será excluído pelo navegador.

```php
setcookie($cookie_name, $cookie_value, time() + (86400 * 30), "/"); // 86400 = 1 day
```

Por exemplo, o cookie acima terá uma data de validade de 30 dias a partir do momento de criação.

Para alterar o valor de um cookie, basta executar a mesma função `setcookie` passando outro valor.

Para acessar os dados de um cookie, é necessário utilizar o array superglobal `$_COOKIE`. Por exemplo:

```php
<?php
$cookie_name = "user";
$cookie_value = "John Doe";
setcookie($cookie_name, $cookie_value, time() + (86400 * 30), "/"); // 86400 = 1 day
?>
<html>
<body>

<?php
if(!isset($_COOKIE[$cookie_name])) {
    echo "Cookie named '" . $cookie_name . "' is not set!";
} else {
    echo "Cookie '" . $cookie_name . "' is set!<br>";
    echo "Value is: " . $_COOKIE[$cookie_name];
}
?>

</body>
</html>
```

Observação: é importante que a função `set_cookie` seja chamada antes da tag HTML, pois os dados devem ser adicionados ainda no cabeçalho da resposta HTTP.

Para remover um cookie, simplesmente atribua uma data de validade já expirada:

```php
// set the expiration date to one hour ago
setcookie("user", "", time() - 3600);
```
## Sessões

Sessões são uma forma robusta da linguagem PHP de manter informações armazenadas entre
diferentes requisições HTTP de um mesmo usuário.

Sempre que uma sessão PHP é iniciada, o usuário recebe um Cookie com uma chave que o identifica, chamada SessionID. A partir dessa chave, o PHP é capaz de saber qual usuário (computador/navegador) está realizando uma requisição.

Para fazer uso de sessões no PHP é necessário iniciá-las por meio da função `session_star()`. Essa função deve ser chamada sempre que o uso de sessões for necessário.

Por fim, para armazenar ou ler dados de uma sessão, basta utilizar o array superglobal `$_SESSION`. Por exemplo:

```php
<?php
session_start();
?>
<!DOCTYPE html>
<html>
<body>

<?php
// Echo session variables that were set on previous page
echo "Favorite color is " . $_SESSION["favcolor"] . ".<br>";
echo "Favorite animal is " . $_SESSION["favanimal"] . ".";
?>

</body>
</html>
```

## Sistema de login com Sessions

https://gitlab.com/ds122-alexkutzke/ds122-login-app
