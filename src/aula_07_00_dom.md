# Aula 07 - Programação Cliente (Manipulação do DOM)

## Bibliografia recomendada

  * [MDN Web Docs - Introdução ao DOM](https://www.google.com/search?q=https://developer.mozilla.org/pt-BR/docs/Web/API/Document_Object_Model/Introduction)
  * [w3schools - JS HTML DOM](https://www.w3schools.com/js/js_htmldom.asp)

## DOM (Document Object Model)

O **DOM** (Document Object Model) é uma interface de programação padronizada pelo W3C que representa o documento HTML como uma árvore de objetos (nós). Esta API permite que a linguagem JavaScript acesse, altere, adicione ou remova elementos e estilos do documento renderizado pelo navegador em tempo de execução.

Através do DOM, a estrutura estática do HTML torna-se dinâmica. O ponto de entrada principal para esta API é o objeto global `document`.

### Manipulação do DOM

A implementação nativa da API nos navegadores modernos dispensa o uso de bibliotecas de terceiros (como jQuery) para a maioria das tarefas de manipulação.

#### Seleção de Elementos (Busca)

Historicamente, utilizavam-se métodos específicos para ID, classes ou tags. No JavaScript moderno, o padrão da indústria é a utilização dos métodos `querySelector` e `querySelectorAll`, que aceitam a sintaxe padrão de seletores CSS, conferindo maior flexibilidade.

```javascript
// Métodos legados (ainda válidos e performáticos, mas menos flexíveis)
const porId = document.getElementById("teste");
const porClasse = document.getElementsByClassName("intro");
const porTag = document.getElementsByTagName("p");

// O padrão moderno e mais versátil: querySelector (Retorna o primeiro elemento que casar com o seletor)
const titulo = document.querySelector("#teste");
const primeiroParagrafoIntro = document.querySelector("p.intro");

// querySelectorAll (Retorna uma NodeList com todos os elementos que casam com o seletor)
const todosParagrafos = document.querySelectorAll("p");
```

#### Alteração de Conteúdo e Propriedades

Após selecionar o elemento, suas propriedades podem ser alteradas. O uso de `var` é obsoleto; utilize `const` para referências de elementos do DOM.

```javascript
const elemento = document.querySelector("#p1");

// 1. textContent: Altera apenas o texto. É mais rápido e seguro contra ataques XSS.
elemento.textContent = "Novo conteúdo textual puro!";

// 2. innerHTML: Interpreta tags HTML. Use apenas se for estritamente necessário renderizar novas tags.
elemento.innerHTML = "Texto com <strong>negrito</strong>.";

// 3. Alteração de atributos
const imagem = document.querySelector("#myImage");
imagem.src = "landscape.jpg";
imagem.alt = "Nova descrição da imagem";

// 4. Alteração de estilos inline (Apenas para valores dinâmicos)
const p2 = document.querySelector("#p2");
p2.style.color = "blue";
p2.style.marginTop = "20px";

// 5. Manipulação de Classes CSS (Prática recomendada para estilização)
const divAlerta = document.querySelector(".alerta");
divAlerta.classList.add("alerta-sucesso"); // Adiciona classe
divAlerta.classList.remove("alerta-erro"); // Remove classe
divAlerta.classList.toggle("oculto");      // Alterna a classe (adiciona se não tiver, remove se tiver)
```

*Nota: Evite o uso da função `document.write()`. Trata-se de uma prática obsoleta que sobrescreve todo o documento HTML caso executada após o carregamento da página.*

#### Navegação no DOM (Traversing)

É possível navegar pela árvore do DOM através de relações estruturais (pais, filhos e irmãos). Recomenda-se utilizar as propriedades baseadas em *Element* em vez de *Node*, pois ignoram nós de texto em branco (quebras de linha no HTML).

```javascript
const container = document.querySelector("#demo");

// Navegação Moderna (Focada em Elementos HTML)
const pai = container.parentElement;
const filhos = container.children; // Retorna uma HTMLCollection apenas com tags filhas
const primeiroFilho = container.firstElementChild;
const ultimoFilho = container.lastElementChild;
const proximoIrmao = container.nextElementSibling;
const irmaoAnterior = container.previousElementSibling;

// Acesso direto ao primeiro filho
const textoPrimeiroFilho = filhos[0].textContent;
```

#### Criação de Elementos Dinâmicos

A inserção de novos componentes na interface envolve a criação de elementos na memória e sua posterior anexação à árvore do DOM.

```javascript
// 1. Criação de um novo elemento <div>
const novaDiv = document.createElement("div");

// 2. Configuração do elemento
novaDiv.textContent = "Conteúdo gerado via JS";
novaDiv.classList.add("box-destaque");

// 3. Seleção do nó pai onde o elemento será inserido
const elementoPai = document.querySelector("#secao-principal");

// 4. Inserção no DOM
elementoPai.append(novaDiv); // append() é o padrão moderno, substituindo appendChild()
```

### Eventos e Interatividade

A interatividade no lado do cliente ocorre por meio do mapeamento de **eventos** (cliques, digitação, envio de formulários, redimensionamento de janela).

**Má Prática (Evite):** O uso de atributos de evento diretamente no HTML (ex: `<button onclick="funcao()">`) fere o princípio de separação de responsabilidades. O HTML deve conter apenas a estrutura, e o JS o comportamento.

**Padrão Moderno (`addEventListener`):**

A interface `addEventListener` permite vincular múltiplas funções a um mesmo evento sem sobrescrever ouvintes anteriores.

```javascript
// Seleção do botão
const botaoEnviar = document.querySelector("#btnEnviar");

// Declaração da função de callback (Arrow Function)
const processarClique = (event) => {
    // O objeto 'event' (ou 'e') contém os metadados do evento disparado
    
    // event.target aponta para o elemento exato que acionou o evento
    event.target.textContent = "Processando...";
    event.target.classList.add("desativado");
    
    console.log("Botão clicado nas coordenadas: X:", event.clientX, "Y:", event.clientY);
};

// Vinculação do ouvinte de evento
botaoEnviar.addEventListener("click", processarClique);
```

#### Atribuindo eventos a múltiplos elementos

Se o sistema precisar capturar eventos de uma lista de elementos semelhantes, utilize um laço de repetição (`forEach`) sobre a `NodeList` gerada pelo `querySelectorAll`.

```javascript
// Seleciona todos os parágrafos com a classe 'clicavel'
const paragrafos = document.querySelectorAll("p.clicavel");

paragrafos.forEach((paragrafo) => {
    paragrafo.addEventListener("click", (e) => {
        // Altera a cor de fundo do elemento específico que foi clicado
        e.target.style.backgroundColor = "yellow";
    });
});
```

### Links Importantes

  * [Introdução a Eventos](https://developer.mozilla.org/pt-BR/docs/Learn/JavaScript/Building_blocks/Events)
  * [Manipulando o DOM](https://developer.mozilla.org/pt-BR/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents)
  * [Element.classList](https://developer.mozilla.org/pt-BR/docs/Web/API/Element/classList)
  * [Document.querySelector](https://developer.mozilla.org/pt-BR/docs/Web/API/Document/querySelector)
