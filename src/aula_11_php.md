# Aula 11 - PHP

## Bibliografia recomendada para o tema:

* [w3schools - PHP](https://www.w3schools.com/php/default.asp);
* [PHP oficial](http://php.net/);
* [PHP documentação pt-BR](http://php.net/manual/pt_BR/).

## Starter-code
<https://gitlab.com/ds122-alexkutzke/ds122-php-example>

## Linguagem PHP

### Tags PHP

Quando o PHP interpreta um arquivo ele procura pelas tags de abertura e fechamento, <?php e ?>, que dizem ao PHP para iniciar ou parar a interpretação do código entre elas. A interpretação dessa maneira, permite o PHP ser incluído em vários tipos de documentos, pois tudo que está fora dessas tags é ignorado pelo interpretador do PHP.

O PHP também permite a tag curta `<?` (cujo uso é desencorajado pois essa opção está disponível somente quando habilitada na diretiva `short_open_tag` no arquivo de configuração `php.ini`, ou quando o PHP tiver sido compilado com a opção `--enable-short-tags`).

### Escapando o HTML

Tudo o que estiver fora das tags PHP é ignorado pelo interpretador, o que permite arquivos PHP de conteúdo misto. Permite que o PHP seja incluído dentro de documentos HTML, para, por exemplo, para a criação de templates.

```php
<p>Isto vai ser ignorado pelo PHP e enviado ao navegador.</p>
<?php echo 'Enquanto isto vai ser interpretado.'; ?>
<p>Isto também vai ser ignorado pelo PHP e enviado ao navegador.</p>
```

Isso funcionará porque quando o interpretador do PHP encontra `?>`, a tag de fechamento, ele simplesmente começa a repassar qualquer coisa que encontre (exceto um fim de linha imediato, ver a seção sobre separação de instruções), até que ele encontre outra tag de abertura a não ser que esteja no meio de uma instrução condicional, onde então o interpretador vai determinar o resultado da condicional e assim decidir qual caminho tomar. Veja no próximo exemplo.

#### Exemplo 1 Escape avançado usando condições

```php
<?php if ($expression == true): ?>
  Isto irá aparecer se a expressão for verdadeira.
<?php else: ?>
  Senão isso que aparecerá.
<?php endif; ?>
```

Nesse exemplo o PHP irá escapar os blocos em que a condição não seja satisfeita, mesmo que o trecho de código esteja fora das tags de abertura/fechamento do PHP, pois o interpretador do PHP, irá pular os conteúdos de blocos que não possuem uma condição que não foi satisfeita.
Para impressão de grandes blocos de texto, sair do modo de interpretação do PHP é geralmente mais eficiente que enviar todo o texto através das funções echo ou print.


No PHP 5 existem cinco diferentes pares de tags de abertura e fechamento disponíveis, dependendo de como o interpretador estiver configurado. Dois deles, `<?php ?>` e `<script language="php"> </script>` estão sempre disponíveis. Também a tag curta de echo `<?= ?>`, que está sempre disponível desde o PHP 5.4.0.

As outras duas opções são as tags curtas e tags estilo ASP. Assim, embora algumas pessoas achem as tags curtas e ASP convenientes, são menos portáveis, e geralmente não recomendadas.

O PHP 7 removeu o suporte a tags ASP e `<script language="php">`. Assim, é recomendado utilizar apenas `<?php ?>` e `<?= ?>` ao se escrever códigos PHP com maior compatibilidade.

#### Exemplo 2 Abrindo e Fechando as Tags do PHP

```php
1.  <?php echo 'se você quer servir documentos XHTML ou XML,
                escreva assim'; ?>

2.  Você pode utilizar também a tag curta de echo para <?= 'imprimir isso' ?>.
    Ela sempre está disponível do PHP 5.4.0 em diante, e é equivalente a
    <?php echo 'imprimir isso' ?>.

3.  <? echo 'esse código entre tags curtas somente funcionará '.
            'se short_open_tag estiver habititado'; ?>

4.  <script language="php">
        echo 'alguns editores (como o FrontPage) não
              suportam processar instruções com tags assim';
    </script>
    Esta sintaxe foi removida no PHP 7.0.0.

5.  <% echo 'Você também pode utilizar tags no estilo ASP'; %>
    <%= $variable; %> é um atalho para <% echo $variable; %>
    Essas duas sintaxes também foram removidas no PHP 7.0.0.
```

### Separação de instruções 

Como no C ou Perl, o PHP requer que as instruções sejam terminadas com um ponto-e-vírgula ao final de cada comando. A tag de fechamento de um bloco de código PHP automaticamente implica em um ponto-e-vírgula; você não precisa ter um ponto-e-vírgula terminando a última linha de um bloco PHP. A tag de fechamento do bloco irá incluir uma nova linha logo após, se estiver presente.

```php
<?php
    echo 'Isto é um teste';
?>

<?php echo 'Isto é um teste' ?>

<?php echo 'Nós omitimos a última tag de fechamento';
```

### Links importantes

* [Tipos](http://php.net/manual/pt_BR/language.types.php);
* [Variáveis](http://php.net/manual/pt_BR/language.variables.php);
* [Constantes](http://php.net/manual/pt_BR/language.constants.php);
* [Expressões](http://php.net/manual/pt_BR/language.expressions.php);
* [Operadores](http://php.net/manual/pt_BR/language.operators.php);
* [Estruturas de
  Controle](http://php.net/manual/pt_BR/language.control-structures.php);
* [Funções](http://php.net/manual/pt_BR/language.functions.php).

### Produção de conteúdo dinâmico

Um dos objetivos centrais da programação Web é produzir conteúdo de páginas de
maneira dinâmica. Por vezes, o conteúdo gerado deve ser baseado em estruturas de
dados envolvidas na própria lógica de programação da aplicação. Por exemplo,
visualização de estruturas como vetores, matrizes, listas, além de dados
armazenados em diferentes Bancos de Dados. Por essa razão, é comum a produção de
rotinas (funções, classes e métodos) para a produção desse conteúdo. O código a
seguir é um exemplo disso. A partir de um vetor de números inteiros, a função
`cria_lista` retorna uma string com os elementos do vetor em um formato de lista
não ordenada em HTML.

```php
<?php
  function cria_item_lista($item){
    return("<li>$item</li>");
  }
  function cria_lista($itens){
    $result = "<ul>";
    foreach ($itens as $key => $value) {
      $result .= cria_item_lista($value);
    }
    $result .= "</ul>";
    return($result);
  }
?>

<!DOCTYPE html>
<html>
<head>
  <title>Testes de PHP</title>
</head>
<body>
  <?php
    $itens = array(1, 2, 4, 5, 6);

    echo cria_lista($itens);
  ?>


</body>
</html>
```

O exemplo acima é bastante simples, mas serve para ilustrar a produção básica de
um conteúdo dinâmico baseado em estruturas de dados.
