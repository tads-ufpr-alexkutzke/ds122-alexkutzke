# Aula 7 - DOM

## Bibliografia recomendada para o tema:
* [w3schools - JS DOM](https://www.w3schools.com/js/js_htmldom.asp);

## Repositório com *starter code* para a aula:
<https://gitlab.com/ds122-alexkutzke/ds122-dom-example>

## DOM (Document Object Model)

O **DOM** (Documet Object Model) é um padrão estabelecido pelo W3C
que define como a linguagem Javascript pode acessar e modificar um
documento HTML dentro do navegador. Em outras palavras, o DOM pode
ser entendido como uma *API* de acesso ao documento exibido pelo
navegador. Por meio dessa API, programas escritos em Javascript
podem, facilmente, acessar e modificar quaisquer características
de um documento HTML.

### Manipulação do DOM

Por se tratar de um padrão emitido pelo W3C, a maioria dos navegadores
possui, nativamente, a implementação do DOM.
Essa API é composta por um objeto JS chamado `document` que representa
integralmente o documento HTML que está sendo exibido pelo navegador.

![Arquitetura do DOM](https://www.w3schools.com/js/pic_htmltree.gif)

Dessa forma, os programas Javascript rodados em navegadores possuem
acesso à uma variável global com o nome de `document`. E, a partir
dela, a todas operações possíveis sobre o documento.

#### Seleção de elementos

Em geral, quando é necessário manipular um documento HTML a partir
do DOM, temos que identificar o elemento a ser alterado. Um elemento
nada mais é do que um componente que faz parte do documento (como
um parágrafo, um título, um link, etc.).

Existem algumas formas de seleção de elementos permitidos pelo DOM.
A seguir, seguem alguns exemplos:

```javascript
// Seleção por id
var x = document.getElementById("teste");

// Seleção por nome de Tag
var x = document.getElementsByTagName("p");

// Seleção por classe
var x = document.getElementsByClassName("intro");

// Seleção por seletores CSS
var x = document.querySelectorAll("p.intro");

// Seleção por coleções pré-existentes (ver mais em w3schools)
var x = document.forms
```

Cada elemento do DOM possui atributos e métodos utilizados
para alterar as propriedades do elemento no documento.
Por exemplo, os comandos abaixo realizam alterações em elementos
do DOM:

```javascript
// Altera todo o body
document.write(Date());

// Altera o conteúdo do elemento com id igual a "p1"
document.getElementById("p1").innerHTML = "Novo conteudo!";

// Cada elemento pode ser atribuído em uma variável e alterado posteriormente
var element = document.getElementById("header");
element.innerHTML = "Novo texto";

// Altera um atributo do elemento com id igual "myImage"
document.getElementById("myImage").src = "landscape.jpg";

// Altera propriedades CSS
document.getElementById("p2").style.color = "blue";
```

#### Navegação no DOM

Cada elemento do DOM pode possuir elementos filhos, ancestrais e irmãos.
É possível, através da API DOM "navegar" por esses elementos.

![](https://www.w3schools.com/js/pic_navigate.gif)

É possível utilizar os seguintes atributos para navegar entre os elementos
do DOM:

* `parentNode`
* `childNodes[nodenumber]`
* `firstChild`
* `lastChild`
* `nextSibling`
* `previousSibling`

```javascript
// primeiro filho de um elemento
var myTitle = document.getElementById("demo").firstChild.nodeValue;

// primeiro filho de um elemento (utilizando o array childNodes)
var myTitle = document.getElementById("demo").childNodes[0].nodeValue;
```

É possível, ainda, criar novos elementos em um arquivo html utilizando Javascript:

```javascript
// Cria uma nova tag <p>
var para = document.createElement("p");
var node = document.createTextNode("This is new.");
para.appendChild(node);

var element = document.getElementById("div1");
element.appendChild(para);
```

Para mais detalhes, consulte: https://www.w3schools.com/js/js_htmldom_nodes.asp

#### Eventos

Boa parte da programação realizada utilizando Javascript em navegadores
trabalha sobre um paradigma orientado a **eventos**. Um evento é algo
que ocorre no navegador, como: um clique, o carregamento da página, redimensionamento
da janela do navegador, movimento do mouse, etc.

Isso significa que os programas são, em geral, um conjunto de
funções executadas quando certos eventos ocorrem. Por exemplo,
*"uma função X é executada sempre que um dado link é clicado"*.

Existem algumas formas de atribuir uma função a um dado evento:

```html
<!-- Evento como atributo -->
<h1 onclick="this.innerHTML = 'Ooops!'">Click on this text!</h1>

<h1 onclick="funcaoX()">Click on this text!</h1>

<body onload="funcaoW()">
```

```javascript
// Evento como atributo de elemento do DOM
document.getElementById("myBtn").onclick = displayDate;
```

Para atribuirmos mais de uma função a um mesmo evento, utilizamos
`eventListeners`:

```javascript
// A função displayDate será executada quando o elemento de id = "myBtn" for clicado
// Note que, nesse caso, não existe o "on" no nome do evento
document.getElementById("myBtn").addEventListener("click", displayDate);
```

Eventlisteners são úteis para separar o código HTML do código Javascript.

```javascript
// Exemplo com objeto event (ev)
var f = function(ev){
  ev.target.style.color = "red";
};

document.getElementById("demo").addEventListener("click", f);

// Atribuir o evento a todos os paragrafos do documento
var ps = document.getElementsByTagName("p");

for (var i = 0; i < ps.length; i++) {
  ps[i].addEventListener("click",f);
}
```

### Links interessantes

* [Document](https://www.w3schools.com/js/js_htmldom_document.asp);
* [Elementos](https://www.w3schools.com/js/js_htmldom_elements.asp);
* [Modificando HTML](https://www.w3schools.com/js/js_htmldom_html.asp);
* [Modificando CSS](https://www.w3schools.com/js/js_htmldom_css.asp);
* [Eventos](https://www.w3schools.com/js/js_htmldom_events.asp);
* [Event listeners](https://www.w3schools.com/js/js_htmldom_eventlistener.asp);
* [Navegação](https://www.w3schools.com/js/js_htmldom_navigation.asp);
* [Nodes](https://www.w3schools.com/js/js_htmldom_nodes.asp).
