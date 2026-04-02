# Lista de Exercícios: Fundamentos de JavaScript (ES6+)

Os exercícios abaixo apresentam complexidade progressiva e devem ser resolvidos utilizando as práticas modernas da linguagem (declaração de variáveis com `let` e `const`, e utilização de *Arrow Functions* a partir do Exercício 3). 

Utilize o ambiente de execução do navegador (Console) ou o Node.js para testar os scripts.

---

## Exercício 1: Estruturas de Repetição (Triângulo)
Escreva um laço de repetição que execute exatamente **7 iterações**. A cada passagem pelo laço, o programa deve invocar o comando `console.log` **uma única vez** para exibir a linha correspondente do seguinte padrão geométrico:

```text
#
##
###
####
#####
######
#######
```

**Nota Técnica:** Lembre-se que você pode concatenar strings ou utilizar a propriedade `.length` na sua variável de controle para manipular a quantidade de caracteres `#` impressos em cada iteração.

## Exercício 2: Laços Aninhados (Tabuleiro de Xadrez)
Escreva um programa que construa uma única string representando um quadro 8x8. Utilize o caractere de quebra de linha (`\n`) para separar as linhas. As posições do quadro devem alternar entre um espaço em branco (`" "`) e o caractere cerquilha (`"#"`), formando um padrão de tabuleiro de xadrez.

A saída no `console.log` deve ser estritamente:

```text
 # # # #
# # # # 
 # # # #
# # # # 
 # # # #
# # # # 
 # # # #
# # # # 
```

**Requisito Adicional:** Implemente a lógica de forma que o tamanho do tabuleiro possa ser dinamicamente alterado pela modificação de uma única constante `tamanho` (ex: `const tamanho = 8;`).

## Exercício 3: Arrays e Funções Modernas (Soma)
Implemente uma *Arrow Function* chamada `somaArray` que receba um array de números e retorne a soma de todos os seus elementos. 

**Exemplo de uso:**
```javascript
const numeros = [1, 2, 3, 4];
console.log(somaArray(numeros)); // Saída esperada: 10
```

**Desafio (Opcional):** Implemente a mesma função utilizando o método funcional nativo `Array.prototype.reduce()`.

## Exercício 4: Manipulação de Referências (Inversão In-Place)
Crie uma *Arrow Function* chamada `inverteArray` que inverta a ordem dos elementos de um array. 

**Restrições Técnicas:**
1. A função deve ser *in-place*, ou seja, a inversão deve ocorrer no próprio array passado como argumento, **sem a criação de um array auxiliar**.
2. Não utilize o método nativo `Array.prototype.reverse()`.

**Exemplo de uso:**
```javascript
const arrayTeste = [1, 2, 3, 4, 5];
inverteArray(arrayTeste);
console.log(arrayTeste); // Saída esperada: [5, 4, 3, 2, 1]
```

## Exercício 5: Iteração em Strings (Contagem de Caracteres)
Crie uma *Arrow Function* chamada `contaLetras` que receba dois parâmetros: uma string base e um caractere alvo. A função deve retornar o número de vezes que o caractere alvo aparece na string base.

**Exemplo de uso:**
```javascript
const frase = "Desenvolvimento Web";
console.log(contaLetras(frase, "e")); // Saída esperada: 3
```

## Exercício 6: Algoritmo de Busca (Substring)
Crie uma *Arrow Function* chamada `procuraSubstring` que receba duas strings: um texto base e um termo de busca. A função deve procurar o termo dentro do texto e retornar o índice exato onde a primeira ocorrência do termo começa. Caso o termo não exista no texto, a função deve retornar `-1`.

**Restrições Técnicas:**
O programa **não** pode utilizar métodos nativos de busca em strings do JavaScript, tais como `indexOf()`, `includes()`, `search()`, `match()` ou expressões regulares (RegEx). A lógica deve ser implementada iterando pelos caracteres das strings.

**Exemplo de uso:**
```javascript
const texto = "Universidade Federal do Paraná";
console.log(procuraSubstring(texto, "Federal")); // Saída esperada: 13
console.log(procuraSubstring(texto, "Tecnologia")); // Saída esperada: -1
```

## Exercício 7: Processamento de Dados (Agrupamento e Relatório)

Este exercício simula um cenário real de desenvolvimento *Front-end*, onde a aplicação cliente recebe uma lista de dados no formato JSON (proveniente de uma API) e precisa processá-la para exibir um painel de indicadores (*Dashboard*).

Crie uma *Arrow Function* chamada `gerarRelatorio` que receba um array de objetos representando o estoque de uma loja. Cada objeto possui a seguinte estrutura: `{ id, nome, categoria, preco, quantidade }`.

A função deve processar os dados e retornar um **novo objeto** contendo as seguintes informações agregadas:
1.  `valorTotalEstoque`: O valor monetário total de todos os produtos somados (preço $\times$ quantidade).
2.  `produtoMaisCaro`: O nome do produto com o maior preço unitário.
3.  `itensPorCategoria`: Um objeto que agrupa a quantidade total de itens (unidades físicas) disponíveis em cada categoria.

**Restrições Técnicas:**
* A função deve ser construída utilizando métodos iteradores modernos de array (`reduce`, `filter`, `map`, `forEach`). É proibido o uso de laços tradicionais (`for` ou `while`).

**Exemplo de uso e estrutura esperada:**

```javascript
const estoque = [
    { id: 1, nome: "Processador", categoria: "Hardware", preco: 1500, quantidade: 10 },
    { id: 2, nome: "Mouse", categoria: "Periféricos", preco: 150, quantidade: 30 },
    { id: 3, nome: "Teclado", categoria: "Periféricos", preco: 250, quantidade: 20 },
    { id: 4, nome: "Placa de Vídeo", categoria: "Hardware", preco: 3200, quantidade: 5 },
    { id: 5, nome: "Monitor", categoria: "Monitores", preco: 1200, quantidade: 15 }
];

const relatorio = gerarRelatorio(estoque);
console.log(relatorio);

/* Saída Esperada:
{
    valorTotalEstoque: 58500,
    produtoMaisCaro: "Placa de Vídeo",
    itensPorCategoria: {
        "Hardware": 15,
        "Periféricos": 50,
        "Monitores": 15
    }
}
*/
```
