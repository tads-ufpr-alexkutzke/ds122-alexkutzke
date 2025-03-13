# Aula 05 - CSS

## Bibliografia recomendada para o tema:
* [MDN - CSS](https://developer.mozilla.org/pt-BR/docs/Learn/CSS);
* [w3schools - CSS](https://www.w3schools.com/css/default.asp).

## Cascanding Style Sheets

**CSS** ou Cascanding Style Sheets é uma linguagem de descrição, utilizada
para definir o estilo de um arquivo HTML. Ou seja, utiliza-se o CSS para
especificar a **aparência** que um arquivo HTML deve ter.

Com o uso de arquivos CSS, é possível separar completamente a descrição da
aparência e do conteúdo de uma página HTML. Com isso, facilita-se muito a
manutenção das páginas de um site. Um exemplo de arquivo CSS é mostrado
abaixo:

```css
body {
    background-color: lightblue;
}

h1 {
    color: white;
    text-align: center;
}

p {
    font-family: verdana;
    font-size: 20px;
}
```

### Sintaxe e seletores

Arquivos CSS seguem uma sintaxe simples: uma série **regras** definem as
**propriedades** de aparência que devem ser aplicadas a diferentes
**seletores**.

Uma **regra** possui a seguinte sintaxe:

```css
Seletor {
  propriedade: valor;
}
```

Um **seletor** é um conjunto de caracteres capaz de identificar um ou mais
elementos de uma página HTML. Por exemplo, o seletor `h1` identifica **todos
os títulos h1** de uma página.

Propriedades, por sua vez, são elementos que indicam quais os valores devem
ser aplicados para cada características (fonte, tamanho, margem, ...) dos
elementos indicados por um seletor.

Abaixo, temos o exemplo de uma regra CSS completa:

```css
h1 {
    color: white;
    text-align: center;
}
```

A regra acima indica que, todos os títulos `h1` devem ter cor de texto `branco`
e alinhamento `centralizado`. Um arquivo CSS é composto por um conjunto de regras
semelhantes à apresentada acima.

### Adicionando um CSS ao HTML

Existem 3 formas de adicionar regras CSS a um arquivo HTML:

#### 1. Atributo Style

Qualquer **tag** HTML pode receber propriedades CSS por meio de um atributo
`style`. Nesse caso, omitem-se os seletores e as chaves. Por exemplo:

```html
<p style="color:red; text-align: center">Texto do parágrafo</p>
```

Nesse caso, o parágrafo acima, **e apenas ele**, terá cor de texto vermelha e
alinhamento centralizado.

#### 2. Tag `style`

É possível adicionar conteúdo CSS diretamente em um HTML através da tag `style`
no cabeçalho da página. Por exemplo:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
    <style>
      body {
          background-color: lightblue;
      }

      h1 {
          color: white;
          text-align: center;
      }

      p {
          font-family: verdana;
          font-size: 20px;
      }
    </style>
  </head>
<body>

  <h1>This is a Heading</h1>
  <p>This is a paragraph.</p>

</body>
</html>
```

#### 3. Arquivos externos

Por fim, é possível adicionar as regras CSS relevantes a uma página através de
um arquivo externo. Por exemplo, considere um arquivo chamado `estilo.css` com o
seguinte conteúdo:

```css
/* estilo.css */
body {
    background-color: lightblue;
}

h1 {
    color: white;
    text-align: center;
}

p {
    font-family: verdana;
    font-size: 20px;
}
```

Para utilizar esse arquivo CSS em um arquivo HTML, basta adicionar o seguinte
ao cabeçalho do arquivo HTML (**desde que os dois arquivos estejam no mesmo
diretório**):

```html
<head>
  <link rel="stylesheet" type="text/css" href="estilo.css">
</head>
```

A opção de adicionar CSS em um arquivo externo é a melhor delas, pois permite
a total separação entre aparência e conteúdo. Além disso, vários arquivos HTML
podem compartilhar um mesmo arquivo CSS. Facilita-se, assim, a manutenção dos
arquivos.

As opções do atributo `style` e da tag `<style>` devem ser utilizadas apenas
em situações onde forem extremamente necessárias.


### Identificação de tags

É comum a necessidade de atribuir algum tipo de identificação para tags HTML a fim de que 
essas fiquem mais acessível ao arquivo CSS. Para isso, utilizamos, comumente, os atributos
`id` e `class`.

#### Atributo `id`

O atributo `id` define um nome, único em todo o documento, para uma tag HTML. Tags 
com `id` são facilmente identificadas por regras CSS por meio do seletor `#`. Por exemplo,
o código abaixo define regras para uma única tag que possui o id `teste`:

```css
#teste{
  color: red;
}
```

No HTML:

```html
<div id="teste"> ... </div>
```


#### Atributo `class`

O atributo `class` define uma  classe para uma tag HTML. Mais de uma tag pode receber
uma mesma classe. Além disso, uma tag HTML pode receber quantas classes forem necessárias. 
Tags com `class` são facilmente identificadas por regras CSS por meio do seletor `.` (ponto). 
Por exemplo, o código abaixo define regras para um conjunto de tags que possuem a
classe `teste`:

```css
.teste{
  color: red;
}
```

No HTML:

```html
<div class="teste"> ... </div>
```


### Modelo de caixa

Um conceito importante utilizado no CSS é o que chamamos de **modelo de caixa**.
Esse conceito define que **todos** os elementos de uma página HTML são
retangulares e são compostos por 4 camadas: margin (margem), border (borda),
padding (espaçamento) e conteúdo. A figura a seguir demonstra um exemplo do
modelo de caixa:

![box model](https://www.hostinger.com/tutorials/wp-content/uploads/sites/2/2021/11/css-box-model.webp "Modelo de Caixa")

* Conteúdo: é o conteúdo do elemento (texto, imagens, etc);
* Padding: é o espaço entre o conteúdo e a borda do elemento;
* Border: borda visível ou não que cerca o elemento;
* Margin: espaço transparente que separa o elemento dos demais.

### Links importantes

* [Principais seletores](https://www.w3schools.com/css/css_syntax.asp);
* [Cores](https://www.w3schools.com/css/css_colors.asp);
* [Background](https://www.w3schools.com/css/css_background.asp);
* [Bordas](https://www.w3schools.com/css/css_border.asp);
* [Margens](https://www.w3schools.com/css/css_margin.asp);
* [Padding](https://www.w3schools.com/css/css_padding.asp);
* [Altura e Largura](https://www.w3schools.com/css/css_dimension.asp);
* [Texto](https://www.w3schools.com/css/css_text.asp);
* [Fontes](https://www.w3schools.com/css/css_font.asp);
* [Display](https://www.w3schools.com/css/css_display_visibility.asp);
* [Largura máxima](https://www.w3schools.com/css/css_max-width.asp);
* [Position](https://www.w3schools.com/css/css_positioning.asp);
* [Overflow](https://www.w3schools.com/css/css_overflow.asp);
* [Float](https://www.w3schools.com/css/css_float.asp);
* [Inline-block](https://www.w3schools.com/css/css_inline-block.asp);
* [Combinadores](https://www.w3schools.com/css/css_combinators.asp);
* [Pseudo-classes](https://www.w3schools.com/css/css_pseudo_classes.asp);
* [Pseudo-elementos](https://www.w3schools.com/css/css_pseudo_elements.asp);

### CSS3

A linguagem CSS passa por constante evolução e, atualmente, caminhamos para
o estabelecimento da versão 3, conhecida por CSS3. Navegadores ainda estão
se adaptando às novas regras e propriedades da linguagem, mas a compatibilidade
já é bastante satisfatória.

O CSS3 traz várias melhores e novas propriedades que permitem uma maior liberdade
na definição do estilo de uma página HTML. Algumas exemplos da novas
funcionalidades interessantes da versão 3: transformações 2D e 3D, animações,
transparência, sombras dentre várias outras. 
