# Aula — Consumo de APIs com Fetch e JSON

## Bibliografia recomendada

* [MDN Web Docs — Fetch API](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API/Using_Fetch)
* [MDN Web Docs — JSON](https://developer.mozilla.org/pt-BR/docs/Learn/JavaScript/Objects/JSON)
* [w3schools — Fetch API](https://www.w3schools.com/js/js_api_fetch.asp)

---

## JSON (JavaScript Object Notation)

JSON é um formato leve e legível para **troca de dados** entre sistemas. É o formato padrão utilizado por APIs Web modernas para enviar e receber informações.

```json
{
  "nome": "Maria Silva",
  "idade": 25,
  "cursos": ["ADS", "Ciência de Dados"],
  "endereco": {
    "cidade": "Curitiba",
    "uf": "PR"
  },
  "matriculado": true
}
```

**Regras do JSON:**
* Dados organizados em pares **chave: valor**.
* Chaves sempre entre **aspas duplas**.
* Valores podem ser: `string`, `number`, `boolean`, `array`, `object` ou `null`.
* **Não** suporta funções, comentários ou `undefined`.

### JSON no JavaScript

O JavaScript possui duas funções nativas para trabalhar com JSON:

```javascript
// Converter objeto JS → string JSON
const aluno = { nome: "João", idade: 20 };
const jsonString = JSON.stringify(aluno);
console.log(jsonString); // → {"nome":"João","idade":20}

// Converter string JSON → objeto JS
const dados = '{"produto": "Notebook", "preco": 3500}';
const obj = JSON.parse(dados);
console.log(obj.produto); // → Notebook
console.log(obj.preco);  // → 3500
```

---

## O que é uma API?

**API** (*Application Programming Interface*) é um conjunto de regras que permite que dois sistemas se comuniquem.

No contexto Web, uma **API REST** é um serviço que:

1. Recebe requisições HTTP (GET, POST, PUT, DELETE).
2. Processa a lógica de negócio no servidor.
3. Devolve os dados em formato **JSON** (não HTML).

**Exemplo:** ao acessar `https://api.exemplo.com/produtos`, a API pode retornar:

```json
[
  { "id": 1, "nome": "Teclado", "preco": 150.00 },
  { "id": 2, "nome": "Mouse", "preco": 80.00 },
  { "id": 3, "nome": "Monitor", "preco": 900.00 }
]
```

> No front-end moderno, a aplicação JavaScript consome esses dados e os renderiza na interface — sem recarregar a página.

---

## Fetch API

`fetch()` é a interface moderna do JavaScript para realizar **requisições HTTP** diretamente do navegador. Substitui o antigo `XMLHttpRequest`.

### Sintaxe básica

```javascript
fetch(url)
  .then(response => response.json())  // Converte a resposta para JSON
  .then(data => console.log(data))    // Faz algo com os dados
  .catch(error => console.error("Erro:", error));
```

`fetch()` retorna uma **Promise** — um objeto que representa uma operação assíncrona que será concluída no futuro.

### Exemplo: consumindo dados de um arquivo JSON local

Crie um arquivo `produtos.json` na mesma pasta do seu HTML:

```json
[
  { "id": 1, "nome": "Notebook", "categoria": "Eletrônicos", "preco": 3500 },
  { "id": 2, "nome": "Camiseta", "categoria": "Roupas", "preco": 79.90 },
  { "id": 3, "nome": "Fone de Ouvido", "categoria": "Eletrônicos", "preco": 199.90 },
  { "id": 4, "nome": "Tênis", "categoria": "Calçados", "preco": 299.90 },
  { "id": 5, "nome": "Mochila", "categoria": "Acessórios", "preco": 159.90 }
]
```

Agora, no JavaScript:

```javascript
fetch("produtos.json")
  .then(response => {
    if (!response.ok) {
      throw new Error(`Erro HTTP: ${response.status}`);
    }
    return response.json();
  })
  .then(produtos => {
    console.log(`${produtos.length} produtos carregados.`);
    produtos.forEach(produto => {
      console.log(`${produto.nome} — R$ ${produto.preco.toFixed(2)}`);
    });
  })
  .catch(error => console.error("Falha ao carregar:", error));
```

### Renderizando dados no DOM

O caso de uso mais comum é carregar dados e exibi-los na página:

```javascript
const container = document.querySelector("#lista-produtos");

fetch("produtos.json")
  .then(response => response.json())
  .then(produtos => {
    container.innerHTML = ""; // Limpa o conteúdo atual

    produtos.forEach(produto => {
      const card = document.createElement("div");
      card.classList.add("produto-card");
      card.innerHTML = `
        <h3>${produto.nome}</h3>
        <span class="categoria">${produto.categoria}</span>
        <p class="preco">R$ ${produto.preco.toFixed(2)}</p>
      `;
      container.appendChild(card);
    });
  })
  .catch(error => {
    container.innerHTML = "<p class='erro'>Erro ao carregar produtos.</p>";
    console.error(error);
  });
```

### Verificando a resposta — `response.ok`

Sempre verifique se a requisição foi bem-sucedida. O `fetch()` **não** lança erro automaticamente para status HTTP 404 ou 500 — isso deve ser feito manualmente:

```javascript
fetch("dados.json")
  .then(response => {
    if (!response.ok) {
      throw new Error(`Erro ${response.status}: ${response.statusText}`);
    }
    return response.json();
  })
  .then(data => {
    /* processar dados */
  })
  .catch(error => {
    console.error("Requisição falhou:", error.message);
  });
```

| `response.ok` | `response.status` | Significado |
|---|---|---|
| `true` | 200–299 | Sucesso |
| `false` | 404 | Recurso não encontrado |
| `false` | 500 | Erro interno do servidor |

### Consumindo uma API pública

APIs públicas são ótimos recursos para praticar. Exemplo com a [JSONPlaceholder](https://jsonplaceholder.typicode.com/):

```javascript
// API fake que retorna 10 posts
fetch("https://jsonplaceholder.typicode.com/posts?_limit=5")
  .then(response => response.json())
  .then(posts => {
    const container = document.querySelector("#posts");
    posts.forEach(post => {
      const article = document.createElement("article");
      article.innerHTML = `
        <h3>${post.title}</h3>
        <p>${post.body}</p>
      `;
      container.appendChild(article);
    });
  })
  .catch(error => console.error("Erro na API:", error));
```

### Outras APIs públicas para praticar:

* [JSONPlaceholder](https://jsonplaceholder.typicode.com/) — posts, comentários, usuários, álbuns.
* [Fake Store API](https://fakestoreapi.com/) — produtos de loja com imagens.
* [ViaCEP](https://viacep.com.br/) — consulta de CEP brasileiro.
* [Brasil API](https://brasilapi.com.br/) — CEP, CNPJ, bancos, feriados.

---

## Fetch com Async/Await (sintaxe moderna)

`async/await` é uma sintaxe alternativa para trabalhar com Promises, tornando o código mais legível:

```javascript
async function carregarProdutos() {
  const container = document.querySelector("#lista-produtos");

  try {
    const response = await fetch("produtos.json");

    if (!response.ok) {
      throw new Error(`Erro ${response.status}`);
    }

    const produtos = await response.json();

    produtos.forEach(produto => {
      const card = document.createElement("div");
      card.innerHTML = `
        <h3>${produto.nome}</h3>
        <p>R$ ${produto.preco.toFixed(2)}</p>
      `;
      container.appendChild(card);
    });
  } catch (error) {
    container.innerHTML = "<p>Erro ao carregar dados.</p>";
    console.error(error);
  }
}

carregarProdutos();
```

> `async` marca uma função como assíncrona. `await` pausa a execução até que a Promise seja resolvida. `try/catch` substitui o `.catch()`.

---

## Exercícios Práticos

### Exercício 01: Carregar e exibir produtos

Crie uma página HTML com um `<div id="catalogo"></div>`. Utilizando `fetch()`, carregue os dados de `produtos.json` (o arquivo de exemplo acima) e renderize cada produto como um card contendo nome, categoria e preço.

**Requisitos:**
- Tratar o erro de carregamento (exibir mensagem no DOM se falhar).
- Utilizar `response.ok` para verificar a resposta.
- Criar os elementos via DOM (`createElement`, `classList.add`, `innerHTML`).

---

### Exercício 02: Filtro dinâmico com Fetch

A partir do Exercício 01, adicione:

1. Um `<input type="text">` de busca que filtre produtos pelo **nome** em tempo real.
2. Um `<select>` que filtre por **categoria**.

Os filtros devem ser combináveis: se o usuário digitar "note" E selecionar "Eletrônicos", apenas produtos que atendam a **ambos** os critérios devem ser exibidos.

---

### Exercício 03: Consumindo uma API pública

Utilizando a [Fake Store API](https://fakestoreapi.com/):

1. Faça um `fetch` para `https://fakestoreapi.com/products`.
2. Renderize os 8 primeiros produtos em cards, exibindo: imagem, título, preço e categoria.
3. Adicione um `<select>` com as categorias disponíveis (use `fetch` para `https://fakestoreapi.com/products/categories`).
4. Ao selecionar uma categoria, faça um novo `fetch` para filtrar: `https://fakestoreapi.com/products/category/nome-da-categoria`.

---

### Exercício 04: Posto de busca de CEP

Utilizando a [ViaCEP](https://viacep.com.br/):

1. Crie um formulário com um campo para CEP e um botão "Buscar".
2. Ao clicar, faça `fetch` para `https://viacep.com.br/ws/XXXXXXXX/json/` (substituindo os X pelo CEP digitado).
3. Exiba o resultado em um `<div>`: logradouro, bairro, cidade, UF.
4. Trate erros: CEP inválido, CEP não encontrado, falha de conexão.

---

### Links importantes

* [Fetch API — MDN](https://developer.mozilla.org/pt-BR/docs/Web/API/Fetch_API)
* [Trabalhando com JSON — MDN](https://developer.mozilla.org/pt-BR/docs/Learn/JavaScript/Objects/JSON)
* [JSONPlaceholder — Fake API](https://jsonplaceholder.typicode.com/)
* [Fake Store API](https://fakestoreapi.com/)
* [Async/Await — MDN](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/async_function)
