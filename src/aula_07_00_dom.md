# JavaScript: DOM e eventos

[Slides desta aula (PDF)](slides/aula_07_dom.pdf)

## Bibliografia recomendada para o tema

* [MDN - DOM](https://developer.mozilla.org/pt-BR/docs/Web/API/Document_Object_Model);
* [MDN - Manipulando documentos](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/DOM_scripting);
* [MDN - Introdução a eventos](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/Events);
* [MDN - Validação de formulários](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Extensions/Forms/Form_validation);
* [JavaScript Eloquente, 3a edição, em português](https://github.com/braziljs/eloquente-javascript), capítulos 14 e 15;
* FLANAGAN, David. **JavaScript: o guia definitivo**. Porto Alegre: Bookman, 2012 (bibliografia complementar da disciplina).

> Na aula anterior, o catálogo virou um array de objetos, e as funções de busca,
> filtro e relatório imprimiam o resultado no console. Nesta aula o mesmo array
> passa a montar os cartões na tela, e a página passa a reagir ao que a pessoa
> digita e clica. É o que falta para a Entrega 2 do trabalho prático, exceto o
> carregamento dos dados por `fetch`, que é o assunto do encontro seguinte.

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Descrever o DOM como a árvore de objetos que o navegador monta a partir do
   HTML, e distinguir o que está no arquivo do que está na página aberta;
2. Selecionar um elemento ou um conjunto de elementos com `querySelector` e
   `querySelectorAll`, e reconhecer o erro de quando nada foi encontrado;
3. Alterar o texto, os atributos e as classes de um elemento, e justificar a
   preferência por classe em vez de `style`;
4. Criar elementos com `createElement` e `append`, e montar uma lista na tela a
   partir de um array de objetos;
5. Explicar por que `innerHTML` com dados do usuário é arriscado, e quando
   `textContent` resolve;
6. Registrar uma função para um evento com `addEventListener`, e usar o objeto
   do evento para saber o que aconteceu;
7. Ligar a busca e o filtro da aula anterior aos campos da página, com o evento
   `input`;
8. Validar um formulário em JavaScript, impedir o envio com `preventDefault` e
   mostrar mensagens na própria página.

---

## Material de exemplo

Os exemplos desta aula usam as páginas da pasta `base/` do
[repositório da tarefa](https://gitlab.com/ds122-alexkutzke/ds122-dom-assignment),
que trazem as três páginas da loja com o layout pronto e o `js/produtos.js` com
os seis produtos. Faça o *fork* e o clone do repositório antes de começar,
conforme o `README.md` dele: a parte teórica usa essas páginas para testes no
console, e a parte prática é feita dentro do seu *fork*.

Quem já tem as próprias páginas e o `produtos.js` da tarefa de JavaScript
trabalha sobre eles, também dentro do *fork*.

# Parte teórica

## 1. A página alterada pelo console

Abra o `catalogo.html` no navegador, aperte `F12` e vá à aba **Console**.
Digite, uma linha por vez:

```javascript
document.querySelector("h1").textContent = "Loja Alterada";
document.querySelector(".produto").remove();
document.querySelectorAll(".produto").length
```

O título mudou, o primeiro cartão sumiu, e a contagem de cartões caiu de 6 para
5, sem que nenhum arquivo tenha sido salvo. Recarregue a página: tudo volta ao
que estava.

As alterações foram feitas numa estrutura que o navegador mantém na memória
enquanto a página está aberta, e que ele montou lendo o HTML. Essa estrutura é o
DOM, e o JavaScript de uma página trabalha sobre ela.

## 2. O DOM

DOM é a sigla de *Document Object Model*, modelo de objetos do documento. Ao
receber o HTML, o navegador lê as tags e cria um objeto para cada elemento, com
propriedades (o texto, os atributos, as classes) e métodos (acrescentar um
filho, remover-se, registrar um evento). A página que você vê na tela é desenhada
a partir desses objetos, e é redesenhada sempre que algum deles muda.

A especificação do DOM é mantida pelo WHATWG, o mesmo grupo que mantém o HTML, e
é a mesma em todos os navegadores atuais. O código desta aula roda igual no
Chrome, no Firefox e no Safari.

### 2.1. Uma árvore de nós

Os objetos ficam organizados na mesma hierarquia das tags: cada elemento é filho
do elemento que o contém. Um trecho do `catalogo.html` fica assim:

```text
document
└── html
    ├── head
    │   ├── meta
    │   ├── meta
    │   ├── title
    │   └── link
    └── body
        ├── header
        │   ├── h1
        │   └── nav
        └── main
            └── section
                ├── h2
                └── div.grade
                    ├── article.produto
                    └── article.produto
```

Cada item da árvore é um **nó** (*node*). Elementos são nós, e o texto dentro de
um elemento também é um nó, filho daquele elemento. A árvore acima mostra só os
elementos.

É a mesma árvore que o CSS percorre. Quando você escreveu `.produto h3` na aula
de CSS, o seletor descreveu um caminho nessa hierarquia: um `h3` que está em
algum ponto abaixo de um elemento com a classe `produto`.

### 2.2. O arquivo e a página aberta

O navegador oferece duas formas de ver o HTML de uma página, e elas mostram
coisas diferentes:

* **Código-fonte** (`Ctrl+U`): o arquivo como chegou do servidor. Não muda
  depois de carregado;
* **Aba Elements** do `F12`: o DOM naquele instante, desenhado como HTML.
  Reflete cada alteração feita pelo JavaScript.

Repita a primeira linha da seção 1 e compare as duas vistas. No código-fonte o
`<h1>` continua com `Loja Exemplo`; na aba **Elements**, ele já diz
`Loja Alterada`.

Essa diferença explica um comportamento que confunde no começo. Quando o
catálogo for montado pelo JavaScript, os cartões vão aparecer na tela e na aba
**Elements**, e não vão estar no código-fonte.

### 2.3. O objeto `document`

A raiz da árvore está disponível em todo script de página como `document`, que
você já usou na aula anterior sem que ele tivesse sido declarado. Toda busca por
elementos começa por ele.

```javascript
document.title          // o texto da tag <title>
document.body           // o elemento <body>
document.documentElement // o elemento <html>
```

`document` existe no navegador. Um script executado fora dele, no Node.js, não
tem `document`, porque não há página.

## 3. Selecionar elementos

Antes de alterar um elemento, o script precisa de uma referência a ele. A forma
usada hoje aceita os mesmos seletores do CSS.

### 3.1. `querySelector`

Devolve o **primeiro** elemento que casa com o seletor:

```javascript
const titulo = document.querySelector("h1");
const grade = document.querySelector(".grade");
const campoNome = document.querySelector("#nome");
const primeiroPreco = document.querySelector(".produto .preco");
```

A referência vai numa `const`, porque a variável vai apontar sempre para o
mesmo elemento, ainda que o conteúdo dele mude.

O método também existe em qualquer elemento, e aí a busca fica restrita aos
descendentes dele:

```javascript
const cartao = document.querySelector(".produto");
const nomeDoCartao = cartao.querySelector("h3");
```

### 3.2. `querySelectorAll` e a `NodeList`

Devolve **todos** os elementos que casam com o seletor, numa `NodeList`:

```javascript
const cartoes = document.querySelectorAll(".produto");

cartoes.length;        // 6
cartoes[0];            // o primeiro cartão
cartoes.forEach((cartao) => console.log(cartao.querySelector("h3").textContent));
```

A `NodeList` tem `length`, acesso por índice e `forEach`, e para aí: não tem
`map`, `filter` nem `reduce`. Quando precisar deles, converta para array com
`Array.from(cartoes)`.

Um engano frequente é tratar a lista como se fosse um elemento:

```javascript
document.querySelectorAll(".produto").classList.add("destaque");
// TypeError: Cannot read properties of undefined (reading 'add')
```

A `NodeList` não tem `classList`. É preciso percorrer a lista e alterar cada
elemento.

### 3.3. `getElementById`

É a forma anterior ao `querySelector` para buscar por `id`, e continua em uso:

```javascript
const campoNome = document.getElementById("nome");  // sem o #
```

As duas linhas abaixo devolvem o mesmo elemento:

```javascript
document.getElementById("nome");
document.querySelector("#nome");
```

Nos exemplos desta aula usamos `querySelector` em todos os casos, para que a
forma de buscar seja uma só.

### 3.4. Quando nada é encontrado

Se nenhum elemento casar com o seletor, `querySelector` devolve `null`, sem
erro. O erro aparece na linha seguinte, quando o código tenta usar o resultado:

```javascript
const botao = document.querySelector("#enviar");  // não existe na página
botao.addEventListener("click", enviar);
// TypeError: Cannot read properties of null (reading 'addEventListener')
```

A mensagem aponta a segunda linha, e a causa está na primeira. Diante de
`Cannot read properties of null`, confira o seletor: um `#` ou um `.` a menos,
um `id` digitado diferente do HTML, ou um elemento que existe em outra página e
não nesta.

`querySelectorAll` sem resultado devolve uma `NodeList` vazia, com `length`
igual a zero, e um `forEach` sobre ela simplesmente não executa nada.

### 3.5. Por que o script precisa de `defer`

Na aula anterior, o script entrou no `<head>` com `defer`, e a justificativa
ficou para depois: sem `defer`, o navegador executa o script no momento em que
encontra a tag, antes de ler o resto do HTML. Um `querySelector` executado
nesse momento procura um elemento que ainda não entrou na árvore e recebe `null`.

Com `defer`, o script só executa depois que o documento inteiro foi lido, e todo
elemento do HTML já está no DOM. É por isso que todos os scripts desta aula
continuam no `<head>` com `defer`.

## 4. Alterar o que já existe

### 4.1. Texto: `textContent`

`textContent` lê e altera o texto de um elemento:

```javascript
const titulo = document.querySelector("main h2");
titulo.textContent;                                  // "Produtos"
titulo.textContent = `Produtos (${produtos.length})`;
```

Atribuir a `textContent` substitui todo o conteúdo do elemento pelo texto
informado. Se o valor tiver `<` e `>`, eles aparecem na tela como caracteres, e
não viram tags.

### 4.2. Atributos

Os atributos mais comuns viram propriedades de mesmo nome no objeto:

```javascript
const imagem = document.querySelector(".produto img");
imagem.src = "imagens/cafe.jpg";
imagem.alt = "Pacote de café em grão de 500 g";

const link = document.querySelector(".menu a");
link.href;   // o endereço completo, já resolvido pelo navegador
```

Em campos de formulário, a propriedade que interessa é `value`, que guarda o que
está digitado no momento:

```javascript
const campoNome = document.querySelector("#nome");
campoNome.value;             // o texto digitado
campoNome.value = "";        // apaga o campo
```

`value` é sempre uma **string**, mesmo num `<input type="number">`. A seção 6.5
volta a esse ponto, porque ele causa um defeito que o console não denuncia.

Para atributos que não viram propriedade, existem `getAttribute` e
`setAttribute`:

```javascript
cartao.setAttribute("aria-label", "Produto em promoção");
```

### 4.3. Classes: `classList`

A forma de mudar a aparência de um elemento pelo JavaScript é ligar e desligar
classes, cujas regras ficam no CSS:

```javascript
const aviso = document.querySelector(".aviso");

aviso.classList.add("oculto");        // acrescenta a classe
aviso.classList.remove("oculto");     // retira
aviso.classList.toggle("oculto");     // acrescenta se não tem, retira se tem
aviso.classList.contains("oculto");   // true ou false
```

```css
.oculto {
  display: none;
}
```

`classList` mexe só na classe informada, e preserva as outras que o elemento já
tem. Um elemento com `class="produto"` que recebe `classList.add("destaque")`
passa a ter `class="produto destaque"`.

### 4.4. `style`, e por que preferir classe

Cada elemento tem também a propriedade `style`, que altera o estilo diretamente:

```javascript
aviso.style.display = "none";
cartao.style.borderColor = "gold";   // border-color vira borderColor
```

Funciona, e fica no atributo `style` do elemento, com a especificidade mais alta
da cascata que vimos na aula de CSS. Em relação à classe, tem estas
desvantagens:

* a aparência passa a estar em dois lugares, no CSS e no JavaScript, e quem for
  mudar a cor do destaque precisa procurar nos dois;
* desfazer exige saber o valor anterior de cada propriedade, enquanto desfazer
  uma classe é um `remove`;
* as regras de uma classe podem mudar por media query, e o `style` escrito pelo
  script não.

Reserve `style` para valores calculados na hora, que não cabem numa classe
pronta, como a largura de uma barra de progresso em porcentagem.

## 5. Criar elementos

### 5.1. `createElement` e `append`

Um elemento novo é criado em três passos: criar, configurar e acrescentar à
árvore.

```javascript
const aviso = document.createElement("p");    // 1. cria, fora da página
aviso.textContent = "Frete grátis acima de R$ 100,00";
aviso.classList.add("destaque");              // 2. configura

const secao = document.querySelector("main section");
secao.append(aviso);                          // 3. acrescenta como último filho
```

Até o passo 3, o elemento existe só na memória e não aparece na tela. `append`
aceita mais de um nó de uma vez, na ordem em que devem aparecer:

```javascript
cartao.append(nome, imagem, categoria, preco);
```

Existem também `prepend`, que acrescenta como primeiro filho, e `remove`, usado
na seção 1, que tira o elemento da árvore. Em código mais antigo você vai
encontrar `appendChild`, que acrescenta um nó por vez.

### 5.2. Uma função que monta um cartão

Com os passos da seção anterior, uma função pode receber um objeto do array de
produtos e devolver o cartão correspondente, pronto para ser acrescentado:

```javascript
const criaCartao = (produto) => {
  const cartao = document.createElement("article");
  cartao.classList.add("produto");

  const nome = document.createElement("h3");
  nome.textContent = produto.nome;

  const preco = document.createElement("p");
  preco.classList.add("preco");
  preco.textContent = formataPreco(produto.preco);

  cartao.append(nome, preco);
  return cartao;
};

const grade = document.querySelector(".grade");
grade.append(criaCartao(produtos[0]));
```

A função devolve o elemento e não o acrescenta a lugar nenhum. Quem chama decide
onde ele entra, e a mesma função serve para a grade do catálogo e para um
destaque na página inicial.

O cartão ganha as mesmas classes dos cartões escritos à mão (`produto`,
`preco`), e por isso recebe o mesmo estilo sem nenhuma linha nova de CSS.

### 5.3. De um array para a tela

Com a `criaCartao`, montar o catálogo inteiro é percorrer o array:

```javascript
const renderiza = (lista) => {
  grade.replaceChildren();
  lista.forEach((produto) => grade.append(criaCartao(produto)));
};

renderiza(produtos);
```

`replaceChildren()` sem argumentos remove todos os filhos da grade. Sem essa
linha, chamar `renderiza` uma segunda vez acrescentaria os cartões novos depois
dos antigos. Em código mais antigo, o mesmo efeito aparece escrito como
`grade.innerHTML = ""`.

A `renderiza` recebe a lista como parâmetro, em vez de usar `produtos`
diretamente. É o que vai permitir, na seção 6, chamar a mesma função com o
resultado de uma busca.

A partir daqui, os cartões escritos à mão no HTML saem do arquivo. O array passa
a ser o único lugar onde os produtos estão descritos, e acrescentar um produto é
acrescentar um objeto ao array.

### 5.4. `innerHTML`

`innerHTML` lê e altera o conteúdo de um elemento como texto HTML, que o
navegador interpreta:

```javascript
cartao.innerHTML = `
  <h3>${produto.nome}</h3>
  <p class="preco">${formataPreco(produto.preco)}</p>
`;
```

O código fica mais curto que o da seção 5.2, e é comum em tutoriais. O problema
é que tudo o que estiver dentro do `${}` também é interpretado como HTML. Se o
nome do produto vier de um formulário, alguém pode cadastrar um nome com tags, e
elas passam a fazer parte da página de todos os visitantes. Quando a tag
cadastrada é um `<img>` com o atributo `onerror`, o navegador executa o
JavaScript que estiver nele. Esse ataque se chama *cross-site scripting*, ou XSS,
e volta à disciplina quando os dados passarem a vir do banco, na parte de PHP.

`textContent` não tem esse problema, porque nunca interpreta tags. A regra
adotada nesta disciplina é usar `textContent` e `createElement` para qualquer
valor que venha de dados, e deixar `innerHTML` só para trechos fixos, escritos
por você no próprio código.

## 6. Eventos

Até aqui, todo o código executou uma vez, quando a página carregou. Para a
página reagir ao que a pessoa faz, o script registra funções que o navegador
chama quando algo acontece: um clique, uma tecla, o envio de um formulário. Cada
um desses acontecimentos é um **evento**.

### 6.1. `addEventListener`

```javascript
const botao = document.querySelector("#mostrar-promocoes");

const mostraPromocoes = () => {
  renderiza(produtos.filter((produto) => produto.desconto > 0));
};

botao.addEventListener("click", mostraPromocoes);
```

O primeiro argumento é o nome do evento, e o segundo é a função que o navegador
vai chamar cada vez que o evento acontecer naquele elemento. Essa função é
chamada de ouvinte (*listener*) ou de tratador (*handler*).

A linha do `addEventListener` executa uma vez, quando o script carrega, e só
registra o ouvinte. A `mostraPromocoes` executa depois, a cada clique.

Um mesmo elemento pode ter vários ouvintes para o mesmo evento, e o navegador
chama todos, na ordem em que foram registrados.

Você vai encontrar também o evento escrito no próprio HTML, como
`<button onclick="mostraPromocoes()">`. Funciona, e mistura comportamento com
estrutura, pela mesma razão que nos levou a escrever o CSS em arquivo separado.
Nesta disciplina, os eventos são registrados no script.

### 6.2. Passar a função, não o resultado dela

```javascript
botao.addEventListener("click", mostraPromocoes);     // certo
botao.addEventListener("click", mostraPromocoes());   // errado
```

Na segunda linha, os parênteses **chamam** a função ali mesmo, durante o
carregamento, e o que é registrado como ouvinte é o valor que ela devolveu,
`undefined`. O efeito do clique acontece uma vez, sozinho, quando a página abre,
e os cliques seguintes não fazem nada. O console não mostra erro nenhum.

É a situação da seção 10.5 da aula anterior, em que uma função foi passada como
valor para o `forEach`. O `addEventListener` recebe a função da mesma forma.

### 6.3. O objeto do evento

O navegador chama o ouvinte passando um argumento, o objeto do evento, com
informações sobre o que aconteceu:

```javascript
botao.addEventListener("click", (evento) => {
  console.log(evento.type);     // "click"
  console.log(evento.target);   // o elemento que recebeu o clique
});
```

`evento.target` é o mais usado. Com ele, a mesma função pode servir a vários
elementos e saber qual deles foi acionado:

```javascript
document.querySelectorAll(".produto").forEach((cartao) => {
  cartao.addEventListener("click", (evento) => {
    console.log(evento.target);   // o h3, a imagem ou o preço clicado
  });
});
```

O nome do parâmetro é livre. Em documentação e em código alheio aparecem
`event`, `evt` e `e`.

### 6.4. Eventos de campo: `input` e `change`

Dois eventos acompanham o que acontece num campo de formulário:

| Evento | Quando acontece |
|---|---|
| `input` | a cada alteração do valor: cada tecla, cada colagem, cada clique na seta de um campo numérico |
| `change` | quando a alteração é confirmada: ao sair do campo de texto, ou logo ao escolher uma opção num `<select>` ou marcar um *checkbox* |

Uma busca que atualiza a lista enquanto a pessoa digita usa `input`. Ela
reaproveita a `buscaPorNome`, escrita na seção 19 da
[aula anterior](./aula_06_00_js.md):

```javascript
const buscaPorNome = (lista, termo) =>
  lista.filter((produto) =>
    produto.nome.toLowerCase().includes(termo.toLowerCase())
  );
```

O ouvinte do campo de busca chama a função a cada tecla:

```javascript
const campoBusca = document.querySelector("#busca");

campoBusca.addEventListener("input", () => {
  renderiza(buscaPorNome(produtos, campoBusca.value));
});
```

A `buscaPorNome` é a da aula anterior, sem nenhuma alteração. Ela recebia a
lista e o termo e devolvia outra lista; agora o termo vem do campo, e a lista
devolvida vai para a `renderiza`.

### 6.5. `value` é sempre string

```javascript
const campoMax = document.querySelector("#preco-max");
campoMax.value;            // "50", com aspas, mesmo em type="number"
campoMax.value > 9;        // true: a comparação com número converte
campoMax.value + 1;        // "501": o + com string concatena
Number(campoMax.value);    // 50
```

Converta com `Number` antes de fazer conta. E atenção ao campo vazio:
`Number("")` vale `0`. Um preço máximo vazio convertido direto vira `0`, e
nenhum produto passa pelo filtro. O tratamento do campo vazio é um dos passos da
parte prática.

## 7. Formulários

### 7.1. O evento `submit` e o `preventDefault`

Quando a pessoa envia um formulário, pelo botão ou pelo `Enter` num campo, o
navegador dispara o evento `submit` no `<form>` e, em seguida, faz o que o HTML
manda: uma requisição para o endereço do atributo `action`, que carrega outra
página. Vimos essa requisição na aula de HTTP.

Algumas ações têm esse comportamento padrão do navegador: o envio do formulário,
a navegação ao clicar num link. O ouvinte pode cancelá-lo com
`evento.preventDefault()`:

```javascript
const formulario = document.querySelector("form");

formulario.addEventListener("submit", (evento) => {
  evento.preventDefault();
  console.log("envio interceptado, a página não recarregou");
});
```

O ouvinte fica no `<form>`, com o evento `submit`, e não no botão com `click`.
O `submit` só acontece quando o formulário vai de fato ser enviado, depois da
validação nativa da seção 7.2, qualquer que tenha sido a forma de envio.

Enquanto a disciplina não tiver back-end, o `processa.php` do `action` não
existe, e o formulário de contato vai sempre cancelar o envio e dar o retorno
na própria página. Quando o PHP entrar, o `preventDefault` passa a ser chamado
só quando a validação falhar.

### 7.2. Validação nativa e validação em JavaScript

O formulário de contato já tem validação nativa desde a aula de HTML: `required`,
`minlength`, `type="email"`. Ela continua valendo, e o navegador a executa
**antes** de disparar o `submit`. Se um campo `required` estiver vazio, aparece
a mensagem do navegador e o ouvinte de `submit` nem é chamado.

A validação em JavaScript cobre o que os atributos não expressam. Um exemplo,
testado no formulário da pasta `base/`: o campo nome tem `required` e
`minlength="3"`, e aceita um nome de três espaços, porque três espaços são três
caracteres.

```javascript
"   ".length;          // 3
"   ".trim().length;   // 0
```

`trim()` devolve a string sem os espaços das duas pontas, e a regra em
JavaScript fica:

```javascript
if (campoNome.value.trim().length < 3) {
  // recusa
}
```

Nenhuma das duas validações protege o servidor. Quem quiser pode enviar uma
requisição direto ao `action`, sem passar pela página. A validação no navegador
existe para dar retorno rápido a quem preenche; a validação que garante os dados
é a do servidor, que vem com o PHP.

### 7.3. Mensagem de erro no lugar do campo

O retorno de uma validação precisa aparecer perto do campo, e não num `alert`,
que bloqueia a página e some quando fechado. Um elemento vazio abaixo de cada
campo recebe a mensagem:

```html
<p>
  <label for="nome">Nome</label>
  <input type="text" id="nome" name="nome" minlength="3" required>
  <span class="erro" id="erro-nome"></span>
</p>
```

```javascript
const erroNome = document.querySelector("#erro-nome");

formulario.addEventListener("submit", (evento) => {
  evento.preventDefault();
  erroNome.textContent = "";      // apaga a mensagem da tentativa anterior

  if (campoNome.value.trim().length < 3) {
    erroNome.textContent = "Informe o nome, com pelo menos 3 letras.";
  }
});
```

A primeira linha dentro do ouvinte apaga a mensagem anterior. Sem ela, a pessoa
corrige o campo, envia de novo, e a mensagem antiga continua na tela.

## 8. Quando dá errado

Os defeitos desta aula se dividem entre os que o console aponta e os que só
aparecem na tela.

| Sintoma | Causa provável |
|---|---|
| `TypeError: Cannot read properties of null` | seletor que não achou nada (seção 3.4), ou script sem `defer` executado antes do HTML (seção 3.5) |
| `TypeError: ... (reading 'add')` numa lista | `classList` ou `addEventListener` chamado direto na `NodeList` (seção 3.2) |
| o efeito do clique acontece ao abrir a página, e o clique não faz nada | função chamada no `addEventListener` em vez de passada (seção 6.2) |
| soma com campo de formulário dá `"501"` | `value` usado sem `Number` (seção 6.5) |
| a lista cresce a cada filtro, com cartões repetidos | `renderiza` sem esvaziar o contêiner antes (seção 5.3) |
| o formulário recarrega a página e as mensagens somem | `preventDefault` ausente (seção 7.1) |

Os quatro últimos não geram erro no console. A forma de encontrá-los é comparar
o que a página faz com o que devia fazer, e usar `console.log` dentro do ouvinte
para confirmar se ele está sendo chamado e com que valores.

## 9. Para ler depois

Esta seção é leitura complementar, fora da exposição em sala e fora da Prova 2.

**Atributos `data-*`.** Um elemento pode guardar dados próprios em atributos
com prefixo `data-`, como `<article data-id="3">`, lidos no JavaScript por
`cartao.dataset.id`. É uma forma de o ouvinte de um clique saber a qual produto
o cartão corresponde. Veja
[MDN - Usando atributos de dados](https://developer.mozilla.org/pt-BR/docs/Web/HTML/How_to/Use_data_attributes).

**Delegação de eventos.** Os eventos sobem pela árvore: um clique num `h3`
dispara o ouvinte do `h3`, depois o do `article`, depois o da `.grade`, até o
`document`. Com isso, um único ouvinte na grade atende a todos os cartões, mesmo
os criados depois, usando `evento.target` e `closest(".produto")` para achar o
cartão clicado. Veja a seção sobre propagação em
[MDN - Introdução a eventos](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Scripting/Events).

**`localStorage`.** O navegador guarda pares de chave e valor por site, que
sobrevivem ao recarregamento: `localStorage.setItem("favoritos", "[1,3]")` e
`localStorage.getItem("favoritos")`. É o recurso do item opcional de favoritos da
Entrega 2. Veja
[MDN - Window.localStorage](https://developer.mozilla.org/pt-BR/docs/Web/API/Window/localStorage).

---

# Parte prática

Trabalhe no seu *fork* do
[repositório da tarefa](https://gitlab.com/ds122-alexkutzke/ds122-dom-assignment),
sobre as suas páginas e o seu `produtos.js`, ou sobre os da pasta `base/`. Cada
passo tem um resultado visível na página: confira antes de seguir para o
próximo.

As seções desta parte são as paradas práticas dos slides, na mesma ordem.

## 10. O ambiente

Copie para a raiz do *fork* as suas páginas, as pastas `css/` e `imagens/` e o
`js/produtos.js` da tarefa anterior, ou mova para a raiz o conteúdo da pasta
`base/`. Crie o `js/app.js` com as funções `formataPreco`, `buscaPorNome` e
`filtraPorFaixa` da tarefa de JavaScript, e ligue os dois scripts no `<head>` do
`catalogo.html`, nesta ordem:

```html
<script src="js/produtos.js" defer></script>
<script src="js/app.js" defer></script>
```

Abra o catálogo com o console aberto e confira que `produtos.length` responde o
número de produtos e que não há erro vermelho.

## 11. Título e aviso

No fim do `app.js`:

```javascript
const titulo = document.querySelector("main h2");
titulo.textContent = `Produtos (${produtos.length})`;

const aviso = document.querySelector(".aviso");
aviso.classList.add("oculto");
```

E no fim do CSS:

```css
/* ---------- 8. Classes ligadas pelo JavaScript ---------- */

.oculto {
  display: none;
}
```

Recarregue. O título mostra a contagem e a faixa de aviso sumiu. Abra o
código-fonte com `Ctrl+U` e confira que ali o `<h2>` continua só com
`Produtos`.

Nas suas próprias páginas, o seletor do `<h2>` pode ser outro. Ajuste-o, e
confirme no console que `document.querySelector` devolve o elemento certo antes
de alterar o texto.

## 12. Um cartão

Acrescente a cada objeto do `produtos.js` o campo `imagem`, com o caminho da
imagem do produto (no `produtos.js` da pasta `base/`, ele já existe). Em
`app.js`, escreva a `criaCartao` com todos os elementos do cartão:

```javascript
const criaCartao = (produto) => {
  const cartao = document.createElement("article");
  cartao.classList.add("produto");

  const nome = document.createElement("h3");
  nome.textContent = produto.nome;

  const imagem = document.createElement("img");
  imagem.src = produto.imagem;
  imagem.alt = `Imagem de ${produto.nome}`;

  const categoria = document.createElement("p");
  categoria.classList.add("categoria");
  categoria.textContent = produto.categoria;

  const preco = document.createElement("p");
  preco.classList.add("preco");
  preco.textContent = formataPreco(produto.preco);

  cartao.append(nome, imagem, categoria, preco);
  return cartao;
};

const grade = document.querySelector(".grade");
grade.append(criaCartao(produtos[0]));
```

Recarregue. Aparece um sétimo cartão no fim da grade, com o mesmo estilo dos
seis escritos à mão.

## 13. O catálogo renderizado

Apague do `catalogo.html` todos os `<article class="produto">`, e deixe só a
grade vazia:

```html
<div class="grade"></div>
```

Troque a última linha da seção anterior pela `renderiza`:

```javascript
const renderiza = (lista) => {
  grade.replaceChildren();
  lista.forEach((produto) => grade.append(criaCartao(produto)));
};

renderiza(produtos);
```

Recarregue. Os cartões voltam, agora gerados pelo array.

Acrescente o destaque condicional, que é um requisito da Entrega 2. Dentro da
`criaCartao`, antes do `return`:

```javascript
if (produto.desconto >= 0.1) {
  cartao.classList.add("destaque");
}
```

E no CSS:

```css
.destaque {
  border: 3px solid #b8860b;
}
```

Com o `produtos.js` da pasta `base/`, recebem a borda o café, o mel e o
sabonete, com descontos de 15%, 10% e 20%.

A tarefa pede também um parágrafo com o desconto em porcentagem, criado só para
os produtos com desconto. Para escrever o número, arredonde com `Math.round`:
com alguns descontos a multiplicação por 100 não dá um inteiro, como
`0.07 * 100`, que vale `7.000000000000001`, pelo motivo visto na seção de
números da aula anterior.

## 14. Busca enquanto digita

Acrescente o campo acima da grade, no `catalogo.html`:

```html
<form class="filtros" role="search">
  <p>
    <label for="busca">Buscar por nome</label>
    <input type="search" id="busca">
  </p>
</form>
```

E no `app.js`, antes da chamada `renderiza(produtos)`:

```javascript
const campoBusca = document.querySelector("#busca");

campoBusca.addEventListener("input", () => {
  renderiza(buscaPorNome(produtos, campoBusca.value));
});
```

Digite `de` no campo. Com os produtos da pasta `base/`, ficam o chá de hibisco,
a farinha de milho, o sabonete de argila e a cesta de vime. Apague o termo: com
o campo vazio, `includes("")` é verdadeiro para qualquer nome, e todos os
produtos voltam.

## 15. Faixa de preço

Acrescente os dois campos ao formulário de filtros:

```html
<p>
  <label for="preco-min">Preço mínimo</label>
  <input type="number" id="preco-min" min="0" step="0.01">
</p>
<p>
  <label for="preco-max">Preço máximo</label>
  <input type="number" id="preco-max" min="0" step="0.01">
</p>
```

Com três campos, cada ouvinte precisa considerar os outros dois. Troque o
ouvinte da seção anterior por uma função que lê os três e aplica os dois
filtros, e registre-a nos três campos:

```javascript
const campoMin = document.querySelector("#preco-min");
const campoMax = document.querySelector("#preco-max");

const aplicaFiltros = () => {
  const minimo = campoMin.value === "" ? 0 : Number(campoMin.value);
  const maximo = campoMax.value === "" ? Infinity : Number(campoMax.value);

  const porNome = buscaPorNome(produtos, campoBusca.value);
  renderiza(filtraPorFaixa(porNome, minimo, maximo));
};

campoBusca.addEventListener("input", aplicaFiltros);
campoMin.addEventListener("input", aplicaFiltros);
campoMax.addEventListener("input", aplicaFiltros);
```

Campo vazio vira `0` no mínimo e `Infinity` no máximo, e assim não exclui
nenhum produto. `Infinity` é um número do JavaScript maior que qualquer outro.

Teste com `de` na busca e a faixa de 10 a 30. Com a pasta `base/`, ficam o chá
de hibisco e a farinha de milho.

A tarefa pede também uma mensagem para quando nenhum produto passar pelos
filtros. Ela depende só do tamanho da lista que chega à `renderiza`.

## 16. Validação do contato

No `contato.html`, ligue um script próprio no `<head>`:

```html
<script src="js/contato.js" defer></script>
```

Acrescente um `<span class="erro">` vazio abaixo do campo nome e outro abaixo da
mensagem, com `id` próprios, e um parágrafo de sucesso oculto depois do
formulário:

```html
<p id="sucesso" class="oculto">Mensagem enviada com sucesso!</p>
```

Em `js/contato.js`:

```javascript
const formulario = document.querySelector("form");
const campoNome = document.querySelector("#nome");
const campoMensagem = document.querySelector("#mensagem");
const erroNome = document.querySelector("#erro-nome");
const erroMensagem = document.querySelector("#erro-mensagem");
const sucesso = document.querySelector("#sucesso");

formulario.addEventListener("submit", (evento) => {
  evento.preventDefault();

  erroNome.textContent = "";
  erroMensagem.textContent = "";
  sucesso.classList.add("oculto");

  let valido = true;

  if (campoNome.value.trim().length < 3) {
    erroNome.textContent = "Informe o nome, com pelo menos 3 letras.";
    valido = false;
  }

  if (campoMensagem.value.trim().length < 10) {
    erroMensagem.textContent = "A mensagem precisa ter pelo menos 10 caracteres.";
    valido = false;
  }

  if (valido) {
    sucesso.classList.remove("oculto");
    formulario.reset();
  }
});
```

A variável `valido` é `let` porque pode mudar para `false` em qualquer um dos
dois testes. Os dois testes executam sempre, e a pessoa vê de uma vez todos os
campos com problema.

Teste com três espaços no nome e `oi` na mensagem, com os demais campos
preenchidos: as duas mensagens de erro aparecem. Corrija os dois campos e envie
de novo: as mensagens somem, aparece o parágrafo de sucesso e o formulário é
limpo. Nenhum dos envios recarrega a página.

A regra `.erro` no CSS fica a seu critério. Uma cor que contraste com o fundo e
`display: block`, para a mensagem ficar abaixo do campo, costumam bastar.

---

## Tarefa desta aula

Os exercícios desta aula valem nota e compõem o item *Exercícios em sala* da
média. A tarefa é maior do que o tempo de aula: comece hoje, com o professor por
perto, e termine ao longo da semana.

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
[MDN](https://developer.mozilla.org/pt-BR/docs/Web/API/Document_Object_Model) e
o professor.

A busca, a faixa de preço, o destaque condicional e a validação do contato são
requisitos da Entrega 2 do trabalho prático, que vence na aula anterior à Prova
2. O que falta para ela, carregar os produtos de um arquivo JSON com `fetch`, é
o assunto do encontro seguinte.

A lista de [exercícios de treino de DOM e eventos](./aula_07_01_dom_exercicios.md),
sem entrega e sem nota, está à parte e serve para quem quiser mais repetição.

---

## Resumo

* O DOM é a árvore de objetos que o navegador monta a partir do HTML. O
  JavaScript altera essa árvore, e o arquivo continua como estava: o código-fonte
  mostra o arquivo, a aba **Elements** mostra o DOM.
* `querySelector` devolve o primeiro elemento que casa com um seletor CSS, ou
  `null`. `querySelectorAll` devolve uma `NodeList`, que tem `forEach` e não tem
  os demais métodos de array.
* `Cannot read properties of null` quase sempre é um seletor que não achou nada,
  ou um script sem `defer`.
* `textContent` altera o texto sem interpretar tags. `innerHTML` interpreta, e
  por isso não recebe dados vindos do usuário.
* A aparência muda por classe, com `classList`, e as regras ficam no CSS.
* `createElement` cria o elemento fora da página, e `append` o coloca na árvore.
  Uma função que devolve o cartão pronto, chamada num `forEach`, monta a lista
  inteira a partir do array.
* `addEventListener` recebe o nome do evento e a função, sem parênteses. O
  navegador chama a função a cada ocorrência, passando o objeto do evento.
* `input` acontece a cada alteração do campo, `change` quando a alteração é
  confirmada, `submit` no envio do formulário.
* `value` de campo é sempre string. Converta com `Number`, e trate o campo vazio
  antes, porque `Number("")` vale `0`.
* `preventDefault` cancela o envio do formulário. A validação nativa executa
  antes do `submit`, e a validação em JavaScript cobre o que os atributos não
  expressam.
