# Fetch API e JSON

[Slides desta aula (PDF)](slides/aula_07_fetch.pdf)

## Bibliografia recomendada para o tema

* [MDN - Trabalhando com JSON](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/JSON);
* [MDN - Usando a Fetch API](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API/Using_Fetch);
* [MDN - async function](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/async_function);
* [JavaScript Eloquente, 3a edição, em português](https://github.com/braziljs/eloquente-javascript), capítulos 11 e 18;
* FLANAGAN, David. **JavaScript: o guia definitivo**. Porto Alegre: Bookman, 2012 (bibliografia complementar da disciplina).

> Na aula de DOM e eventos, o catálogo passou a ser montado pelo JavaScript, a
> partir do array do `js/produtos.js`. Nesta aula, os produtos saem do código e
> vão para um arquivo de dados, o `produtos.json`, que a página pede ao servidor
> com `fetch`. É o último requisito da Entrega 2 do trabalho prático.

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Escrever dados em JSON, e apontar o que muda em relação a um objeto
   JavaScript;
2. Converter entre texto JSON e valor JavaScript com `JSON.parse` e
   `JSON.stringify`;
3. Servir as páginas por um servidor local, e explicar por que o `fetch` falha
   em página aberta direto do disco;
4. Descrever o que `fetch` devolve, e usar `await` dentro de uma função `async`
   para esperar a resposta;
5. Conferir `response.ok` e explicar por que uma resposta 404 não cai no
   `catch`;
6. Carregar uma lista de um arquivo JSON e montá-la na página com as funções da
   aula de DOM;
7. Consultar uma API pública e tratar a resposta de erro que ela devolve.

---

## Material de exemplo

Os exemplos desta aula usam as páginas do
[repositório da tarefa](https://gitlab.com/ds122-alexkutzke/ds122-lab-02-assignment).
Faça o *fork* e o clone antes de começar, conforme o `README.md` dele, e copie
para a raiz as suas páginas e os arquivos da tarefa de DOM. Se você não tiver a
tarefa de DOM pronta, mova para a raiz o conteúdo da pasta `base/`, que traz o
catálogo com `criaCartao` e `renderiza` funcionando.

# Parte teórica

## 1. Uma requisição pelo console

Abra `https://viacep.com.br` no navegador, aperte `F12` e vá à aba **Network**
(**Rede**, no Firefox em português). Deixe-a aberta, passe para a aba
**Console** e digite, uma linha por vez:

```javascript
const resposta = await fetch("https://viacep.com.br/ws/80060000/json/");
resposta.status
const dados = await resposta.json();
dados.logradouro
dados.localidade
```

O console responde `200`, `"Rua XV de Novembro"` e `"Curitiba"`. Volte à aba
**Network**: há uma linha nova, com o endereço pedido, o método `GET` e o status
`200`. Clique nela e abra a aba **Resposta** (*Response*): o servidor devolveu
um texto entre chaves, com pares de nome e valor.

A página não recarregou. O JavaScript fez uma requisição HTTP, como as que vimos
no início do semestre, recebeu a resposta e transformou o corpo dela em um
objeto. As seções seguintes tratam de cada parte disso: o formato do texto
recebido (seção 2), de onde a página pode fazer requisições (seção 3), por que o
`await` aparece (seção 4) e o `fetch` em si (seção 5).

## 2. JSON

JSON (*JavaScript Object Notation*) é um formato de texto para dados. A sintaxe
vem dos objetos e arrays do JavaScript, mas o formato é usado por praticamente
qualquer linguagem: o PHP, que vem na segunda metade da disciplina, lê e escreve
JSON com duas funções.

### 2.1. A sintaxe

Um documento JSON é um único valor, que pode ser um objeto, um array, uma
string, um número, `true`, `false` ou `null`. Objetos e arrays podem conter
outros valores, inclusive outros objetos e arrays:

```json
[
  {
    "id": 1,
    "nome": "Café em grão",
    "preco": 32.5,
    "desconto": 0.15,
    "disponivel": true,
    "tags": ["orgânico", "torra média"]
  },
  {
    "id": 2,
    "nome": "Chá de hibisco",
    "preco": 18,
    "desconto": 0,
    "disponivel": false,
    "tags": []
  }
]
```

### 2.2. O que muda em relação a um objeto JavaScript

O mesmo produto, escrito no `produtos.js` e no `produtos.json`:

```javascript
// produtos.js
const produtos = [
  { id: 1, nome: 'Café em grão', preco: 32.5, }, // primeiro produto
];
```

```json
[
  { "id": 1, "nome": "Café em grão", "preco": 32.5 }
]
```

| No JavaScript | No JSON |
|---|---|
| nome do campo com ou sem aspas | nome do campo sempre entre aspas duplas |
| string com aspas simples, duplas ou crase | string só com aspas duplas |
| vírgula depois do último item é aceita | vírgula depois do último item é erro |
| comentários com `//` e `/* */` | não há comentários |
| `const produtos =` antes do array | só o valor, sem variável |
| funções, `undefined`, datas | não existem; datas viram string |

Um número entre aspas é uma string: `"preco": "32.5"` e `"preco": 32.5` são
valores diferentes, e só o segundo soma como número.

### 2.3. `JSON.parse` e `JSON.stringify`

O JSON chega pela rede como texto. `JSON.parse` converte o texto em valor
JavaScript, e `JSON.stringify` faz o caminho contrário:

```javascript
const texto = '{"nome": "Café em grão", "preco": 32.5}';

const produto = JSON.parse(texto);
produto.preco * 2;              // 65

JSON.stringify({ nome: "Mel", preco: 24.9 });
// '{"nome":"Mel","preco":24.9}'
```

Texto com erro de sintaxe faz o `JSON.parse` lançar um `SyntaxError`, com a
posição do problema. Teste no console com `JSON.parse("{'nome': 'Mel'}")`.

Nesta aula quase não chamamos `JSON.parse` diretamente: o `response.json()` da
seção 5.3 faz essa conversão sobre o corpo da resposta.

## 3. O servidor local

Abra o `catalogo.html` com dois cliques no arquivo e veja o endereço na barra do
navegador: ele começa com `file://`. A página foi lida do disco, sem servidor e
sem HTTP. Para mostrar HTML, CSS e imagens, isso basta.

Para o `fetch`, não basta. Por segurança, o navegador bloqueia requisições
feitas por uma página aberta do disco, e o console do Chrome mostra:

```
Access to fetch at 'file:///.../produtos.json' from origin 'null' has been
blocked by CORS policy: Cross origin requests are only supported for protocol
schemes: chrome, chrome-extension, chrome-untrusted, data, http, https,
isolated-app.
```

A partir desta aula, as páginas são abertas por um servidor local, que entrega
os arquivos da pasta por HTTP, em `http://localhost`. Use uma das duas opções:

* no VS Code, a extensão **Live Server**, com o botão *Go Live* no canto
  inferior direito, que abre a página no navegador e a recarrega a cada
  alteração salva;
* no terminal, dentro da pasta do repositório:

  ```bash
  python3 -m http.server 8000
  ```

  e no navegador o endereço `http://localhost:8000/catalogo.html`. O terminal
  fica ocupado enquanto o servidor estiver rodando, e `Ctrl+C` o encerra.

Com o servidor rodando, a aba **Network** mostra cada arquivo da página como uma
requisição, com o status de cada um. É a mesma lista que vimos na aula de HTTP,
agora para o seu próprio site.

## 4. Operações assíncronas

Uma requisição pela rede leva tempo: de alguns milissegundos, no servidor local,
a alguns segundos, numa conexão ruim. Se o navegador parasse o JavaScript até a
resposta chegar, a página inteira congelaria nesse intervalo, sem rolar e sem
responder a cliques.

Por isso, o `fetch` não devolve a resposta. Ele dispara a requisição e devolve,
na hora, uma *Promise* (promessa): um objeto que representa um resultado que
ainda vai chegar. Veja no console:

```javascript
const p = fetch("https://viacep.com.br/ws/80060000/json/");
p   // Promise {<pending>}
```

A *Promise* começa pendente (*pending*). Quando a resposta chega, ela passa a
cumprida (*fulfilled*), com a resposta dentro; se a requisição falhar, passa a
rejeitada (*rejected*), com o erro dentro.

A palavra `await`, colocada antes de uma *Promise*, espera que ela se resolva e
devolve o valor que está dentro dela:

```javascript
const resposta = await fetch("https://viacep.com.br/ws/80060000/json/");
resposta   // Response {status: 200, ok: true, ...}
```

Fora do console, `await` só pode ser usado dentro de uma função marcada com
`async`. A seção 5.4 mostra essa função.

## 5. `fetch`

### 5.1. O objeto `Response`

`await fetch(url)` devolve um objeto `Response`, com o que o servidor
respondeu. As propriedades usadas nesta aula:

| Propriedade ou método | O que é |
|---|---|
| `resposta.status` | o código de status HTTP, como `200` ou `404` |
| `resposta.ok` | `true` quando o status está entre 200 e 299 |
| `resposta.json()` | lê o corpo e converte de JSON; devolve uma *Promise* |
| `resposta.text()` | lê o corpo como texto; devolve uma *Promise* |

O endereço passado ao `fetch` pode ser completo, como o do ViaCEP, ou relativo
à página, como `"produtos.json"`, da mesma forma que o `href` de um link.

### 5.2. `response.ok` e o 404

O `fetch` só rejeita a *Promise* quando a requisição não consegue acontecer:
sem rede, servidor fora do ar, domínio que não existe, ou bloqueio do navegador,
como o da seção 3. Uma resposta 404 ou 500 é uma resposta HTTP completa, e para
o `fetch` a requisição deu certo.

Teste no console, com o servidor local rodando:

```javascript
const r = await fetch("nao-existe.json");
r.status   // 404
r.ok       // false
```

Não houve erro vermelho de JavaScript. Por isso, todo `fetch` confere
`response.ok` antes de usar o corpo.

### 5.3. `response.json()`

O corpo da resposta chega aos poucos pela rede, e lê-lo também é uma operação
assíncrona. `response.json()` devolve outra *Promise*, que também precisa de
`await`:

```javascript
const resposta = await fetch("produtos.json");
const produtos = await resposta.json();
produtos.length   // 6
```

Sem o segundo `await`, `produtos` é uma *Promise*, e `produtos.forEach` dá
`TypeError: produtos.forEach is not a function`.

### 5.4. Uma função `async` com `try` e `catch`

Juntando as seções anteriores, a função que carrega o catálogo:

```javascript
const carregaProdutos = async () => {
  try {
    const resposta = await fetch("produtos.json");
    if (!resposta.ok) {
      throw new Error(`Erro HTTP: ${resposta.status}`);
    }
    const produtos = await resposta.json();
    renderiza(produtos);
  } catch (erro) {
    console.error(erro);
    const aviso = document.createElement("p");
    aviso.classList.add("erro");
    aviso.textContent = "Não foi possível carregar os produtos.";
    grade.replaceChildren(aviso);
  }
};

carregaProdutos();
```

Linha a linha:

* `async` antes dos parênteses marca a função como assíncrona, o que permite
  usar `await` dentro dela;
* o `try` contém o caminho normal, e qualquer erro dentro dele desvia a execução
  para o `catch`;
* se o status não for de sucesso, `throw new Error(...)` lança um erro de
  propósito, e o 404 passa a cair no mesmo `catch` que a falha de rede;
* o `catch` recebe o erro, registra no console e mostra uma mensagem na página,
  porque o visitante não abre o console;
* `renderiza` é a mesma função da aula de DOM, que recebe uma lista e monta os
  cartões. Ela não sabe de onde a lista veio.

### 5.5. A ordem de execução

Uma função `async` devolve o controle a quem a chamou assim que encontra o
primeiro `await`. O resto dela continua quando a resposta chegar. O código
abaixo da chamada não espera:

```javascript
let produtos = [];

const carrega = async () => {
  const resposta = await fetch("produtos.json");
  produtos = await resposta.json();
  console.log("2. dados chegaram:", produtos.length);
};

carrega();
console.log("1. logo depois da chamada:", produtos.length);
```

O console mostra a linha `1.` com `0`, e só depois a linha `2.` com `6`.
Tudo o que depende dos dados vai **dentro** da função, depois do `await`, como a
chamada a `renderiza` na seção 5.4.

## 6. Uma API pública: ViaCEP

API (*Application Programming Interface*), no contexto da web, é um endereço que
responde dados em vez de páginas, em geral em JSON, para ser consumido por
outros programas. O ViaCEP, que usamos na seção 1, recebe um CEP no endereço e
devolve o endereço correspondente:

```
https://viacep.com.br/ws/80060000/json/
```

A [documentação do ViaCEP](https://viacep.com.br/) descreve as respostas. Duas
delas não são o endereço:

| Pedido | Status | Corpo |
|---|---|---|
| CEP com formato inválido, como `123` | 400 | página de erro em HTML |
| CEP com oito dígitos que não existe, como `99999999` | 200 | `{ "erro": "true" }` |

O segundo caso tem status 200, e `response.ok` é `true`. O tratamento dele
depende de olhar o conteúdo: se o objeto tiver o campo `erro`, o CEP não existe.
Muitas APIs públicas sinalizam erro assim, e a documentação de cada uma diz
como.

O formato do CEP pode ser conferido antes de qualquer requisição, sem gastar uma
ida ao servidor:

```javascript
const cep = campoCep.value.replace("-", "");
if (cep.length !== 8 || Number.isNaN(Number(cep))) {
  // mostra a mensagem e não faz o fetch
}
```

O ViaCEP aceita requisições feitas por páginas de qualquer site. Nem toda API
aceita: o navegador só entrega a resposta à página se o servidor da API permitir
o site de origem, pelo cabeçalho `Access-Control-Allow-Origin`. Esse mecanismo
se chama CORS (*Cross-Origin Resource Sharing*), e é o mesmo que bloqueia a
página aberta do disco na seção 3.

## 7. Quando dá errado

| Sintoma | Causa provável |
|---|---|
| `blocked by CORS policy` e endereço começando com `file://` | página aberta do disco, sem servidor local (seção 3) |
| `SyntaxError` ao ler o JSON | vírgula sobrando, aspas simples ou comentário no arquivo (seção 2.2) |
| `TypeError: ... .forEach is not a function` | `await` esquecido antes de `resposta.json()` (seção 5.3) |
| `await is only valid in async functions` | `await` fora de uma função `async` (seção 4) |
| `SyntaxError: Unexpected token '<'`, com status 404 na aba **Network** | arquivo não encontrado, sem conferir `response.ok`: o `json()` tentou ler a página de erro do servidor (seção 5.2) |
| a lista aparece, mas um total ou contagem fica zerado | código que usa os dados fora da função, antes de eles chegarem (seção 5.5) |
| soma de preços dá `"032.518"` | números entre aspas no JSON (seção 2.2) |

Os dois últimos não geram erro no console. Para os demais, a aba **Network**
mostra se o arquivo foi pedido, com que status voltou, e o corpo que chegou.

## 8. Para ler depois

Esta seção é leitura complementar, fora da exposição em sala e fora da Prova 2.

**`.then` e `.catch`.** Antes do `async`/`await`, o código com *Promise* era
escrito encadeando métodos. O mesmo carregamento da seção 5.4:

```javascript
fetch("produtos.json")
  .then((resposta) => {
    if (!resposta.ok) {
      throw new Error(`Erro HTTP: ${resposta.status}`);
    }
    return resposta.json();
  })
  .then((produtos) => renderiza(produtos))
  .catch((erro) => console.error(erro));
```

As duas formas fazem a mesma coisa, e muito código e muita documentação ainda
usam esta. Veja [MDN - Usando promises](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Guide/Using_promises).

**Enviar dados com `fetch`.** O `fetch` aceita um segundo argumento, com o
método e o corpo da requisição, para enviar dados ao servidor por `POST`. É o
que um formulário faz sem recarregar a página, e volta a fazer sentido quando o
servidor for nosso, em PHP. Veja a seção sobre corpo da requisição em
[MDN - Usando a Fetch API](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API/Using_Fetch).

**Outras APIs públicas.** [BrasilAPI](https://brasilapi.com.br/) (CEP, CNPJ,
feriados, bancos) e [IBGE - serviço de localidades](https://servicodados.ibge.gov.br/api/docs/localidades)
(estados e municípios) respondem JSON e aceitam requisições de qualquer site.

---

# Parte prática

Trabalhe no seu *fork* do
[repositório da tarefa](https://gitlab.com/ds122-alexkutzke/ds122-lab-02-assignment),
com as páginas abertas pelo servidor local. Cada passo tem um resultado visível
na página: confira antes de seguir para o próximo.

As seções desta parte são as paradas práticas dos slides, na mesma ordem.

## 9. O ambiente

Copie para a raiz do *fork* as suas páginas, as pastas `css/` e `imagens/` e os
arquivos `js/produtos.js` e `js/app.js` da tarefa de DOM, ou mova para a raiz o
conteúdo da pasta `base/`.

Inicie o servidor local, pela extensão Live Server ou com
`python3 -m http.server 8000`, e abra o catálogo por `http://localhost`.
Confira que os cartões aparecem e que a aba **Network** lista o `catalogo.html`,
o CSS e os dois scripts, todos com status `200`.

## 10. Os produtos em JSON

Crie o `produtos.json` na raiz, com os produtos do `js/produtos.js` escritos em
JSON, conforme a seção 2.2. Acrescente a cada produto o campo `descricao`:

```json
[
  {
    "id": 1,
    "nome": "Café em grão",
    "descricao": "Grãos de torra média, de produção familiar.",
    "categoria": "Bebidas",
    "preco": 32.5,
    "desconto": 0.15,
    "imagem": "imagens/produto.svg"
  }
]
```

Abra `http://localhost:8000/produtos.json` no Firefox e confira que ele mostra os
dados formatados. Se mostrar um erro de sintaxe, a mensagem diz a linha.

Apague o `js/produtos.js` e a tag `<script>` que o ligava ao `catalogo.html`.
Recarregue: a grade fica vazia, e o console mostra
`ReferenceError: produtos is not defined`, porque o `renderiza(produtos)` do fim
do `app.js` ainda procura o array. O próximo passo resolve.

## 11. O catálogo pelo `fetch`

No fim do `app.js`, troque `renderiza(produtos);` por:

```javascript
const carregaProdutos = async () => {
  const resposta = await fetch("produtos.json");
  if (!resposta.ok) {
    throw new Error(`Erro HTTP: ${resposta.status}`);
  }
  const produtos = await resposta.json();
  renderiza(produtos);
};

carregaProdutos();
```

Recarregue. Os cartões voltam, e a aba **Network** tem uma linha a mais, a do
`produtos.json`, com status `200`.

Na `criaCartao`, acrescente a descrição logo depois da imagem:

```javascript
const descricao = document.createElement("p");
descricao.classList.add("descricao");
descricao.textContent = produto.descricao;
cartao.append(descricao);
```

Acrescente um sétimo produto ao `produtos.json`, recarregue e confira que ele
aparece sem nenhuma alteração no JavaScript.

## 12. A mensagem de erro

Envolva o corpo da `carregaProdutos` em `try`/`catch`, como na seção 5.4, com a
mensagem `Não foi possível carregar os produtos.` em um `<p class="erro">`
dentro da grade.

Para testar, troque `"produtos.json"` por `"produto.json"` no `fetch`, e
recarregue. Confira a mensagem na página, o erro no console e a linha com status
`404` na aba **Network**. Desfaça a troca e confira que os cartões voltam.

## 13. O endereço pelo CEP

No `contato.html`, acrescente ao formulário quatro campos, cada um com seu
`<label>`: `cep`, `rua`, `bairro` e `cidade`, e um `<p id="erro-cep" class="erro"></p>`
logo depois do campo `cep`. Crie o `js/contato.js` e ligue-o à página com
`defer`:

```javascript
const campoCep = document.querySelector("#cep");
const erroCep = document.querySelector("#erro-cep");

const buscaCep = async () => {
  erroCep.textContent = "";
  const cep = campoCep.value.replace("-", "");

  if (cep.length !== 8 || Number.isNaN(Number(cep))) {
    erroCep.textContent = "Digite um CEP com oito números.";
    return;
  }

  const resposta = await fetch(`https://viacep.com.br/ws/${cep}/json/`);
  const dados = await resposta.json();

  document.querySelector("#rua").value = dados.logradouro;
  document.querySelector("#bairro").value = dados.bairro;
  document.querySelector("#cidade").value = dados.localidade;
};

campoCep.addEventListener("blur", buscaCep);
```

Digite `80060000` e saia do campo com `Tab`: rua, bairro e cidade se preenchem.
Digite `123` e confira a mensagem.

Agora digite `99999999`. Os campos ficam com `undefined`, porque o ViaCEP
respondeu `{ "erro": "true" }` (seção 6). Trate esse caso com a mesma mensagem
de erro, antes de preencher os campos, e acrescente o `try`/`catch` para o caso
de a requisição falhar.

---

## Tarefa desta aula

Os exercícios desta aula valem nota e compõem o item *Exercícios em sala* da
média. As paradas práticas desta aula cobrem os exercícios 1 a 4 da tarefa; o
exercício 5 é de revisão para a Prova 2.

O enunciado fica no `README.md` do repositório-modelo da tarefa. O endereço desse
repositório e o **prazo de entrega** estão na **UFPR Virtual**, que é onde os
dois são mantidos atualizados.

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
[MDN](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API) e o professor.

O carregamento do catálogo pelo `produtos.json` é o requisito 4 da Entrega 2 do
trabalho prático, com prazo no domingo, 11/10. No trabalho, o cartão montado
pelo JavaScript mantém o que a Entrega 1 pedia: imagem, nome, descrição curta e
preço.

---

## Resumo

* JSON é um formato de texto para dados, com a sintaxe de objetos e arrays do
  JavaScript e regras mais estritas: nomes e strings entre aspas duplas, sem
  vírgula sobrando, sem comentários.
* `JSON.parse` converte texto em valor, `JSON.stringify` converte valor em
  texto. Número entre aspas é string.
* O `fetch` não funciona em página aberta do disco (`file://`). A partir desta
  aula, as páginas são abertas por um servidor local.
* `fetch` devolve uma *Promise*, e `await` espera o resultado dela. `await` só
  pode ser usado dentro de função `async`.
* `fetch` só rejeita quando a requisição não acontece. Um 404 chega como
  resposta, com `ok` falso: confira `response.ok` sempre.
* `response.json()` também devolve uma *Promise*, e também precisa de `await`.
* O `try`/`catch` junta a falha de rede e o status de erro no mesmo tratamento,
  com uma mensagem na página.
* O código depois da chamada de uma função `async` não espera por ela. O que usa
  os dados vai dentro da função, depois do `await`.
* Uma API pública pode sinalizar erro no corpo de uma resposta 200, como o
  `{ "erro": "true" }` do ViaCEP. A documentação da API diz como.
