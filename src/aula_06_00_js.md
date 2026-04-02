# Aula 06 - Programação Cliente (JavaScript Moderno)

## Bibliografia recomendada

  * [MDN Web Docs - JavaScript](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript)
  * [w3schools - JavaScript Tutorial](https://www.w3schools.com/js/default.asp)
  * [Eloquent JavaScript](https://github.com/braziljs/eloquente-javascript) - Livro de referência sobre a linguagem.

-----

## JavaScript e ECMAScript

JavaScript é uma linguagem de programação interpretada, de tipagem dinâmica fraca e multiparadigma. Originalmente projetada para manipulação de páginas web no lado do cliente (navegador), sua aplicação foi expandida para o lado do servidor com o surgimento do **Node.js** e outros runtimes (como Deno e Bun).

### Versões da Linguagem (O Padrão ECMAScript)

A linguagem JavaScript é padronizada pela organização ECMA International sob a especificação chamada **ECMAScript (ES)**.

  * **ES5 (2009):** Versão legada, suportada por navegadores antigos.
  * **ES6 ou ES2015:** O marco de modernização da linguagem. Introduziu uma nova sintaxe e recursos fundamentais como `let`, `const`, Arrow Functions, Classes, Promises e Template Strings.
  * **ES7+ (Atualizações anuais):** A partir de 2015, o comitê adotou um ciclo de lançamento anual (ES2016, ES2017, etc.), adicionando melhorias incrementais.

No desenvolvimento atual, utiliza-se a sintaxe moderna (ES6+).

-----

## Utilizando JS em páginas Web

A inclusão de código JavaScript no HTML ocorre através da tag `<script>`. A prática moderna recomenda o uso de arquivos externos com o atributo `defer`.

### Arquivo Externo com `defer` (Recomendado)

O atributo `defer` instrui o navegador a carregar o script em paralelo com o HTML, mas sua execução só ocorre após a renderização completa do DOM. Isso elimina a necessidade de colocar a tag `<script>` no final do `<body>`.

**Arquivo HTML (`index.html`):**

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Exemplo JS</title>
    <script src="app.js" defer></script>
</head>
<body>
    <h1>Página Web</h1>
    <p id="demo">Um parágrafo.</p>
    <button id="btnAcao">Clique aqui</button>
</body>
</html>
```

**Arquivo JS (`app.js`):**

```javascript
// Boas práticas recomendam separar o comportamento (JS) da estrutura (HTML).
// Evite atributos como onclick no HTML.

document.getElementById('btnAcao').addEventListener('click', () => {
    document.getElementById('demo').innerHTML = "Parágrafo alterado.";
});

console.log("Arquivo carregado e DOM pronto!");
```

-----

## Variáveis e Escopo (ES6+)

No JavaScript moderno, o uso da palavra-chave `var` é **obsoleto** devido a problemas com o escopo de função e "hoisting" (elevação). Utilize estritamente `let` e `const`, que respeitam o **escopo de bloco**.

  * **`const`:** Declara uma constante. O valor (ou a referência de memória para objetos/arrays) não pode ser reatribuído. Padrão preferencial.
  * **`let`:** Declara uma variável que pode sofrer reatribuição.

<!-- end list -->

```javascript
// Escopo de Bloco
let x = "escopo global";

if (true) {
    let x = "escopo de bloco (if)";
    const y = "constante local";
    console.log(x); // → escopo de bloco (if)
}

console.log(x); // → escopo global
// console.log(y); // Erro de referência: y não está definido
```

**Nota:** A declaração implícita de variáveis (sem `let` ou `const`) cria variáveis globais não intencionais, o que é um erro em escopos modernos (especialmente usando `"use strict";`).

-----

## Tipos de Dados

O JavaScript possui tipagem dinâmica. A mesma variável pode armazenar diferentes tipos de dados em momentos distintos de sua execução.

**Tipos Primitivos:**

  * `String`: Textos.
  * `Number`: Números (inteiros e de ponto flutuante, incluindo `NaN`).
  * `Boolean`: `true` ou `false`.
  * `Undefined`: Variável declarada, mas sem valor atribuído.
  * `Null`: Ausência intencional de valor.
  * `Symbol`: Identificador único (ES6).
  * `BigInt`: Números inteiros além do limite de precisão do `Number`.

**Tipos Estruturais (Por referência):**

  * `Object`: Estruturas de chave/valor, Arrays, Datas.
  * `Function`: Um subtipo de objeto invocável.

<!-- end list -->

```javascript
typeof "João";       // "string"
typeof 3.14;         // "number"
typeof false;        // "boolean"
typeof undefined;    // "undefined"
typeof null;         // "object" (Bug??)
typeof [1, 2, 3];    // "object" (Arrays são objetos no JS)
```

-----

## Strings Modernas: Template Literals (Template Strings)

No ES6+, utiliza-se crases (`` ` ``) para manipulação de strings. Isso permite interpolação de variáveis e expressões diretamente na string, além de quebras de linha nativas.

```javascript
const nome = "Maria";
const idade = 25;

// Concatenação legada
console.log("O nome dela é " + nome + " e ela tem " + idade + " anos.");

// Interpolação moderna (Template String)
console.log(`O nome dela é ${nome} e ela tem ${idade} anos.`);

// Expressões matemáticas dentro de strings
console.log(`Daqui a 5 anos, ela terá ${idade + 5} anos.`);
```

-----

## Funções

Funções em JavaScript são "cidadãs de primeira classe", podendo ser armazenadas em variáveis, passadas como argumentos ou retornadas por outras funções.

### 1\. Declaração Tradicional

Sofre *hoisting*, permitindo sua invocação antes da linha de declaração no código.

```javascript
function quadrado(x) {
    return x * x;
}
```

### 2\. Expressão de Função (Função Anônima)

Armazenada em uma variável. Não sofre *hoisting*.

```javascript
const quadrado = function(x) {
    return x * x;
};
```

### 3\. Arrow Functions (ES6+)

Sintaxe concisa para funções anônimas. Além da sintaxe reduzida, Arrow Functions **não** possuem seu próprio escopo `this` (herdam do contexto pai).

```javascript
// Sintaxe completa
const multiplicar = (a, b) => {
    return a * b;
};

// Retorno implícito (se houver apenas uma linha)
const somar = (a, b) => a + b;

// Parênteses opcionais com apenas um parâmetro
const dobro = x => x * 2;

console.log(somar(5, 5)); // → 10
console.log(dobro(4));    // → 8
```

-----

## Arrays e Iteração

Arrays armazenam coleções ordenadas de dados.

```javascript
const lista = [2, 3, 5, 7, 11];
console.log(lista[1]); // → 3
```

Abaixo, a evolução das estruturas de laços para iteração sobre arrays:

```javascript
const frutas = ["Maçã", "Banana", "Laranja"];

// 1. Laço tradicional
for (let i = 0; i < frutas.length; i++) {
    console.log(frutas[i]);
}

// 2. Laço moderno: for...of (ES6)
for (const fruta of frutas) {
    console.log(fruta);
}

// 3. Método forEach (Abordagem Funcional)
frutas.forEach(fruta => console.log(fruta));
```

-----

## Closures (Aprofundamento)

O JavaScript suporta funções de fechamento (*closures*). Uma closure é uma função que "lembra" do ambiente em que foi criada, ou seja, tem acesso às variáveis de seu escopo pai mesmo após a execução da função pai ter terminado.

```javascript
const multiplicador = (fator) => {
    return (numero) => {
        return numero * fator;
    };
};

// 'dobro' armazena a função interna, com o 'fator' preservado como 2
const dobro = multiplicador(2);
const triplo = multiplicador(3);

console.log(dobro(5));  // → 10
console.log(triplo(5)); // → 15
```

-----

## Links Importantes e Documentação

  * [Introdução ao JS - MDN](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Introduction)
  * [Tipos e Estruturas de Dados](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Data_structures)
  * [Funções e Arrow Functions](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Functions)
  * [Trabalhando com Objetos](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Working_with_Objects)
  * [Arrays e Métodos Iteradores](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array)
  * [Fetch API (Consumo de APIs - Conteúdo Futuro)](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API/Using_Fetch)
