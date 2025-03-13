# Aula 04 - HTML

## Bibliografia recomendada para o tema:
* [MDN - HTML](https://developer.mozilla.org/pt-BR/docs/Web/HTML).

## HyperText Markup Language

O HTML (HyperText Markup Language), é, como o nome diz, uma linguagem de marcação
para criação de documento hipertexto. Abaixo, temos um exemplo de um arquivo
HTML simples:

```html
<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>

<h1>This is a Heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

## Tags e atributos

A linguagem conta com um conjunto de
palavras-chave denominadas `tags`. De maniera geral, uma `tag` possui o seguinte
formato:

```html
<nome_tag atributo1="valor1" atributo2="valor2" ...>conteúdo</nome_tag>
```

Onde `<nome_tag ...>` é chamada de tag de abertura e `</nome_tag>` é denominada
tag de fechamento. Vale ressaltar que nem todas as tads possuem fechamento,
nesse caso, a sintaxe é a seguinte:

```html
<nome_tag atributo1="valor1" atributo2="valor2" ... />
```

Cada tag pode receber zero ou mais **atributos**. Um atributo indica
características referentes a tag em questão. Por exemplo:

```html
<a href="http://www.tads.ufpr.br">Moodle do TADS</a>
```

A tag acima irá produzir um link (ou âncora) para a url `http://www.tads.ufpr.br`,
sendo exibida na página com o texto `Moodle do TADS`. Algo semelhante a isso:
[Moodle do TADS](http://www.tads.ufpr.br).

Isso ocorre pois a tag `a` indica a criação de um link e, por meio do parâmentro
`href`, o navegador sabe para qual URL o link deve levar ao ser clicado.

# Estrutura de um arquivo HTML

Um arquivo HTML sempre deve possuir:
* Uma declaração de tipo, geralmente na primeira linha do arquivo;
* Uma tag `<html>...</html>`, com o seguinte conteúdo:
  * Uma tag `<head>...</head>` que indica o **cabeçalho** do documento. Aqui vão
  tags que descrevem propriedades do documento, como, por exemplo, `<title>...</tile>`;
  * Uma tag `<body>...</body>` que indica o **corpo** do documento. Aqui vão todas
  as tags de *conteúdo* do documento. Tudo que deve ser visível ao usuário, deve
  ser descrito dentro da tag `<body>`.

# Características do HTML

O HTML deve ser utilizado para especificar apenas duas coisas:

* O **conteúdo** do documento (títulos, parágrafos, figuras, etc.);
* A **estrutura** do documento (isso é um título, aquilo é um link).

Ou seja, o HTML deve ser utilizado para definir a **semântica** do seu conteúdo,
seu significado. **Não se deve utilizar o HTML para indicar nenhum tipo de formatação
ou posicionamento do conteúdo**.

É importante:

* Que o arquivo HTML seja corretamente indentado. Tags que estão *dentro* de outras
devem receber um recúo à direita;
* Utilize editores de texto simples para escrever seus arquivos HTML durante a disciplina.
Evite o uso IDEs:
  * Atom ou Sublime Text são indicados;
* Tags e atributos sempre devem ser escritos com letras minúsculas;
* Valores de atributos devem ser escritos entre àspas dúplas.

## Tags e conceitos importantes

* [Charset](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#attr-charset);
* [Links](https://developer.mozilla.org/pt-BR/docs/Learn/HTML/Introduction_to_HTML/Creating_hyperlinks);
* [Imagens](https://developer.mozilla.org/pt-BR/docs/Learn/HTML/Multimedia_and_embedding/Images_in_HTML);
* [Tabelas](https://developer.mozilla.org/pt-BR/docs/Learn/HTML/Tables/Basics);
* [Listas](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Element/li);
* [Elementos "em linha" e "em bloco"](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Block-level_elements#n%C3%ADvel-de-bloco_vs._em-linha);
* [Formulários](https://developer.mozilla.org/pt-BR/docs/Learn/Forms/Your_first_form);
* [URL encoding](https://developer.mozilla.org/en-US/docs/Glossary/percent-encoding).

## Exemplo HTML com formulário

```html
<!DOCTYPE html>
<html>
<head>
  <title>Page Title</title>
  <meta charset="utf-8"/>
</head>
<body>

  <form action="INSERIR_URL" method="post">
    First name:<br>
    <input type="text" name="firstname"><br>
    Last name:<br>
    <input type="text" name="lastname"><br><br>

    <input type="radio" name="gender" value="male" checked> Male<br>
    <input type="radio" name="gender" value="female"> Female<br>
    <input type="radio" name="gender" value="other"> Other<br>

    <select name="cars">
      <option value="volvo">Volvo</option>
      <option value="saab">Saab</option>
      <option value="fiat">Fiat</option>
      <option value="audi">Audi</option>
    </select><br>

    <textarea name="message" rows="10" cols="30">
      The cat was playing in the garden.
    </textarea><br>

    <input type="submit" value="Submit">
  </form>

</body>
</html>
```
