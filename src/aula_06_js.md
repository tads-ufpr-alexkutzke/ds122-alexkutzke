# Aula 06 - Javascript

## Bibliografia recomendada para o tema:
* [MDN - JS](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript);
* [w3schools - JS](https://www.w3schools.com/js/default.asp);
* [Eloquent Javascript](https://github.com/braziljs/eloquente-javascript) - Ótimo livro sobre
o tema (agora em português!);

## Javascript

Javascript é uma linguagem de programação originalmente projetada
para dar maiores possibilidades à paginas WEB. O primeiro navegador
a implementá-la foi o Netscape, em meados de 1995.
A intenção era que as páginas web pudessem rodar pequenos programas
no próprio navegador.

Depois de sua larga adoção por outros navegadores,
uma referência padrão para a linguagem foi escrita pela
Organização Internacional ECMA. Dessa forma, os navegadores
poderiam implementar uma versão em comum do Javascript e os programas
escritos nessa linguagem seriam executados sem dificuldades por qualquer
navegador. Esse padrão se chama ECMAScript e é, desde então, o nome
mais **oficial**, e menos conhecido, do Javascript. Atualmente,
a maioria dos navegadores já é compatível com a versão 5 do ECMAScript,
e estão em processo de preparação para a versão 6. Em geral,
pode-se utilizar o termo **Javascript** ao invés de **ECMAScript** sem
maiores problemas.

Embora o nome possa sugerir, o Javascript não possui nenhuma relação
com a linguagem de programação Java. É uma caso de escolha infeliz de um nome.
Ao tempo em que o Javascript estava sendo produzido, a linguagem Java
estava em evidência no mercado. Por essa razão, os produtores do Javascript
pensaram ser uma boa jogada de *marketing* relacionar os nomes das duas
linguagens. Porém, as semelhanças acabam por aí.

Nos dias de hoje, o Javascript possui um papel muito importante nas
aplicações Web. É utilizado na manipulação dos elementos da própria
página Web, até em comunicações HTTP assíncronas.

Além disso, o Javascript passou a ser utilizado em aplicações que rodam
**fora de navegadores**. Exemplo disso é a grande popularidade do **Runtime** de
Javascript chamado **Node.js**. Com o Node.js, é possível escrever
as mais variadas aplicações em Javascript nativo, desde comandos de
terminais até aplicações robustas de cliente-servidor.

Exemplo de código Javascript:

```js
function soma(a,b){
    return(a+b);
}

console.log(soma(2,10));
// -> 10
```

### Utilizando JS em páginas Web

Para incluir um código JS em um arquivo HTML, temos duas opções:

1. Incluir o código JS diretamente no HTML;
2. Utilizar um arquivo JS externo e referenciá-lo no HTML.

Ambas as opções acima utilizam a tag `<script>`. Por exemplo,
para incluir o código script diretamente no HTML:

```html
<!DOCTYPE html>
<html>
<head>
  <script>
    function myFunction() {
        document.getElementById("demo").innerHTML = "Paragraph changed.";
    }

    console.log("ola mundo!");
  </script>
</head>

<body>
  <h1>A Web Page</h1>
  <p id="demo">A Paragraph</p>
  <button type="button" onclick="myFunction()">Try it</button>
</body>
</html>
```

E, para utilizar um arquivo externo, suponha o seguinte arquivo JS:

```js
// teste.js
console.log("ola mundo");
```

```html
<!DOCTYPE html>
<html>
<head>
  <script src="teste.js"></script>
</head>

<body>
  <h1>A Web Page</h1>
  <p id="demo">A Paragraph</p>
</body>
</html>
```

As duas opções podem ser utilizadas tanto dentro da tag `<head>` quanto da
tag `<body>`. Existe uma tendência de adicionar códigos JS ao final da tag
`<body>`. Dessa forma, a página pode ter um carregamento mais rápido, principalmente
no caso de existirem scripts JS complexos. Entretanto, não existe uma regra
que determine onde um JS deve ser incluído no arquivo HTML.

Vale salientar o uso do comando `console.log`. Através dele é possível exibir
mensagens no terminal de comandos (console) do navegador (acesse-o pelas ferramentas
  de desenvolvimento do seu navegador).

### Exemplos simples com laços

```js
let i;

for(i=0;i<10;i++){
    console.log(i);
}

i = 0;
while(i<10){
    if(i % 2 == 0)
      console.log(i);
    i++;
}
```

Vale salientar que o JS possui tipagem dinâmica:

```js
let i;
i = 10;
i = 12.34;
i = "Teste";
i = [1,2,"as",12];
```

### Funções

Talvez o recurso que produza maior confusão aos iniciantes em JS seja o conceito
de **função** desta linguagem. Segue um exemplo simples:

```js
function square(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

Entretanto, o mesmo exemplo pode ser reescrito da seguinte maneira:

```js
var square = function(x) {
  return x * x;
};

console.log(square(12));
// → 144
```

No exemplo acima, pode-se notar dois conceitos. O primeiro é o que chamamos
de **função anônima**. Trata-se da declaração de uma função sem nome (perceba que
não existe nome entre `function` e `(x)`). Funções anônimas são um recurso muito
utilizado em JS, pois é bastante útil quando relacionado ao segundo conceito
exposto no código acima: atribuição de funções à variáveis.

Em JS, uma função é, na verdade, um tipo de dado (tipo `function`). Esse tipo
de dado armazena uma sequência de código (código da função) e pode ser armazenado
em variáveis. Uma variável que armazena uma função, pode ser executada posteriormente,
como no exemplo acima. Basta se utilizar do operador `()`. Mais exemplos:

```js
var makeNoise = function() {
  console.log("Pling!");
};

makeNoise();
// → Pling!

const power = function(base, exponent) {
  let result = 1;
  for (let count = 0; count < exponent; count++)
    result *= base;
  return result;
};

console.log(power(2, 10));
// → 1024
```

Outro conceito importante em JS é o escopo de variáveis. O escopo define em quais
partes do código uma dada variável estará disponível. O escopo de uma variável
depende de onde ela foi declarada.

```js
let x = "outside";

const f1 = function() {
  let x = "inside f1";
};
f1();
console.log(x);
// → outside

const f2 = function() {
  x = "inside f2";
};
f2();
console.log(x);
// → inside f2
```

Variáveis não declaradas com o operador `var` serão consideradas Globais.

### Tipos de dados

Em JS existem apenas 5 tipos de dados que podem armazenar valores:

* string
* number
* boolean
* object
* function

E 2 tipos de dados que não armazenam valores:

* null
* undefined


```js
typeof "John"                 // Returns "string"
typeof 3.14                   // Returns "number"
typeof NaN                    // Returns "number"
typeof false                  // Returns "boolean"
typeof [1,2,3,4]              // Returns "object"
typeof {name:'John', age:34}  // Returns "object"
typeof new Date()             // Returns "object"
typeof function () {}         // Returns "function"
typeof myCar                  // Returns "undefined" *
typeof null                   // Returns "object"
```

Cuidado, o JS faz conversões automáticas de tipo quando necessário. Mais
informações em https://www.w3schools.com/js/js_type_conversion.asp

#### Exemplos com Array

```js
let listOfNumbers = [2, 3, 5, 7, 11];
console.log(listOfNumbers[1]);
// → 3
console.log(listOfNumbers[1 - 1]);
// → 2
```

```js
function pares(n){
  let result = [];
  let x = 0, i;

  for(i=0; i<n; i++){
    result.push(x);
    x+=2;
  }
  return(result);
}

console.log(pares(10));
// -> (10) [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

i = 0;
while(i<10){
    if(i % 2 == 0)
      console.log(i);
    i++;
}
```

### Links importantes

* [Possibilidades](https://www.w3schools.com/js/default.asp)
* [Introdução](https://www.w3schools.com/js/js_intro.asp)
* [Onde adicionar ao HTML](https://www.w3schools.com/js/js_whereto.asp)
* [Exibindo conteúdo](https://www.w3schools.com/js/js_output.asp)
* [Tipos de dados](https://www.w3schools.com/js/js_datatypes.asp)
* [Funções](https://www.w3schools.com/js/js_functions.asp)
* [Objects](https://www.w3schools.com/js/js_objects.asp)
* [Escopo de variáveis](https://www.w3schools.com/js/js_scope.asp)
* [Conversão de tipos](https://www.w3schools.com/js/js_type_conversion.asp)
* [Debugging](https://www.w3schools.com/js/js_debugging.asp)
* [JS hoisting](https://www.w3schools.com/js/js_hoisting.asp)
* [Convenções](https://www.w3schools.com/js/js_conventions.asp)
* [Best Practices](https://www.w3schools.com/js/js_best_practices.asp)
* [Erros comuns](https://www.w3schools.com/js/js_mistakes.asp)
* [JSON](https://www.w3schools.com/js/js_json.asp)
* [Definição de Funções](https://www.w3schools.com/js/js_function_definition.asp)
* [Parâmetros de Funções](https://www.w3schools.com/js/js_function_parameters.asp)
* [Invocação de Funções](https://www.w3schools.com/js/js_function_invocation.asp)

### Closures (apenas como curiosidade)

Considere o seguinte código:

```js
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

let twice = multiplier(2);
console.log(twice(5));
// → 10
```

Perceba que a função `multiplier` retorna uma outra função. Porém, lembre-se
que funções podem ser salvas em variáveis. E é exatamente isso que acontece
na variável `twice`. A função retornada pela função `multiplier` é salva na
variável `twice`, a qual é executada logo na sequência. Legal, não?

Consegue compreender o seguinte código?

```js
const add = (function () {
    let counter = 0;
    return function () {return counter += 1;}
})();

add();
add();
add();

// the counter is now 3
```

Mais informações em: https://www.w3schools.com/js/js_function_closures.asp

