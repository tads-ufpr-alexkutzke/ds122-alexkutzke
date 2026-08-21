# HTML5

[Slides desta aula (PDF)](slides/aula_04_html.pdf)

## Bibliografia recomendada para o tema

* [MDN - HTML](https://developer.mozilla.org/pt-BR/docs/Web/HTML) (referência principal, use por consulta);
* [MDN - Estruturando a web com HTML](https://developer.mozilla.org/pt-BR/docs/Learn/HTML);
* [HTML Living Standard](https://html.spec.whatwg.org/multipage/) (especificação oficial, para tirar dúvidas pontuais).

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Escrever um documento HTML5 válido a partir do zero, sem copiar modelo pronto;
2. Escolher o elemento certo para cada conteúdo, justificando pelo significado e não pela aparência;
3. Explicar como o navegador transforma o texto do arquivo na árvore que ele de fato exibe;
4. Construir formulários e relacionar cada campo com a requisição HTTP que o navegador vai montar;
5. Aplicar validação nativa e explicar por que ela não substitui a validação no servidor;
6. Identificar decisões de marcação que quebram a acessibilidade da página e corrigi-las;
7. Validar o documento no W3C e interpretar os erros apontados.

---

# Parte teórica

## 1. O que o navegador recebe

Na aula anterior, ao pedir uma página com o `curl`, o corpo da resposta veio como
texto. Aquele texto é HTML:

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8

<!DOCTYPE html>
<html lang="pt-br">
...
```

O que o servidor envia é um documento de texto descrevendo o conteúdo, e não uma
tela pronta. O navegador lê esse texto, monta uma estrutura na memória e desenha
a tela a partir dela. O cabeçalho `Content-Type: text/html` é o que
avisa o navegador de que aquele corpo deve ser interpretado como HTML, e não
exibido literalmente.

HTML é a sigla de *HyperText Markup Language*. Marcação (*markup*) significa que
a linguagem funciona anotando um texto: o conteúdo continua sendo texto comum, e
as marcas dizem o que cada trecho é.

## 2. Marcação: elementos e atributos

A marca do HTML se chama *tag*. Um **elemento** é a tag de abertura, o conteúdo e
a tag de fechamento:

```html
<p>Este é um parágrafo.</p>
```

`<p>` abre, `</p>` fecha, e o que está entre os dois é o conteúdo do elemento.

Alguns elementos não têm conteúdo, e por isso não têm fechamento. São chamados de
elementos vazios:

```html
<img src="foto.jpg" alt="Bancada do laboratório">
<br>
<hr>
```

A barra final (`<br />`) é aceita, mas dispensável no HTML5.

Toda tag de abertura pode receber **atributos**, que ajustam o comportamento do
elemento:

```html
<a href="https://www.ufpr.br" title="Página da UFPR">UFPR</a>
```

`href` e `title` são atributos, cada um com seu valor entre aspas duplas. O nome
do atributo é fixo, definido pela especificação; inventar um nome não produz
erro visível, apenas nada acontece.

Elementos ficam uns dentro dos outros, e a ordem de fechamento importa:

```html
<p>Um <strong>aviso <em>importante</em></strong> no meio do texto.</p>
```

O que abre por último fecha primeiro. Cruzar as marcas (`<strong><em></strong></em>`)
é erro, embora o navegador tente adivinhar o que você quis dizer e siga em
frente.

> **O navegador não reclama.** Diferente de um compilador, ele nunca recusa uma
> página. Diante de marcação quebrada, ele aplica regras de recuperação de erro e
> exibe algo. O resultado costuma ser uma página que funciona no seu navegador,
> quebra no do colega, e é ilegível para um leitor de tela. Por isso existe o
> validador da seção 17.

### Convenções de escrita

* Nomes de tags e atributos em **minúsculas**;
* Valores de atributo sempre entre **aspas duplas**;
* Indentação consistente, dois ou quatro espaços, marcando o aninhamento;
* Comentários com `<!-- texto -->`, que não aparecem na tela mas chegam ao
  usuário, junto com todo o resto do arquivo.

## 3. Estrutura mínima do documento

Todo documento tem o mesmo esqueleto:

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Catálogo de Produtos</title>
</head>
<body>
  <h1>Catálogo de Produtos</h1>
</body>
</html>
```

Cada linha tem função:

* `<!DOCTYPE html>` declara que o documento segue o padrão atual. Sem ele, o
  navegador entra em modo de compatibilidade com páginas dos anos 1990, e o
  layout muda;
* `lang="pt-br"` informa o idioma do conteúdo. Leitores de tela usam esse valor
  para escolher a pronúncia, e buscadores para classificar a página;
* `<meta charset="utf-8">` diz em que codificação os bytes do arquivo foram
  gravados. Sem isso, acentos viram símbolos estranhos. O valor precisa
  corresponder ao que o editor de fato gravou;
* `<meta name="viewport" ...>` faz o celular usar a largura real da tela em vez
  de fingir ser um monitor de mesa. Sem essa linha, o site responsivo que você
  vai construir com CSS aparece reduzido no telefone;
* `<title>` é o texto da aba do navegador, do favorito e do resultado de busca.

O `<head>` guarda informação **sobre** o documento e não aparece na página. O
`<body>` guarda o que é exibido.

## 4. A árvore do documento

Ao ler o arquivo, o navegador monta uma estrutura de árvore na memória: o
elemento `html` na raiz, `head` e `body` como filhos, e assim por diante. Essa
árvore se chama **DOM** (*Document Object Model*).

Para o HTML abaixo:

```html
<body>
  <h1>Produtos</h1>
  <ul>
    <li>Teclado</li>
    <li>Monitor</li>
  </ul>
</body>
```

a árvore correspondente:

```
body
├── h1
│   └── "Produtos"
└── ul
    ├── li
    │   └── "Teclado"
    └── li
        └── "Monitor"
```

Duas consequências práticas, já nesta aula:

* O que o CSS estiliza e o que o JavaScript manipula é a árvore, e não o texto do
  arquivo. Aninhar errado desloca o galho inteiro;
* As ferramentas de desenvolvedor do navegador mostram a árvore, com os erros de
  marcação já corrigidos pelo navegador. Por isso o que aparece lá às vezes
  difere do seu arquivo.

O DOM volta em detalhe na aula de JavaScript. Aqui basta reconhecer que sua
marcação constrói uma estrutura hierárquica.

## 5. Texto: títulos, parágrafos e listas

### Títulos

De `<h1>` a `<h6>`, em ordem de importância. O `<h1>` identifica o assunto da
página, e os demais abrem subseções.

```html
<h1>Catálogo de Produtos</h1>
<h2>Periféricos</h2>
<h3>Teclados</h3>
```

Não pule níveis para conseguir uma letra menor (`<h1>` seguido de `<h3>`): o
tamanho se resolve no CSS. Um usuário de leitor de tela navega pela lista de
títulos da página, e essa lista é o índice do seu documento.

### Parágrafos e quebras

`<p>` delimita um parágrafo. O HTML ignora quebras de linha e espaços repetidos
do arquivo: escrever o texto em cinco linhas ou em uma só produz o mesmo
resultado. Quem separa os parágrafos é a marcação.

`<br>` força uma quebra dentro de um mesmo bloco, para casos em que a quebra faz
parte do conteúdo, como endereço ou verso de poema. Não use para criar espaço
vertical.

### Ênfase

```html
<p>Preço <strong>promocional</strong>, válido <em>somente hoje</em>.</p>
```

`<strong>` marca importância, `<em>` marca ênfase. Existem `<b>` e `<i>`, que só
mudam a aparência sem acrescentar significado. Prefira os dois primeiros.

### Listas

```html
<ul>
  <li>Teclado</li>
  <li>Monitor</li>
</ul>

<ol>
  <li>Abrir o editor</li>
  <li>Criar o arquivo</li>
  <li>Salvar com extensão .html</li>
</ol>
```

`<ul>` quando a ordem não importa, `<ol>` quando importa. Os filhos diretos de
uma lista são sempre `<li>`, e uma lista pode conter outra dentro de um `<li>`.

Menus de navegação são listas de links, e devem ser marcados como tal.

## 6. Links e caminhos

O link é o que faz o hipertexto:

```html
<a href="catalogo.html">Ver o catálogo</a>
```

O valor de `href` pode ser:

* **Relativo**, resolvido a partir do endereço da página atual:
  `catalogo.html`, `imagens/teclado.jpg`, `../index.html` (uma pasta acima);
* **Absoluto**, com o endereço completo: `https://www.ufpr.br/`;
* **Interno**, apontando para um elemento com `id` na mesma página:
  `<a href="#contato">` leva até `<section id="contato">`;
* **Outros esquemas**: `mailto:alexander@ufpr.br`, `tel:+554133611234`.

Use caminhos relativos entre as páginas do seu próprio site. Caminho absoluto do
seu disco (`file:///C:/Users/...`) funciona na sua máquina e em nenhuma outra.

Para abrir em nova aba:

```html
<a href="https://www.ufpr.br" target="_blank" rel="noopener">Site da UFPR</a>
```

O `rel="noopener"` impede que a página aberta manipule a sua pela referência que
o navegador entregaria a ela.

O texto do link precisa dizer para onde ele leva. Um leitor de tela consegue
listar todos os links da página fora de contexto, e uma lista de dez itens
escritos "clique aqui" não informa nada.

## 7. Imagens e figuras

```html
<img src="imagens/teclado.jpg" alt="Teclado mecânico preto, vista superior">
```

`src` é o caminho do arquivo, e `alt` é o texto alternativo. O `alt` é lido em voz
alta por leitores de tela, aparece quando a imagem falha ao carregar e é indexado
por buscadores. Descreva o que a imagem mostra, sem começar com "imagem de".

Imagem puramente decorativa recebe `alt=""`, vazio, o que faz o leitor de tela
ignorá-la. Omitir o atributo é diferente: aí o leitor de tela lê o nome do
arquivo.

Quando a imagem tem legenda visível:

```html
<figure>
  <img src="imagens/teclado.jpg" alt="Teclado mecânico preto, vista superior">
  <figcaption>Teclado mecânico ABNT2, switches marrons.</figcaption>
</figure>
```

`<figure>` agrupa a mídia com sua legenda, e `<figcaption>` é a legenda. O `alt`
descreve a imagem para quem não a vê; a `figcaption` é lida por todos.

Áudio e vídeo seguem a mesma lógica, com reprodução nativa:

```html
<video src="aula.mp4" controls width="480">
  Seu navegador não reproduz vídeo.
</video>
```

## 8. Tabelas

Tabela serve para dado tabular, com linhas e colunas relacionadas. Não use tabela
para posicionar elementos na tela, prática comum antes do CSS e hoje um erro de
acessibilidade.

```html
<table>
  <caption>Especificações dos modelos em estoque</caption>
  <thead>
    <tr>
      <th scope="col">Componente</th>
      <th scope="col">Especificação</th>
      <th scope="col">Preço</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Processador</td>
      <td>4 núcleos, 3.2 GHz</td>
      <td>R$ 890,00</td>
    </tr>
    <tr>
      <td>Memória</td>
      <td>16 GB DDR4</td>
      <td>R$ 320,00</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="2">Total estimado</td>
      <td>R$ 1.210,00</td>
    </tr>
  </tfoot>
</table>
```

* `<tr>` é a linha, `<td>` a célula de dado e `<th>` a célula de cabeçalho;
* `scope="col"` ou `scope="row"` informa se aquele cabeçalho descreve a coluna ou
  a linha, o que permite ao leitor de tela anunciar "Processador, preço, 890
  reais" ao chegar na célula;
* `<caption>` dá título à tabela;
* `colspan` e `rowspan` fazem uma célula ocupar várias colunas ou linhas.

## 9. Semântica de estrutura

Todo conteúdo pode ser embrulhado em `<div>`, elemento genérico sem significado
nenhum. O resultado funciona e é ruim: a página vira uma pilha de caixas
indistinguíveis para quem não enxerga o layout.

Os elementos estruturais do HTML5 dizem qual o papel de cada bloco:

* `<header>`: cabeçalho da página ou de uma seção;
* `<nav>`: bloco de links de navegação;
* `<main>`: conteúdo principal, único por página;
* `<article>`: conteúdo que faz sentido sozinho, como uma notícia, um comentário
  ou o cartão de um produto;
* `<section>`: agrupamento temático, normalmente com um título próprio;
* `<aside>`: conteúdo lateral, relacionado mas não essencial;
* `<footer>`: rodapé da página ou de uma seção.

Uma página típica:

```html
<body>
  <header>
    <h1>Catálogo de Produtos</h1>
    <nav>
      <ul>
        <li><a href="index.html">Início</a></li>
        <li><a href="catalogo.html">Catálogo</a></li>
        <li><a href="contato.html">Contato</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section>
      <h2>Destaques</h2>
      <article>
        <h3>Teclado mecânico</h3>
        <p>Layout ABNT2, iluminação branca.</p>
      </article>
    </section>
  </main>

  <aside>
    <h2>Mais vendidos</h2>
  </aside>

  <footer>
    <p>Fulano de Tal, GRR20260000.</p>
  </footer>
</body>
```

Como escolher: pergunte o que o bloco **é**, e não onde ele aparece na tela. Se o
trecho pode ser recortado e publicado sozinho, é `<article>`. Se agrupa assuntos
sob um título, é `<section>`. Quando nenhum elemento descreve o papel do bloco e
você só precisa de um agrupamento para estilizar depois, aí sim use `<div>`, e
`<span>` para o equivalente dentro de uma linha de texto.

## 10. Acessibilidade

Acessibilidade é a página funcionar para quem navega por leitor de tela, por
teclado ou com baixa visão. No Brasil, sites de órgãos públicos são obrigados a
seguir o eMAG e a Lei Brasileira de Inclusão. Fora isso, quase tudo o que precisamos
para tornar uma página acessível vem do simples uso de marcações corretas.

O que já foi tratado:

* `lang` no elemento `html`;
* títulos em ordem, sem pular níveis;
* `alt` em toda imagem;
* texto de link que diz para onde leva;
* `<label>` associado a cada campo de formulário (próxima seção);
* elementos estruturais no lugar de `<div>` genérico, o que dá ao leitor de tela
  os pontos de referência para saltar direto ao conteúdo principal.

Dois cuidados adicionais:

* **Teclado**: links, botões e campos são acessíveis por `Tab` porque são
  elementos nativos. Uma `<div>` com um clique preso por JavaScript não é, e
  ninguém que use apenas teclado consegue ativá-la. Prefira sempre o elemento
  nativo;
* **ARIA**: atributos como `aria-label` e `role` complementam o significado
  quando o HTML não dá conta. Usados sem necessidade, atrapalham. A primeira
  regra da especificação de ARIA é não usar ARIA quando existe elemento HTML com
  aquele papel.

Para testar sem instalar nada: navegue na sua própria página usando somente a
tecla `Tab` e verifique se dá para alcançar tudo, e se dá para ver onde está o
foco.

## 11. Formulários

Formulário é como o usuário envia dados ao servidor.

```html
<form action="processa.php" method="post">
  <label for="nome">Nome completo</label>
  <input type="text" id="nome" name="nome" required>

  <label for="email">E-mail</label>
  <input type="email" id="email" name="email" required>

  <button type="submit">Enviar</button>
</form>
```

* `action` é a URL que vai receber os dados. Vazio ou ausente, é a própria
  página;
* `method` é o método HTTP. Com `get`, os dados vão na *query string* da URL, à
  vista e no histórico. Com `post`, vão no corpo da requisição. Cadastro e senha
  usam `post`; busca e filtro costumam usar `get`, justamente para o resultado
  ficar num endereço que pode ser copiado;
* `name` é o nome do dado no envio. **Campo sem `name` não é enviado.** O que o
  servidor recebe é o par `name=valor`;
* `id` identifica o elemento dentro do documento, e serve para o `<label>`
  apontar para ele com `for`.

O `<label>` associado faz duas coisas: o leitor de tela anuncia o rótulo ao
chegar no campo, e o clique no texto move o foco para o campo, o que ajuda quem
tem dificuldade motora. Rótulo solto num `<p>` ao lado não produz nenhum dos
dois efeitos.

### Tipos de campo

O atributo `type` do `<input>` muda o teclado exibido no celular, o controle
apresentado e a validação aplicada:

```html
<input type="text">      <!-- texto de uma linha -->
<input type="email">     <!-- exige formato de e-mail -->
<input type="password">  <!-- oculta os caracteres na tela -->
<input type="number">    <!-- aceita number, com min, max e step -->
<input type="date">      <!-- calendário nativo -->
<input type="tel">
<input type="url">
<input type="file">
<input type="hidden">
```

`type="password"` esconde o texto na tela e nada mais: o valor segue na
requisição como os demais. O que protege o envio é o HTTPS.

### Escolha entre opções

```html
<fieldset>
  <legend>Categoria</legend>

  <input type="radio" id="cat-perif" name="categoria" value="perifericos">
  <label for="cat-perif">Periféricos</label>

  <input type="radio" id="cat-comp" name="categoria" value="componentes">
  <label for="cat-comp">Componentes</label>
</fieldset>

<label for="uf">Estado</label>
<select id="uf" name="uf">
  <option value="">Selecione...</option>
  <option value="PR">Paraná</option>
  <option value="SC">Santa Catarina</option>
</select>

<input type="checkbox" id="aceite" name="aceite" value="sim">
<label for="aceite">Aceito receber novidades</label>

<label for="mensagem">Mensagem</label>
<textarea id="mensagem" name="mensagem" rows="5" maxlength="200"></textarea>
```

Botões de rádio do mesmo grupo compartilham o `name` e diferem no `value`: é o
`name` que os torna mutuamente exclusivos. `<fieldset>` agrupa campos
relacionados e `<legend>` nomeia o grupo, o que o leitor de tela anuncia antes
das opções.

Caixa de seleção só é enviada quando marcada, e o servidor recebe o `value`
declarado. Desmarcada, o campo simplesmente não aparece na requisição.

### Botões

```html
<button type="submit">Cadastrar</button>
<button type="reset">Limpar</button>
<button type="button">Sem efeito próprio, para uso com JavaScript</button>
```

O padrão de um `<button>` dentro de um formulário é `submit`, e esquecer o
`type` num botão destinado ao JavaScript faz a página recarregar sozinha, engano
frequente na aula de JS.

## 12. Validação nativa

O navegador consegue barrar o envio de dados fora do formato esperado, sem uma
linha de JavaScript:

```html
<input type="text" name="sku" pattern="[A-Z]{3}[0-9]{4}"
       title="Três letras maiúsculas seguidas de quatro números" required>

<input type="number" name="quantidade" min="0" max="100" step="1" required>

<input type="text" name="nome" minlength="3" maxlength="60" required>
```

* `required` impede o envio com o campo vazio;
* `minlength` e `maxlength` limitam o comprimento do texto;
* `min`, `max` e `step` limitam números e datas;
* `pattern` exige uma expressão regular. Use `title` junto, porque é esse texto
  que o navegador mostra ao recusar o valor;
* `placeholder` é apenas um exemplo dentro do campo, que some ao digitar. Não
  substitui o `<label>`.

> **Validação no cliente não é segurança.** Toda essa checagem roda no navegador
> do usuário, que pode desativá-la pelas ferramentas de desenvolvedor, ou nem
> usar navegador: um `curl` monta a requisição à mão, com o conteúdo que quiser.
> A validação nativa existe para dar retorno imediato e evitar viagem
> desnecessária ao servidor. O servidor **sempre** revalida tudo, assunto da
> aula de PHP.

## 13. O que o HTML não faz

O HTML descreve o conteúdo e a estrutura. Aparência é responsabilidade do CSS, e
comportamento é do JavaScript.

Por isso não se usa `<center>`, `<font>`, o atributo `bgcolor` e afins, todos
obsoletos, nem se escolhe um elemento pelo efeito visual que ele produz por
padrão. Escolher `<h4>` porque a letra sai do tamanho desejado quebra o índice da
página; o tamanho certo sai de uma linha de CSS na semana que vem.

Um teste rápido de qualidade da sua marcação: desative o CSS da página e leia o
que sobrou. Se o documento continua fazendo sentido de cima a baixo, a estrutura
está correta.

---

# Parte prática

## 14. Sua primeira página

Crie a pasta do exercício, abra-a no VS Code e crie o arquivo `index.html`. A
extensão precisa ser `.html`.

Digite o esqueleto da seção 3 à mão, sem copiar e colar, e acrescente um `<h1>` e
um parágrafo. Salve e abra o arquivo pelo navegador, com duplo clique ou
`Ctrl+O`. O endereço mostrado começa com `file://`.

Deixe a janela do editor e a do navegador lado a lado. A cada alteração salva,
recarregue com `F5` e confira o efeito.

Confira também os caminhos relativos: crie uma pasta `imagens/` ao lado do
`index.html`, ponha um arquivo dentro e referencie-o com
`src="imagens/arquivo.jpg"`. Se a imagem não aparecer, o caminho está errado, e
o texto do `alt` aparece no lugar dela.

> **Atalho útil.** No VS Code, digitar `!` numa linha vazia de um arquivo `.html`
> e apertar `Tab` gera o esqueleto completo. Use depois de já saber escrevê-lo,
> e não antes.

## 15. Ver a árvore nas ferramentas do navegador

Com a página aberta, aperte `F12` e vá até a aba **Elements** (ou **Inspetor**,
no Firefox).

1. Expanda os nós e reconheça a árvore da seção 4;
2. Passe o mouse pelos elementos da lista e observe o destaque correspondente na
   página;
3. Clique com o botão direito em algo na página e escolha *Inspecionar* para
   saltar direto ao elemento;
4. Dê um duplo clique no texto de um elemento e altere-o. A página muda na hora,
   e o seu arquivo não. Recarregue e a alteração some, porque você editou a
   árvore em memória, e não o documento.

Agora um experimento sobre recuperação de erro. Escreva no `body`:

```html
<p>Parágrafo <strong>com marcação <em>cruzada</strong> aqui</em>.</p>
```

Salve, recarregue e compare o arquivo com a árvore exibida no inspetor. O
navegador reorganizou os elementos por conta própria.

## 16. Formulário, e os dados chegando ao servidor

Crie `teste-form.html` com um formulário apontado para um serviço de eco:

```html
<form action="https://postman-echo.com/get" method="get">
  <label for="nome">Nome</label>
  <input type="text" id="nome" name="nome" required>

  <label for="periodo">Período</label>
  <input type="number" id="periodo" name="periodo" min="1" max="8">

  <button type="submit">Enviar</button>
</form>
```

Envie e observe a URL de destino: os pares `nome=...&periodo=...` foram parar na
*query string*, montados pelo navegador a partir dos atributos `name`.

Faça três alterações, uma de cada vez, e observe o resultado:

1. Remova o atributo `name` do campo `periodo` e envie de novo. O valor some da
   URL;
2. Troque o `method` para `post` e o `action` para `https://postman-echo.com/post`.
   Os dados saem da URL. Para vê-los, abra a aba **Network** das ferramentas de
   desenvolvedor, clique na requisição e procure o corpo enviado;
3. Deixe o campo obrigatório vazio e tente enviar. O navegador barra o envio, e
   nada chega à rede.

## 17. Validar o documento

Abra o [W3C Markup Validation Service](https://validator.w3.org/#validate_by_input),
cole o conteúdo do seu arquivo e valide.

Erros comuns nesta altura: falta do `<!DOCTYPE html>`, ausência de `charset`,
`<img>` sem `alt`, tag não fechada, `id` repetido no mesmo documento, `<li>` fora
de `<ul>` ou `<ol>`, e elemento de bloco dentro de `<p>`.

Corrija até a página passar sem erros e sem avisos. Documento válido não garante
página boa, mas página com erro de sintaxe fica à mercê da recuperação de erro do
navegador, que muda de navegador para navegador.

---

## Exercícios

**Estes exercícios valem nota** e compõem o item *Exercícios em sala* da média.
**A entrega vai até 27/08, às 23h59**, por `push` no GitLab. A tarefa é maior do
que o tempo de aula: comece hoje, com o professor por perto para tirar dúvidas, e
termine durante a semana.

O enunciado fica no `README.md` do repositório-modelo da tarefa, e o endereço
desse repositório está na atividade correspondente na **UFPR Virtual**.

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
[MDN](https://developer.mozilla.org/pt-BR/docs/Web/HTML) e o professor.

O que a tarefa produz é a base do que será cobrado na **Entrega 1 do trabalho
prático**, em 11/09.

---

## Resumo

* O servidor envia texto; o navegador interpreta esse texto e monta a árvore que
  de fato exibe.
* Um elemento é abertura, conteúdo e fechamento, com atributos na abertura.
  Aninhamento errado não gera mensagem de erro, gera árvore diferente da
  esperada.
* O esqueleto do documento carrega decisões concretas: `lang`, `charset` e
  `viewport` mudam pronúncia, acentuação e layout no celular.
* Escolha o elemento pelo significado do conteúdo. `<div>` é o que sobra quando
  nenhum outro descreve o papel do bloco.
* A maior parte da acessibilidade vem de marcação correta: `alt`, `label`,
  títulos em ordem e texto de link informativo.
* Em formulário, `name` é o que vai para a requisição, `id` é o que liga o campo
  ao `<label>`, e `method` decide entre URL e corpo da mensagem.
* Validação nativa serve ao usuário, e não à segurança. O servidor revalida.
* Aparência é CSS, comportamento é JavaScript. Sem CSS, o documento ainda precisa
  fazer sentido.
