# JavaScript: sintaxe moderna (ES6+)

[Slides desta aula (PDF)](slides/aula_06_js.pdf)

## Bibliografia recomendada para o tema

* [MDN - JavaScript](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript);
* [MDN - Guia de JavaScript](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide);
* [MDN - Referência de Array](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array);
* [JavaScript Eloquente, 3a edição, em português](https://github.com/braziljs/eloquente-javascript);
* [javascript.info, em inglês](https://javascript.info/);
* FLANAGAN, David. **JavaScript: o guia definitivo**. Porto Alegre: Bookman, 2012 (bibliografia complementar da disciplina).

> As três páginas do catálogo já estão prontas e estilizadas. Até aqui elas
> mostram sempre o mesmo conteúdo, escrito à mão no HTML. O JavaScript é a
> terceira camada: ele roda no navegador de quem visita e muda o que está na
> tela sem pedir outra página ao servidor.

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Executar uma expressão no console do navegador e distinguir, na saída, o que
   foi impresso do valor que a expressão devolveu;
2. Ligar um arquivo `.js` externo a uma página com `defer` e confirmar que ele
   executou;
3. Escolher entre `const` e `let`, e dizer por que `var` ficou de fora;
4. Prever o resultado de uma comparação com `==` e com `===`, e explicar a
   diferença pela conversão de tipo;
5. Escrever uma função nas três formas usadas hoje (declaração, expressão e
   arrow function) e converter uma na outra;
6. Percorrer um array com `for...of` e com `forEach`, e dizer quando cada um
   cabe;
7. Produzir um novo array a partir de outro com `map` e com `filter`, e um valor
   único com `reduce`;
8. Escrever um array de objetos que represente os produtos do seu catálogo e
   extrair dele um relatório;
9. Ler uma mensagem de erro do console, identificar o tipo do erro e a linha, e
   chegar à causa.

---

## Material de exemplo

Os exemplos desta aula usam o `catalogo.html` do
[pacote de exemplo desta aula](./exemplos/ds122_aula_06_ponto_de_partida.zip),
que traz as três páginas da loja com o layout pronto e sem nenhuma linha de
JavaScript. Baixe e descompacte o pacote antes de começar: a parte teórica usa
essas páginas para testes no console, e a parte prática parte delas.

# Parte teórica

## 1. O console

Abra o `catalogo.html` no navegador e aperte `F12`. É o mesmo painel que você usou
para inspecionar CSS, agora na aba **Console**.

Clique na linha do `>` e digite:

```javascript
2 + 3
```

Aperte `Enter`. Aparece `5`. O console é um interpretador de JavaScript ligado à
página aberta: ele lê o que você escreve, executa e mostra o valor obtido.

Agora digite, uma linha por vez:

```javascript
"Loja" + " " + "Exemplo"
document.title
document.querySelectorAll("article.produto").length
```

A última linha responde quantos elementos `<article class="produto">` existem na
página aberta. No `catalogo.html` do pacote de exemplo, a resposta é `6`.

Três coisas apareceram e ainda não têm nome:

* o código que você digitou tinha acesso ao conteúdo da página, sem que nada
  tivesse sido salvo em arquivo;
* `document` existia sem você ter declarado;
* `.length` e `querySelectorAll` vieram junto com `document`, um deles um valor e
  o outro uma função.

O acesso à página tem nome, DOM, e é o assunto do próximo encontro. Hoje o
console serve como calculadora: uma linha, um resultado, sem recarregar nada.

> Duas teclas que economizam tempo no console: a seta para cima repete o último
> comando, e `Shift+Enter` quebra a linha sem executar, o que permite escrever
> várias linhas de uma vez.

## 2. Onde o JavaScript roda

JavaScript é uma linguagem interpretada, de tipagem dinâmica, criada em 1995
para rodar dentro do navegador. Cada navegador traz um motor que executa esse
código: V8 no Chrome e no Edge, SpiderMonkey no Firefox, JavaScriptCore no
Safari.

Desde 2009, o mesmo motor V8 também roda fora do navegador, no Node.js, o que
levou a linguagem para o servidor. Neste semestre o servidor é PHP, e o
JavaScript fica no lado do cliente.

### 2.1. ECMAScript e o que significa ES6

A linguagem é padronizada pela ECMA International com o nome **ECMAScript**.
"JavaScript" é o nome da implementação que os navegadores entregam; ECMAScript é
o nome da especificação.

A versão **ES6**, também chamada ES2015, reorganizou a linguagem: trouxe `let`,
`const`, arrow functions, template strings, classes e promessas (_promises_). Desde então sai
uma versão por ano, com acréscimos menores.

O que se escreve hoje é ES6 ou posterior, e é isso que este material chama de
sintaxe moderna. Código anterior a 2015 aparece bastante em tutorial antigo e em
resposta de fórum, com `var` e `function` em lugares onde hoje se usa outra
coisa. Saber reconhecer o estilo antigo evita copiar o que já foi substituído.

## 3. O script na página

Do mesmo modo que o CSS, o JavaScript entra na página pela tag própria. Três
formas, em ordem crescente de preferência:

```html
<!-- 1. No atributo do elemento. Não use. -->
<button onclick="alert('oi')">Clique</button>

<!-- 2. Dentro da página. Serve para teste rápido. -->
<script>
  console.log("rodou");
</script>

<!-- 3. Arquivo externo. É o que usamos. -->
<script src="js/app.js" defer></script>
```

A separação entre estrutura (HTML), aparência (CSS) e comportamento (JavaScript)
é a mesma razão que levou a escrevermos CSS em um arquivo separado: o comportamento
fica em um arquivo, versionado e reaproveitado por várias páginas.

### 3.1. Por que `defer`

Quando o navegador encontra um `<script src="...">` sem atributo nenhum, ele para
de montar a página, baixa o arquivo, executa, e só então continua. Como o script
executa antes do resto da página existir, qualquer tentativa de mexer em um
elemento que vem abaixo dele falha.

O atributo `defer` resolve os dois problemas de uma vez: o arquivo é baixado em
paralelo com a leitura do HTML, e a execução fica para depois que a página
inteira estiver montada.

```html
<head>
  <meta charset="utf-8">
  <title>Loja Exemplo - Catálogo</title>
  <link rel="stylesheet" href="css/estilo.css">
  <script src="js/app.js" defer></script>
</head>
```

Com `defer`, o `<script>` pode ficar no `<head>`. A prática antiga de colocar a
tag na última linha do `<body>` resolvia o mesmo problema sem o atributo, e ainda
aparece em muito código.

### 3.2. Confirmar que o arquivo carregou

Erro de caminho em `src` não aparece na tela: a página abre normalmente, apenas
sem comportamento nenhum. Duas conferências, nesta ordem:

1. a primeira linha de `js/app.js` é um `console.log("app.js carregou")`, e a
   mensagem aparece no console ao recarregar;
2. na aba **Network** do painel, com a página recarregada, existe uma linha para
   `app.js` com status `200`.

Se a mensagem não aparecer e a linha da aba Network mostrar `404`, o caminho do
`src` está errado.

## 4. Valores e tipos

JavaScript tem tipagem dinâmica: o tipo pertence ao valor, não à variável. A
mesma variável pode guardar um número agora e um texto na linha seguinte, e o
interpretador aceita.

Os valores primitivos que aparecem no dia a dia:

| Tipo | Exemplo | `typeof` devolve |
|---|---|---|
| `number` | `42`, `3.14`, `-7` | `"number"` |
| `string` | `"Café"`, `'Café'`, `` `Café` `` | `"string"` |
| `boolean` | `true`, `false` | `"boolean"` |
| `undefined` | variável declarada sem valor | `"undefined"` |
| `null` | ausência de valor, colocada por você | `"object"` |

Existem ainda `symbol` e `bigint`, que não aparecem nesta disciplina.

Fora dos primitivos ficam os objetos, e arrays e funções são objetos:

```javascript
typeof "Café"      // "string"
typeof 3.14        // "number"
typeof false       // "boolean"
typeof undefined   // "undefined"
typeof [1, 2, 3]   // "object"
typeof null        // "object"
```

As duas últimas linhas podem confundir. `typeof [1, 2, 3]` devolve `"object"`
porque array é um tipo de objeto. Para perguntar se um valor é array, existe
`Array.isArray(valor)`.

`typeof null` devolver `"object"` é um defeito da primeira versão da linguagem,
de 1995, nunca corrigido porque corrigir quebraria código existente. Aceite como
excentricidade e siga.

### 4.1. `undefined` e `null`

Os dois representam ausência de valor, com origens diferentes:

* `undefined` é o que o interpretador coloca: variável declarada e não
  inicializada, propriedade que não existe no objeto, função sem `return`;
* `null` é o que você coloca, para dizer que ali não há valor por decisão do
  programa.

```javascript
let preco;
console.log(preco);            // undefined

const produto = { nome: "Café" };
console.log(produto.desconto); // undefined, a propriedade não existe

let selecionado = null;        // ninguém selecionado por enquanto
```

## 5. `const`, `let` e escopo de bloco

Toda variável precisa ser declarada antes do uso, e há duas palavras para isso.

**`const`** cria um nome que não pode ser reatribuído. É a escolha padrão: use
`const` até que o programa precise trocar o valor.

**`let`** cria um nome que pode receber outro valor depois. É o caso de
contadores, acumuladores e de qualquer coisa que muda durante a execução.

```javascript
const taxa = 0.1;
let total = 0;

total = total + 100;   // válido
// taxa = 0.2;         // TypeError: Assignment to constant variable.
```

### 5.1. `const` com array e objeto

Este é, talvez, o mal-entendido mais comum. `const` protege a **ligação entre o nome e
o valor**, não o conteúdo do valor.

```javascript
const carrinho = ["café", "chá"];

carrinho.push("mate");     // válido: o array mudou, o nome continua o mesmo
console.log(carrinho);     // ["café", "chá", "mate"]

// carrinho = ["outro"];   // TypeError: o nome não pode apontar para outro array
```

Um array declarado com `const` continua sendo alterável por `push`, `pop` e
atribuição por índice. O que `const` impede é trocar o array inteiro por outro.

### 5.2. Escopo de bloco

Um bloco é qualquer trecho entre chaves: corpo de `if`, de `for`, de função. Nome
declarado com `const` ou `let` dentro de um bloco existe só dentro dele.

```javascript
let x = "fora";

if (true) {
  let x = "dentro";
  console.log(x);     // dentro
}

console.log(x);       // fora
```

### 5.3. Por que `var` ficou de fora

Antes do ES6 havia apenas `var`, com dois comportamentos que produziam erro
difícil de achar:

```javascript
function testa() {
  for (var i = 0; i < 3; i++) {
    // ...
  }
  console.log(i);   // 3, o i sobreviveu ao for
}
```

`var` tem escopo de função, e não de bloco, então o contador vaza para fora do
laço. Além disso, `var` sofre *hoisting*: a declaração é tratada como se
estivesse no topo da função, e ler a variável antes da linha em que ela aparece
devolve `undefined` em vez de erro.

Escreva `const` por padrão e `let` quando precisar reatribuir. `var` aparece
neste material só para você reconhecer em código alheio.

> Atribuir a um nome que nunca foi declarado (`preco = 10;` sem `const` nem
> `let`) cria uma variável global silenciosa. Em módulos e em modo estrito isso
> vira erro, e é assim que deve ser tratado.

## 6. Strings e template literals

Aspas simples e duplas são equivalentes em JavaScript, portanto, escolha por estilo.
A crase abre a terceira forma, o **template literal**, que aceita interpolação e
quebra de linha:

```javascript
const nome = "Café em grão";
const preco = 32.5;

// concatenação, o jeito antigo
console.log("O " + nome + " custa R$ " + preco + ".");

// template literal, com ${} para cada expressão
console.log(`O ${nome} custa R$ ${preco}.`);

// a expressão dentro de ${} pode ser qualquer uma
console.log(`Com 10% de desconto: R$ ${preco * 0.9}.`);

// e o texto pode ocupar várias linhas
const cartao = `
  Produto: ${nome}
  Preço:   ${preco}
`;
```

Métodos de string que aparecem no trabalho:

```javascript
const termo = "  Café Em Grão  ";

termo.trim()                  // "Café Em Grão", sem os espaços das pontas
termo.trim().toLowerCase()    // "café em grão"
termo.length                  // 16, contando os espaços
"café em grão".includes("grão")   // true
"café em grão".startsWith("café") // true
```

Strings são imutáveis: nenhum desses métodos altera a original, todos devolvem
uma string nova. Por isso `termo.trim()` sozinho não resolve nada, o resultado
precisa ser guardado ou usado na mesma expressão.

Para a busca do catálogo, a combinação `toLowerCase()` com `includes()` é uma
estratégia para ignorar maiúsculas:

```javascript
const nomeProduto = "Café em grão";
const busca = "CAFÉ";

nomeProduto.toLowerCase().includes(busca.toLowerCase());   // true
```

## 7. Números

Existe um único tipo numérico, `number`, usado tanto para inteiro quanto para
fracionário. Ele é um ponto flutuante de 64 bits, e isso tem uma consequência
visível:

```javascript
0.1 + 0.2        // 0.30000000000000004
```

Não é defeito do JavaScript. A mesma conta dá o mesmo resultado em C, em Java e
em Python, porque 0,1 não tem representação exata em base 2. Ao representar valores
monetários, como R$, o contorno é arredondar na hora de mostrar ou salvar sempre
em centavos.

```javascript
const preco = 32.5;
const total = preco * 3;

total                          // 97.5
total.toFixed(2)               // "97.50", uma string, já arredondada
Number(total.toFixed(2))       // 97.5, de volta a número
```

Para exibir valor em real, existe a formatação por localidade:

```javascript
const preco = 1890;

preco.toLocaleString("pt-BR", { style: "currency", currency: "BRL" });
// "R$ 1.890,00"
```

### 7.1. Texto que vira número

Campo de formulário devolve sempre string, inclusive `<input type="number">`.
Somar sem converter concatena:

```javascript
"10" + 5        // "105", o + com uma string do lado vira concatenação
"10" - 5        // 5, o - não existe para strings, então o "10" vira número

Number("10")      // 10
Number("10,5")    // NaN, a vírgula não é separador decimal aqui
Number("")        // 0, cuidado com campo vazio
parseFloat("10.5 reais")   // 10.5, lê enquanto der e para
parseInt("42px")           // 42
```

`NaN` (*Not a Number*) é o valor devolvido quando uma conta numérica não tem
resultado. Ele é do tipo `number` e tem uma propriedade estranha: não é igual a
si mesmo.

```javascript
NaN === NaN            // false
Number.isNaN(valor)    // a forma correta de testar
```

## 8. Comparação e conversão

Há dois operadores de igualdade, e a escolha entre eles é fonte constante de
erro.

`===` compara valor e tipo. `==` converte antes de comparar, seguindo regras
específicas:

```javascript
1 == "1"        // true, o "1" vira 1 antes da comparação
1 === "1"       // false, tipos diferentes

0 == false      // true
0 === false     // false

null == undefined    // true
null === undefined   // false
```

Use `===` e `!==` sempre. O `==` aparece aqui para você reconhecer em código
alheio e desconfiar dele. :)

### 8.1. Valores falsos

Em `if`, `while` e nos operadores lógicos, qualquer valor é convertido para
booleano. Seis valores viram `false`, e são os únicos:

```javascript
false
0
""          // string vazia
null
undefined
NaN
```

Todo o resto vira `true`, inclusive `"0"`, `"false"`, `[]` e `{}`. Array vazio
dentro de um `if` entra no bloco, porque array vazio é um objeto.

```javascript
const busca = "";

if (busca) {
  // não entra aqui: string vazia é valor falso
}

if (busca.trim() !== "") {
  // forma explícita, que diz o que está sendo testado
}
```

## 9. Decisão e repetição

A sintaxe de controle de fluxo é a mesma de C, Java e PHP, com chaves e
parênteses.

```javascript
const preco = 32.5;

if (preco > 100) {
  console.log("caro");
} else if (preco > 20) {
  console.log("médio");
} else {
  console.log("barato");
}
```

O operador ternário resolve a escolha entre dois valores dentro de uma
expressão:

```javascript
const frete = preco > 100 ? 0 : 15;
```

Os operadores lógicos são `&&` (e), `||` (ou) e `!` (não):

```javascript
if (preco >= 20 && preco <= 50) {
  console.log("dentro da faixa");
}
```

Para repetição, três formas convivem:

```javascript
const frutas = ["café", "chá", "mate"];

// 1. for clássico, quando o índice importa
for (let i = 0; i < frutas.length; i++) {
  console.log(i, frutas[i]);
}

// 2. for...of, quando só o valor importa
for (const fruta of frutas) {
  console.log(fruta);
}

// 3. while, quando o número de repetições não é conhecido de antemão
let restante = 3;
while (restante > 0) {
  restante = restante - 1;
}
```

Repare no `let i` do primeiro laço e no `const fruta` do segundo. O contador
precisa ser reatribuído a cada volta, então é `let`. A variável do `for...of`
recebe um valor novo a cada volta, e por isso pode ser `const`.

Existe também `for...in`, que percorre as **chaves** de um objeto. Usá-lo em
array devolve os índices como strings, o que quase nunca é o desejado. Para
array, `for...of` ou `forEach`.

`forEach` é um método do próprio array: ele recebe uma função e a chama uma vez
para cada elemento. O `for...of` acima, escrito com `forEach`, fica assim:

```javascript
frutas.forEach(fruta => {
  console.log(fruta);
});
```

O trecho `fruta => { ... }` é uma arrow function, assunto da seção 10.3, e o
`forEach` volta com mais detalhes na seção 11.1. Para escolher entre os dois:
quando o laço precisa parar no meio, com `break`, use `for...of`, porque o
`forEach` sempre percorre o array inteiro.

## 10. Funções

Função é um bloco de código com nome, parâmetros e um valor de retorno. Há três
formas de escrever, e as três produzem funções que se chamam do mesmo jeito.

### 10.1. Declaração

```javascript
function calculaTotal(preco, quantidade) {
  return preco * quantidade;
}

calculaTotal(32.5, 3);   // 97.5
```

### 10.2. Expressão de função

A função fica sem nome e é guardada em uma variável:

```javascript
const calculaTotal = function (preco, quantidade) {
  return preco * quantidade;
};
```

### 10.3. Arrow function

Forma introduzida no ES6, e a mais usada hoje para funções curtas:

```javascript
// forma completa
const calculaTotal = (preco, quantidade) => {
  return preco * quantidade;
};

// corpo de uma expressão só: as chaves e o return somem juntos
const calculaTotal = (preco, quantidade) => preco * quantidade;

// um parâmetro só: os parênteses são opcionais
const dobro = valor => valor * 2;

// nenhum parâmetro: os parênteses são obrigatórios
const agora = () => new Date();
```

O retorno implícito só existe quando o corpo é uma expressão única, sem chaves.
Escrever as chaves e esquecer o `return` devolve `undefined`, e é um dos erros
mais frequentes:

```javascript
const dobro = valor => { valor * 2 };
dobro(4);    // undefined
```

Arrow functions têm outra diferença, no tratamento de `this`, que importa dentro
de classes e de objetos com métodos. O assunto está na seção 14.

### 10.4. Parâmetro com valor padrão

```javascript
const calculaTotal = (preco, quantidade = 1) => preco * quantidade;

calculaTotal(32.5);      // 32.5
calculaTotal(32.5, 3);   // 97.5
```

Chamar uma função com menos argumentos do que os parâmetros declarados não é
erro em JavaScript: os que faltam ficam `undefined`. O valor padrão evita o
`NaN` que viria da conta com `undefined`.

### 10.5. Função como valor

Função é um valor como qualquer outro: pode ser guardada em variável, passada
como argumento e devolvida por outra função. É isso que torna possível o
`forEach` da próxima seção.

```javascript
const aplicar = (valor, operacao) => operacao(valor);

aplicar(4, dobro);              // 8
aplicar(4, valor => valor + 1); // 5
```

Uma função passada como argumento para outra é chamada de *callback*.

## 11. Arrays

Array é uma lista ordenada, com índice começando em zero, e pode guardar valores
de tipos diferentes.

```javascript
const precos = [32.5, 18, 74.9, 12];

precos[0]          // 32.5
precos.length      // 4
precos[precos.length - 1]   // 12, o último
precos[10]         // undefined, índice que não existe não dá erro
```

Métodos que alteram o próprio array:

```javascript
precos.push(50);      // acrescenta no fim
precos.pop();         // remove do fim e devolve
precos.unshift(5);    // acrescenta no começo
precos.shift();       // remove do começo e devolve
```

### 11.1. Percorrer: `forEach`

`forEach` chama uma função para cada elemento. Ela recebe o valor e, se você
pedir, o índice:

```javascript
const produtos = ["Café", "Chá", "Mate"];

produtos.forEach(produto => console.log(produto));

produtos.forEach((produto, indice) => {
  console.log(`${indice}: ${produto}`);
});
```

`forEach` não devolve nada e não pode ser interrompido no meio: `break` não
funciona dentro dele. Quando for preciso parar antes do fim, use `for...of`.

### 11.2. Transformar: `map`

`map` devolve um **array novo**, do mesmo tamanho, com o resultado da função
aplicada a cada elemento. O array original fica intacto.

```javascript
const precos = [32.5, 18, 74.9];

const comDesconto = precos.map(preco => preco * 0.9);
// [29.25, 16.2, 67.41000000000001]

precos;   // [32.5, 18, 74.9], inalterado
```

O terceiro valor saiu com a cauda de casas da seção 7. Ele só vira `67,41` na
hora de exibir, com `toFixed(2)` ou com `toLocaleString`.

### 11.3. Selecionar: `filter`

`filter` devolve um array novo com os elementos para os quais a função devolveu
um valor verdadeiro:

```javascript
const precos = [32.5, 18, 74.9, 12];

const baratos = precos.filter(preco => preco < 30);
// [18, 12]
```

A função passada a `filter` precisa devolver `true` ou `false`. Um erro comum é
escrever a condição sem `return`, dentro de chaves, e receber um array vazio,
porque `undefined` é valor falso.

### 11.4. Resumir: `reduce`

`reduce` percorre o array acumulando um único valor. Ela recebe dois argumentos:
a função que combina, e o valor inicial do acumulador.

```javascript
const precos = [32.5, 18, 74.9, 12];

const soma = precos.reduce((acumulado, preco) => acumulado + preco, 0);
// 137.4
```

A cada volta, `acumulado` é o que voltou da chamada anterior, e na primeira volta
é o `0` que foi passado como segundo argumento. Omitir o valor inicial funciona
para soma de números, e quebra para array vazio ou para acumulador de outro tipo.
Escreva sempre o valor inicial.

### 11.5. Procurar: `find` e `includes`

```javascript
const precos = [32.5, 18, 74.9];

precos.includes(18);                  // true, procura valor exato
precos.find(preco => preco > 30);     // 32.5, o primeiro que satisfaz
precos.findIndex(preco => preco > 30); // 0, a posição dele
precos.some(preco => preco > 70);     // true, existe algum?
precos.every(preco => preco > 10);    // true, todos satisfazem?
```

`find` devolve o elemento, ou `undefined` se nenhum satisfizer. `filter` devolve
sempre um array, com zero ou mais elementos.

### 11.6. Encadear

Como `map` e `filter` devolvem arrays, as chamadas se encadeiam, e a leitura é a
da esquerda para a direita:

```javascript
const precos = [32.5, 18, 74.9, 12];

const total = precos
  .filter(preco => preco < 50)     // [32.5, 18, 12]
  .map(preco => preco * 0.9)       // [29.25, 16.2, 10.8]
  .reduce((soma, preco) => soma + preco, 0);
// 56.25
```

Cada etapa cria um array intermediário. Para as dezenas de itens de um catálogo
isso não tem custo perceptível.

### 11.7. Ordenar: `sort`

`sort` altera o array original e, sem argumento, ordena convertendo tudo para
string, o que erra com números:

```javascript
const precos = [32.5, 18, 74.9, 120];

precos.sort();    // [120, 18, 32.5, 74.9], ordem de texto

// com função de comparação, do menor para o maior
precos.sort((a, b) => a - b);    // [18, 32.5, 74.9, 120]

// do maior para o menor
precos.sort((a, b) => b - a);
```

A função de comparação devolve um número negativo quando `a` vem antes, positivo
quando `b` vem antes, e zero quando tanto faz.

Para ordenar sem alterar o original, copie antes com `[...precos].sort(...)`,
construção explicada na seção 14.

## 12. Objetos

Objeto é um conjunto de pares chave e valor. Enquanto o array guarda uma lista
posicional, o objeto guarda campos com nome, e é a forma natural de representar
um produto.

```javascript
const produto = {
  id: 1,
  nome: "Café em grão",
  categoria: "Bebidas",
  preco: 32.5,
  desconto: 0.15,
  disponivel: true
};
```

O acesso a um campo tem duas formas:

```javascript
produto.nome           // "Café em grão", quando a chave é conhecida
produto["nome"];       // igual, e aceita chave com espaço ou vinda de variável

const campo = "preco";
produto[campo];        // 32.5
```

Acrescentar, alterar e remover campos é permitido a qualquer momento, inclusive
em objeto declarado com `const`:

```javascript
produto.estoque = 12;       // campo novo
produto.preco = 29.9;       // alterado
delete produto.desconto;    // removido
```

Um campo cujo valor é uma função é um **método**, e é chamado com parênteses:

```javascript
const carrinho = {
  itens: [],
  adicionar(produto) {
    this.itens.push(produto);
  }
};

carrinho.adicionar(produto);
carrinho.itens.length;    // 1
```

Dentro de um método escrito com essa sintaxe, `this` é o próprio objeto. Escrever
o método como arrow function muda esse comportamento, e é o caso tratado na seção
14.

### 12.1. Array de objetos

A combinação das duas estruturas é o formato em que dados chegam de uma API e em
que você vai guardar o catálogo:

```javascript
const produtos = [
  { id: 1, nome: "Café em grão",  categoria: "Bebidas",   preco: 32.5, desconto: 0.15 },
  { id: 2, nome: "Chá de hibisco", categoria: "Bebidas",  preco: 18,   desconto: 0    },
  { id: 3, nome: "Mel silvestre",  categoria: "Mercearia", preco: 24.9, desconto: 0.2  },
  { id: 4, nome: "Cesta de vime",  categoria: "Utensílios", preco: 74.9, desconto: 0   }
];
```

Os métodos da seção 11 valem igual, com a função recebendo o objeto inteiro:

```javascript
// nomes de todos
produtos.map(produto => produto.nome);
// ["Café em grão", "Chá de hibisco", "Mel silvestre", "Cesta de vime"]

// só as bebidas
produtos.filter(produto => produto.categoria === "Bebidas");

// valor somado do catálogo
produtos.reduce((soma, produto) => soma + produto.preco, 0);
// 150.3

// o produto de id 3
produtos.find(produto => produto.id === 3);

// os que têm algum desconto, ordenados do maior desconto para o menor
produtos
  .filter(produto => produto.desconto > 0)
  .sort((a, b) => b.desconto - a.desconto)
  .map(produto => produto.nome);
// ["Mel silvestre", "Café em grão"]
```

Para exibir, a soma passa por `toLocaleString`, que produz `R$ 150,30`.

### 12.2. Desestruturação

Extrair campos de um objeto para variáveis com o mesmo nome:

```javascript
const { nome, preco } = produto;

console.log(nome);    // "Café em grão"
console.log(preco);   // 32.5
```

Funciona também na lista de parâmetros de uma função, e é comum em código de
catálogo:

```javascript
const linha = ({ nome, preco }) => `${nome}: R$ ${preco.toFixed(2)}`;

linha(produto);   // "Café em grão: R$ 32.50"
```

Arrays também se desestruturam, por posição:

```javascript
const [primeiro, segundo] = produtos;
```

### 12.3. Objeto na saída do console

`console.log` de um objeto mostra a estrutura expansível. Para array de objetos,
`console.table` monta uma tabela com uma linha por item e uma coluna por campo:

```javascript
console.table(produtos);
```

É a forma mais rápida de conferir se o filtro que você escreveu selecionou o que
deveria.

## 13. Quando dá errado

Diferente do CSS, que ignora em silêncio o que não entende, JavaScript
interrompe a execução e escreve no console. A mensagem tem três partes úteis: o
tipo do erro, a descrição e o arquivo com a linha.

```text
Uncaught TypeError: produtos.filtrar is not a function
    at app.js:14:20
```

Os tipos que mais aparecem:

| Tipo | Significado usual |
|---|---|
| `SyntaxError` | parêntese, chave ou aspas sem fechar; o arquivo inteiro não roda |
| `ReferenceError` | nome usado sem ter sido declarado, ou erro de digitação no nome |
| `TypeError` | o valor existe, mas não é do tipo que a operação pede |

`TypeError: Cannot read properties of undefined` é o mais comum de todos. Ele diz
que você pediu um campo de algo que não existe:

```javascript
const produto = produtos.find(p => p.id === 99);   // não existe, devolve undefined
console.log(produto.nome);
// TypeError: Cannot read properties of undefined (reading 'nome')
```

A causa está uma linha acima da linha apontada, quase sempre. O `find` devolveu
`undefined` e o erro só apareceu quando alguém pediu `.nome` daquilo.

### 13.1. Erro que não acontece

Bug silencioso é mais difícil do que erro com mensagem. Quando o resultado sai
errado sem nenhum erro no console, três conferências resolvem a maioria:

1. `console.log` do valor **antes** e **depois** da operação suspeita;
2. `typeof valor` no console, para descobrir que o número era uma string;
3. o painel **Sources**, ponto de parada na linha pela margem esquerda, e a
   página recarregada: a execução para ali e o painel mostra o valor de cada
   variável naquele instante.

O terceiro item é o depurador, e ele volta com mais uso quando entrarmos em
eventos.

## 14. Para ler depois

Esta seção não é apresentada em aula e não cai na Prova 2. Ela existe para quando
você encontrar essas construções em código alheio, e nenhum exercício desta aula
depende dela.

**Espalhamento (`...`)**. Copia os elementos de um array, ou os campos de um
objeto, para dentro de outro:

```javascript
const copia = [...produtos];                  // array novo, mesmos objetos dentro
const maisUm = [...produtos, novoProduto];
const alterado = { ...produto, preco: 19.9 }; // objeto novo com um campo trocado
```

**Parâmetro resto**. A mesma notação na declaração de função recolhe os
argumentos restantes em um array:

```javascript
const somaTudo = (...valores) => valores.reduce((s, v) => s + v, 0);
somaTudo(1, 2, 3);   // 6
```

**Closure**. Uma função criada dentro de outra continua enxergando as variáveis
de onde nasceu, mesmo depois que a função externa terminou:

```javascript
const criaDesconto = (fator) => (preco) => preco * (1 - fator);

const black = criaDesconto(0.3);
black(100);   // 70
```

**`this` e arrow function**. Método escrito com `function` recebe como `this` o
objeto à esquerda do ponto na chamada. Arrow function não tem `this` próprio:
ela usa o do escopo onde foi escrita. Por isso a seção 12 escreve `adicionar()`
com a sintaxe de método, e não como arrow function.

**Módulos**. `import` e `export` dividem o código em arquivos que declaram o que
oferecem e o que consomem. Exigem `<script type="module">` e um servidor HTTP,
porque não funcionam em arquivo aberto com `file://`. É o caminho natural quando
o `app.js` cresce.

---

# Parte prática

Trabalhe sobre as suas páginas do catálogo, ou sobre o
[pacote de exemplo desta aula](./exemplos/ds122_aula_06_ponto_de_partida.zip), que
traz as três páginas com o layout já pronto, uma pasta `js/` vazia e nenhuma
linha de JavaScript. Cada passo tem um resultado observável: confira no console
antes de seguir para o próximo.

Nada do que você escrever hoje muda a página. O código roda, calcula e imprime no
console. Colocar o resultado na tela é o assunto do encontro seguinte.

## 15. O primeiro arquivo

Crie a pasta `js/` e o arquivo `js/app.js` com uma linha só:

```javascript
console.log("app.js carregou");
```

Ligue-o no `<head>` do `catalogo.html`, depois do `<link>` da folha de estilo:

```html
<script src="js/app.js" defer></script>
```

Recarregue com o console aberto. A mensagem precisa aparecer.

Agora faça o teste do erro, de propósito: troque `src="js/app.js"` por
`src="js/apps.js"`, recarregue e observe as duas coisas ao mesmo tempo, a
mensagem que sumiu do console e a linha vermelha com `404` na aba **Network**.
Desfaça em seguida.

## 16. Os produtos como dados

Crie `js/produtos.js` e escreva ali um array com **os produtos do seu
catálogo**, um objeto por produto, com os mesmos nomes e preços que estão no
HTML:

```javascript
const produtos = [
  { id: 1, nome: "Café em grão", categoria: "Bebidas", preco: 32.5, desconto: 0.15 },
  { id: 2, nome: "Chá de hibisco", categoria: "Bebidas", preco: 18, desconto: 0 }
  // continue com os seus
];
```

O trabalho prático pede ao menos oito produtos no catálogo; quem estiver usando o
pacote de exemplo escreve os seis que estão lá.

Ligue esse arquivo **antes** do `app.js`, porque o `app.js` vai usar o que ele
declara:

```html
<script src="js/produtos.js" defer></script>
<script src="js/app.js" defer></script>
```

Scripts com `defer` executam na ordem em que aparecem no HTML. Em `app.js`,
confirme que os dados chegaram:

```javascript
console.log(produtos.length);
console.table(produtos);
```

Confira na tabela do console que o número de linhas bate com o número de cartões
do `catalogo.html`.

## 17. Uma linha por produto

Ainda em `app.js`, escreva uma arrow function que receba um produto e devolva a
linha de texto correspondente, e use-a para imprimir o catálogo inteiro:

```javascript
const formataPreco = (valor) =>
  valor.toLocaleString("pt-BR", { style: "currency", currency: "BRL" });

const linhaDoProduto = (produto) =>
  `${produto.nome} (${produto.categoria}): ${formataPreco(produto.preco)}`;

produtos.forEach((produto) => console.log(linhaDoProduto(produto)));
```

Depois, troque o `forEach` por `map` e imprima o array resultante de uma vez:

```javascript
console.log(produtos.map(linhaDoProduto));
```

Responda para si: por que a primeira versão imprimiu uma linha por produto e a
segunda imprimiu uma estrutura só.

## 18. Preço com desconto

Acrescente a função que aplica o desconto e devolve o preço final, e uma lista
com os dois valores lado a lado:

```javascript
const precoFinal = (produto) => produto.preco * (1 - produto.desconto);

produtos.forEach((produto) => {
  console.log(
    `${produto.nome}: de ${formataPreco(produto.preco)} por ${formataPreco(precoFinal(produto))}`
  );
});
```

Confira um dos valores na calculadora. Se algum produto seu tem `desconto: 0`, os
dois preços saem iguais, e está correto.

## 19. Busca e filtro

São os dois requisitos da Entrega 2 do trabalho prático, hoje resolvidos no
console e na semana que vem ligados aos campos da página.

```javascript
const buscaPorNome = (lista, termo) =>
  lista.filter((produto) =>
    produto.nome.toLowerCase().includes(termo.toLowerCase())
  );

const filtraPorFaixa = (lista, minimo, maximo) =>
  lista.filter((produto) => produto.preco >= minimo && produto.preco <= maximo);

console.table(buscaPorNome(produtos, "ca"));
console.table(filtraPorFaixa(produtos, 20, 50));
```

Agora combine os dois, sem escrever uma terceira função:

```javascript
console.table(filtraPorFaixa(buscaPorNome(produtos, "ca"), 20, 50));
```

Teste os casos de borda e confira cada resposta:

* termo que não existe em nenhum produto (a resposta é um array vazio, e
  `console.table` de array vazio não mostra nada);
* termo em maiúsculas;
* faixa em que o mínimo é maior do que o máximo.

## 20. O relatório

Escreva uma função que percorra o catálogo uma vez e devolva um objeto com três
informações: o valor somado de todos os produtos, o nome do mais caro e a
quantidade de produtos por categoria.

```javascript
const gerarRelatorio = (lista) => {
  const total = lista.reduce((soma, produto) => soma + produto.preco, 0);

  const maisCaro = lista.reduce(
    (maior, produto) => (produto.preco > maior.preco ? produto : maior),
    lista[0]
  );

  const porCategoria = lista.reduce((contagem, produto) => {
    contagem[produto.categoria] = (contagem[produto.categoria] || 0) + 1;
    return contagem;
  }, {});

  return { total, maisCaro: maisCaro.nome, porCategoria };
};

console.log(gerarRelatorio(produtos));
```

Leia o terceiro `reduce` com atenção, porque ele traz três construções da aula de
uma vez: o acumulador é um objeto e não um número, o acesso à chave é por
colchete porque o nome da categoria vem de uma variável, e o `|| 0` cobre a
primeira vez que aquela categoria aparece, quando o valor lido seria `undefined`.

## 21. Ler o erro, de volta ao console

Quebre o código de propósito, uma quebra por vez, recarregue e leia a mensagem
inteira antes de desfazer. Anote o tipo do erro e a linha apontada.

1. troque `produtos.filter` por `produtos.filtrar`;
2. apague uma chave `}` no meio do arquivo;
3. escreva `const inexistente = produtos.find(p => p.id === 999);` seguido de
   `console.log(inexistente.nome);`;
4. escreva `const dobro = (v) => { v * 2 };` e imprima `dobro(4)`.

O quarto caso não gera erro nenhum e imprime `undefined`, pelo motivo da seção
10.3. É o tipo de defeito que o console não denuncia, e que só aparece quando
você confere o valor.

---

## Tarefa desta aula

Os exercícios desta aula valem nota e compõem o item *Exercícios em sala* da
média. A tarefa é maior do que o tempo de aula: comece hoje, com o professor por
perto, e termine ao longo da semana.

O enunciado fica no `README.md` do repositório-modelo da tarefa. O endereço desse
repositório e o **prazo de entrega** estão na atividade correspondente na **UFPR
Virtual**, que é onde os dois são mantidos atualizados.

A entrega é o próprio *fork* no GitLab, com os *commits* e o `push` feitos. Não
há link a enviar: o professor recolhe os repositórios pelo nome do grupo, com o
`alexkutzke` como `reporter` e o *fork* dentro do grupo, conforme as
[instruções de submissão](./instrucoes_submissao_tarefas_e_trabalhos.md).

A tarefa pode ser feita **individualmente ou em dupla**. Em dupla, apenas um dos
dois faz o *fork* no próprio grupo e adiciona o colega como `developer`, e a
forma de trabalho sugerida é o [Mob Programming](./00_mob_programming.md).

Esta é uma atividade avaliativa, e portanto **o uso de IA generativa para
produzir o código não é permitido**, conforme as Formas de Avaliação do plano de
ensino. Para consulta durante a tarefa: o material desta aula, a
[MDN](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript) e o professor.

A lista de [exercícios de treino](./aula_06_01_js_exercicios.md), sem entrega e
sem nota, está à parte e serve para quem quiser mais repetição em laços, strings
e arrays.

---

## Resumo

* O console do navegador executa JavaScript contra a página aberta, uma linha por
  vez, e é onde o código é conferido antes de ir para o arquivo.
* O script entra na página por arquivo externo com `defer`, o que permite deixar
  a tag no `<head>` e ainda assim executar com a página montada.
* `const` por padrão, `let` quando o valor for reatribuído. `const` em array ou
  objeto não impede alterar o conteúdo, só trocar o valor inteiro.
* `const` e `let` respeitam o bloco. `var` respeita a função, e por isso saiu de
  uso.
* Template literal com crase e `${}` substitui a concatenação com `+`.
* Todo número é ponto flutuante. Arredonde na exibição, com `toFixed` ou
  `toLocaleString`, e nunca compare resultado de conta com igualdade exata.
* `===` compara valor e tipo. `==` converte antes, e produz igualdades que
  ninguém quer.
* Os seis valores falsos são `false`, `0`, `""`, `null`, `undefined` e `NaN`.
  Array vazio não está na lista.
* `forEach` percorre, `map` transforma, `filter` seleciona, `reduce` resume,
  `find` procura o primeiro. Os quatro últimos devolvem um valor novo e deixam o
  array original intacto.
* Arrow function sem chaves devolve a expressão; com chaves, precisa de `return`
  escrito.
* Erro de JavaScript para a execução e diz o tipo, a descrição e a linha. A causa
  costuma estar antes da linha apontada.
