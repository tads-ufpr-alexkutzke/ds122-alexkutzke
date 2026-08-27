# CSS3: fundamentos

[Slides desta aula (PDF)](slides/aula_05_css.pdf)

## Bibliografia recomendada para o tema

* [MDN - CSS](https://developer.mozilla.org/pt-BR/docs/Web/CSS);
* [MDN - Fundamentos de estilização](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Core/Styling_basics);
* [MDN - Referência de seletores e propriedades](https://developer.mozilla.org/pt-BR/docs/Web/CSS/Reference);
* SILVA, Maurício Samy. **CSS3**. São Paulo: Novatec, 2012 (bibliografia básica da disciplina);
* [CSS Snapshot 2026](https://www.w3.org/TR/css/), a lista do que o W3C considera estável hoje.

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Ligar uma folha de estilo externa a um documento HTML e justificar por que essa
   é a forma usada;
2. Escrever seletores de tipo, classe, `id`, descendente, atributo e
   pseudo-classe, escolhendo o mais adequado a cada caso;
3. Prever qual declaração vence um conflito, aplicando herança, especificidade e
   ordem de aparição;
4. Calcular a largura que um elemento ocupa na tela a partir do modelo de caixa, e
   dizer o que `box-sizing` muda nessa conta;
5. Escolher entre `px`, `rem`, `em`, `%` e unidades de *viewport*, justificando a
   escolha;
6. Declarar cores nas notações usadas na prática e verificar o contraste entre
   texto e fundo;
7. Definir a tipografia da página: família com pilha de reserva, tamanho, peso e
   altura de linha;
8. Usar o inspetor do navegador para descobrir por que uma regra não foi
   aplicada.

---

# Parte teórica

## 1. A página que já tinha aparência

Abra no navegador uma das páginas que você escreveu na aula passada. Ela não tem
nenhum CSS, e mesmo assim o `<h1>` aparece grande e em negrito, os itens de
`<ul>` aparecem com marcador, e o link aparece azul e sublinhado.

Essa aparência não vem do HTML. Vem de uma folha de estilo que o próprio
navegador aplica a todo documento, chamada folha de estilo do agente de usuário.
Ela existe para que um documento sem CSS ainda seja legível.

Dá para ver essa folha. Aperte `F12`, clique num `<h1>` na aba **Elements** (ou
**Inspetor**, no Firefox) e olhe o painel de estilos à direita: junto das regras
do documento aparecem regras marcadas como *user agent stylesheet*, com
`display: block`, `font-size: 2em` e `font-weight: bold`.

Escrever CSS é substituir as decisões dessa folha pelas suas. O navegador continua
aplicando a dele para tudo que você não disser.

## 2. Onde o CSS entra no documento

Há três formas de associar estilo a um documento, e elas não são equivalentes.

**Arquivo externo, ligado pelo `<link>`.** É a forma usada na disciplina e em
produção.

```html
<head>
  <meta charset="utf-8">
  <title>Catálogo</title>
  <link rel="stylesheet" href="css/estilo.css">
</head>
```

O arquivo `estilo.css` contém apenas CSS, sem nenhuma tag HTML dentro. O caminho
em `href` segue as mesmas regras dos caminhos relativos que você usou em `<img>` e
`<a>`. Um arquivo pode atender o site inteiro, e o navegador o guarda em cache depois
do primeiro carregamento.

**Elemento `<style>` no próprio documento.** Serve para um teste rápido ou para
uma página única. O estilo vale só naquele arquivo, e duplica em toda página que
precisar dele.

```html
<style>
  p { color: #333333; }
</style>
```

**Atributo `style` na tag.** Aplica declarações a um elemento específico.

```html
<p style="color: red;">Texto</p>
```

Essa forma mistura apresentação e estrutura no mesmo arquivo, obriga a repetir a
declaração em cada elemento, e cria um problema adicional de especificidade que
veremos na seção 7. Não use, exceto quando o valor for calculado por JavaScript
em tempo de execução, situação que aparece mais adiante na disciplina.

## 3. Anatomia de uma regra

```css
/* isto é um comentário e é a única forma de comentar em CSS */
h1 {
  color: #1a3d6d;
  font-size: 2rem;
}
```

O nome de cada parte importa, porque é como as mensagens de erro e a documentação
se referem a elas:

* `h1` é o **seletor**, e diz a quais elementos a regra se aplica;
* o que está entre chaves é o **bloco de declarações**;
* `color: #1a3d6d;` é uma **declaração**, formada por uma **propriedade**
  (`color`), dois pontos, um **valor** (`#1a3d6d`) e ponto e vírgula;
* o conjunto seletor mais bloco é uma **regra**.

O ponto e vírgula separa declarações. Ele é opcional na última do bloco, e mesmo
assim escreva sempre: quando você acrescentar uma linha depois, o erro já estará
lá.

CSS não avisa quando você erra. Se a propriedade não existir, se o valor for
inválido ou se faltar uma chave, o navegador descarta o que não entendeu e segue
adiante, sem mensagem nenhuma na tela. Mais sobre isso na seção 16.

## 4. Seletores

Um seletor descreve um conjunto de elementos do documento. Estes são os que
resolvem a maior parte dos casos:

```css
/* tipo: todos os parágrafos */
p { line-height: 1.6; }

/* classe: todo elemento com class="destaque" */
.destaque { background-color: #fff3cd; }

/* id: o único elemento com id="rodape" */
#rodape { font-size: 0.875rem; }

/* universal: qualquer elemento */
* { box-sizing: border-box; }

/* agrupamento: a mesma declaração para vários seletores */
h1, h2, h3 { font-family: Georgia, serif; }

/* descendente: um <a> em qualquer nível dentro do <nav> */
nav a { text-decoration: none; }

/* filho direto: um <li> que é filho imediato do <ul> */
ul > li { margin-bottom: 0.5rem; }

/* atributo: campos de texto */
input[type="text"] { border: 1px solid #999999; }

/* pseudo-classe: estado do elemento */
a:hover { text-decoration: underline; }
a:focus-visible { outline: 3px solid #1a73e8; }

/* pseudo-elemento: uma parte do elemento que não existe no HTML */
p::first-line { font-variant: small-caps; }
```

A pseudo-classe usa dois pontos e descreve um estado: `:hover` enquanto o ponteiro
está sobre o elemento, `:focus-visible` quando o elemento recebeu foco pelo
teclado, `:checked` numa caixa marcada. O pseudo-elemento usa dois sinais de dois
pontos e alcança um pedaço do elemento que não tem tag própria.

> Um cuidado de acessibilidade que se aplica desde já: quem navega por teclado
> enxerga onde está pelo contorno de foco. Remover esse contorno com
> `outline: none` deixa a pessoa perdida na página. Se o contorno padrão não
> combina com o seu visual, troque a aparência dele, e não o apague.

### Classe e `id`: quando usar cada um

A classe é reutilizável e pode aparecer em quantos elementos você quiser. O `id`
é único no documento, é o alvo de âncoras (`href="#rodape"`) e é o que o
JavaScript costuma usar para achar o elemento.

Para estilo, prefira classe. O `id` tem especificidade alta demais, e uma regra
presa a ele se torna difícil de sobrescrever depois, como a seção 7 mostra em
números.

## 5. Herança

Algumas propriedades passam do elemento para os descendentes. Se você declara

```css
body {
  font-family: Verdana, sans-serif;
  color: #222222;
}
```

todo texto da página aparece em Verdana e cinza escuro, mesmo sem nenhuma regra
para `p`, `li` ou `td`.

Herdam-se sobretudo propriedades de texto: `color`, `font-family`, `font-size`,
`font-weight`, `line-height`, `text-align`, `list-style`.

Não se herdam as propriedades de caixa: `margin`, `padding`, `border`,
`background`, `width`, `height`. Um `padding` no `body` não vira `padding` em cada
parágrafo dentro dele, o que é o comportamento desejado.

Na dúvida, a página da propriedade na MDN traz um quadro de resumo com a linha
*Herdada: sim* ou *não*.

## 6. A cascata

O primeiro C de CSS é de *Cascading*. É o nome do algoritmo que decide qual
declaração vale quando duas regras dizem coisas diferentes sobre a mesma
propriedade do mesmo elemento.

O navegador resolve o conflito nesta ordem:

1. **Origem e importância.** As declarações do autor da página vencem as do
   navegador. Uma declaração marcada com `!important` sobe para um patamar acima
   de todas as normais;
2. **Especificidade.** Entre declarações da mesma origem, vence o seletor mais
   específico, medido como na seção 7;
3. **Ordem de aparição.** Havendo empate de especificidade, vence a que aparece
   por último.

Sobre o `!important`, ele existe e funciona. Porém seu uso cria um problema maior 
do que o que ele resolve:
porque a única forma de vencer um `!important` depois é outro
`!important`. Nos arquivos CSS da disciplina, considere-o proibido. Quando você
sentir vontade de usá-lo, o que está faltando é entender por que a sua regra
perdeu, e essa investigação é a seção 16.

## 7. Especificidade

A especificidade de um seletor é uma contagem em três casas, escrita aqui como
(A, B, C):

* **A** conta os `id` no seletor;
* **B** conta as classes, os atributos e as pseudo-classes;
* **C** conta os tipos de elemento e os pseudo-elementos.

O seletor universal `*` não conta nada. A comparação é feita da esquerda para a
direita: qualquer valor em A vence qualquer quantidade de B, e um único B vence
qualquer quantidade de C.

| Seletor | A | B | C | Leitura |
|---|---|---|---|---|
| `p` | 0 | 0 | 1 | um tipo |
| `.texto` | 0 | 1 | 0 | uma classe |
| `p.texto` | 0 | 1 | 1 | um tipo e uma classe |
| `.artigo p.destaque` | 0 | 2 | 1 | duas classes e um tipo |
| `#noticia p` | 1 | 0 | 1 | um id e um tipo |
| `input[type="text"]:focus` | 0 | 2 | 1 | um atributo, uma pseudo-classe, um tipo |

Aplicando a este documento:

```html
<div class="artigo" id="noticia">
  <p class="texto destaque">O sistema entra em produção na próxima semana.</p>
</div>
```

com estas cinco regras, nesta ordem:

```css
p                   { color: blue; }    /* (0,0,1) */
.texto              { color: green; }   /* (0,1,0) */
.destaque           { color: orange; }  /* (0,1,0) */
.artigo p.destaque  { color: purple; }  /* (0,2,1) */
#noticia p          { color: red; }     /* (1,0,1) */
```

o parágrafo fica **vermelho**. O `#noticia p` tem A igual a 1, e nenhuma
quantidade de classes alcança isso. Note também que, entre `.texto` e `.destaque`,
que empatam em (0,1,0), venceria `.destaque`, por vir depois no arquivo. A ordem
das classes no atributo `class` do HTML não influencia nada.

Acima de tudo isso está o atributo `style` na tag, que vence qualquer seletor. É
por isso que o estilo *inline* atrapalha: ele tira de você a possibilidade de
sobrescrever aquela declaração pela folha.

A consequência prática é escrever seletores tão simples quanto o problema
permitir. Uma folha construída sobre classes de um nível é fácil de ajustar
depois. Uma folha cheia de `#menu .lista li a span` obriga cada correção futura a
ser mais específica que a anterior.

## 8. O modelo de caixa

Todo elemento é desenhado como uma caixa retangular, formada por quatro camadas,
de dentro para fora:

```
+-------------------------------------------+
|                  margin                   |
|   +-----------------------------------+   |
|   |              border               |   |
|   |   +---------------------------+   |   |
|   |   |         padding           |   |   |
|   |   |   +-------------------+   |   |   |
|   |   |   |     content       |   |   |   |
|   |   |   +-------------------+   |   |   |
|   |   +---------------------------+   |   |
|   +-----------------------------------+   |
+-------------------------------------------+
```

* **content**: onde o texto ou a imagem aparece. É o que `width` e `height`
  dimensionam por padrão;
* **padding**: espaço interno, entre o conteúdo e a borda. Recebe a cor de fundo
  do elemento;
* **border**: a borda, com espessura, estilo e cor;
* **margin**: espaço externo, que afasta a caixa das vizinhas. É sempre
  transparente.

Cada camada aceita valor por lado. As formas abreviadas seguem o sentido horário
a partir do topo:

```css
padding: 10px;                  /* os quatro lados */
padding: 10px 20px;             /* topo e base | direita e esquerda */
padding: 10px 20px 5px;         /* topo | direita e esquerda | base */
padding: 10px 20px 5px 0;       /* topo | direita | base | esquerda */
padding-left: 20px;             /* um lado só */
border: 5px solid #000000;      /* espessura, estilo e cor */
```

### A largura ocupada pela caixa

Considere:

```css
.caixa {
  width: 300px;
  padding: 30px;
  border: 5px solid #000000;
}
```

A largura que essa caixa ocupa na tela vem da soma das camadas:

```
300 (content) + 30 + 30 (padding) + 5 + 5 (border) = 370px
```

O `width` dimensiona apenas o conteúdo. Isso quebra layouts o tempo todo: duas
colunas de `width: 50%` com padding somam mais que a largura disponível, e a
segunda desce para baixo da primeira.

A correção é mudar o significado de `width` com `box-sizing`:

```css
.caixa {
  box-sizing: border-box;
  width: 300px;
  padding: 30px;
  border: 5px solid #000000;
}
```

Agora a caixa ocupa exatamente 300px, e o conteúdo é espremido para
`300 - 60 - 10 = 230px`. É o comportamento que quase todo mundo espera, e por isso
a linha abaixo abre praticamente toda folha de estilo moderna:

```css
* {
  box-sizing: border-box;
}
```

> Os dois valores acima foram conferidos medindo os pixels do que o navegador
> desenhou: 370px sem `box-sizing` e 300px com ele. Você pode repetir essa medição na
> seção 17.

## 9. Colapso de margens verticais

Duas caixas empilhadas, a primeira com `margin-bottom: 30px` e a segunda com
`margin-top: 20px`. O espaço entre elas é de 30px, e não de 50px.

Margens verticais adjacentes se fundem, e a maior prevalece. O nome disso é
colapso de margens. Acontece também entre um elemento e o pai, quando não há
borda nem padding separando os dois.

Margens horizontais nunca colapsam. As verticais também deixam de colapsar dentro
de um contêiner Flexbox ou Grid, o que é um dos motivos de esses modelos, tema do
próximo encontro, tornarem o espaçamento mais previsível.

Duas estratégia para facilitar: adotar a convenção de espaçar sempre para o mesmo
lado, usando só `margin-bottom` entre irmãos, ou usar `gap` no contêiner, quando
ele for Flexbox ou Grid.

## 10. `display`

A propriedade `display` decide como a caixa se comporta em relação às vizinhas.
Quatro valores são importantes nessa aula:

| Valor | Ocupa a linha inteira | Aceita `width` e `height` | Margem vertical empurra vizinhos |
|---|---|---|---|
| `block` | sim | sim | sim |
| `inline` | não | não | não |
| `inline-block` | não | sim | sim |
| `none` | o elemento some da página | | |

`<div>`, `<p>`, `<h1>` e `<section>` são `block` por padrão. `<a>`, `<span>`,
`<strong>` e `<img>` são `inline`.

O caso mais comum na prática é transformar um link em botão. Como `<a>` é
`inline`, o `padding` vertical não afasta as linhas vizinhas e `width` é ignorado:

```css
.botao {
  display: inline-block;
  padding: 0.75rem 1.5rem;
  background-color: #1a3d6d;
  color: #ffffff;
  text-decoration: none;
}
```

> Sobre `display: none`: o elemento desaparece da página e também deixa de ser lido
> por leitores de tela. Para esconder algo visualmente mantendo-o acessível, existem
> técnicas próprias, e `display: none` não é uma delas.

## 11. Unidades de medida

```css
.exemplo {
  width: 300px;    /* pixel CSS, medida absoluta */
  width: 50%;      /* relativo ao elemento pai */
  font-size: 1rem; /* relativo à fonte do elemento raiz <html> */
  padding: 0.5em;  /* relativo à fonte do próprio elemento */
  height: 100vh;   /* 100% da altura da janela */
  width: 80vw;     /* 80% da largura da janela */
}
```

**`px`** é previsível e serve bem para bordas e para detalhes que não devem
escalar. Não serve para tamanho de fonte, pelo motivo do quadro abaixo.

**`rem`** é a unidade de referência para texto e espaçamento. Ela se apoia no
tamanho de fonte do elemento `<html>`, que por padrão é o tamanho que a pessoa
configurou no navegador, tipicamente 16px. Uma folha escrita em `rem` acompanha
esse ajuste. Uma folha escrita em `px` ignora.

**`em`** se apoia no tamanho de fonte do próprio elemento, o que faz os valores se
acumularem em elementos aninhados: uma lista dentro de outra, ambas com
`font-size: 0.9em`, chega a 0.81 do original. Útil para padding de botão, que deve
acompanhar o texto do botão. Trabalhoso para o resto.

**`%`** depende da propriedade: em `width` é relativo à largura do pai, em
`font-size` ao tamanho de fonte do pai.

**`vw` e `vh`** são centésimos da largura e da altura da janela. Aparecem em
seções que ocupam a tela cheia.

> **Não escreva `html { font-size: 16px; }`.** Essa linha aparece em muito
> material antigo e anula o ajuste de tamanho de fonte de quem enxerga pouco, que
> é uma das configurações de acessibilidade mais usadas que existem. O valor
> padrão do `<html>` já é a preferência da pessoa. Se quiser deixar isso explícito
> na folha, use `html { font-size: 100%; }`.

## 12. Cores e contraste

Quatro notações resolvem tudo o que faremos:

```css
color: red;                          /* palavra-chave, 148 nomes definidos */
color: #1a3d6d;                      /* hexadecimal, RR GG BB */
color: #fff;                         /* forma curta de #ffffff */
color: rgb(26, 61, 109);             /* vermelho, verde e azul de 0 a 255 */
color: rgba(26, 61, 109, 0.5);       /* o quarto valor é a opacidade */
color: hsl(212, 61%, 26%);           /* matiz, saturação e luminosidade */
```

O hexadecimal é o que você vai encontrar em qualquer paleta pronta e no seletor de
cores do editor. O `hsl()` é o mais fácil de ajustar à mão: para obter a versão
mais escura de um azul para o `:hover`, basta reduzir o terceiro valor.

Onde declarar cor:

```css
color: #222222;             /* cor do texto */
background-color: #f7f7f7;  /* cor de fundo da caixa */
border-color: #cccccc;      /* cor da borda */
```

### Contraste mínimo entre texto e fundo

Texto cinza claro sobre fundo branco fica ilegível para quem tem baixa visão, para
quem usa a tela sob luz forte e para uma parcela das pessoas com daltonismo. A
diretriz internacional de acessibilidade, a WCAG, define isso em números: a razão
de contraste entre a cor do texto e a cor de fundo deve ser de pelo menos
**4.5:1** para texto normal, e de 3:1 para texto grande, a partir de 24px, ou de
18.66px em negrito.

Confira as suas escolhas no
[verificador de contraste do WebAIM](https://webaim.org/resources/contrastchecker/),
colando os dois hexadecimais. O inspetor do navegador também mostra a razão ao
lado da cor, como você vê na seção 18.

Isso liga diretamente ao **ODS 4** (educação de qualidade) e ao **ODS 10**
(redução das desigualdades): uma página com contraste insuficiente exclui parte
do público sem que o autor perceba, porque no monitor dele o texto está legível.

## 13. Tipografia

```css
body {
  font-family: "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #222222;
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
}

.legenda {
  font-size: 0.875rem;
  font-style: italic;
  text-align: center;
}
```

**`font-family` recebe uma lista, e não um nome.** O navegador tenta a primeira
fonte, e passa à seguinte se ela não estiver instalada na máquina. A lista precisa
terminar em uma família genérica, `sans-serif`, `serif` ou `monospace`, que o
sistema sempre resolve. Nomes com espaço vão entre aspas.

**`line-height` sem unidade.** Escreva `1.6`, e não `1.6rem`. O valor sem unidade
é herdado como fator, e cada elemento o multiplica pelo próprio tamanho de fonte,
que é o comportamento correto quando os tamanhos variam. Entre 1.4 e 1.6 é a faixa
confortável para texto corrido.

**`font-weight`** aceita `normal` (400), `bold` (700) e os múltiplos de 100, quando
a fonte tiver esses cortes.

### Fonte externa do Google Fonts

Escolha a família em [fonts.google.com](https://fonts.google.com/), pegue o
trecho `<link>` que o site oferece e cole no `<head>`, antes da sua folha:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
```

E use o nome no CSS, mantendo a pilha de reserva:

```css
body { font-family: Roboto, Arial, sans-serif; }
```

Cada fonte pedida é um arquivo a mais para baixar. Peça dois, no máximo três, e
teste a página com a rede lenta, condição normal para boa parte do público.

## 14. O que ainda falta para montar um layout

Com o conteúdo desta aula você controla a aparência de cada caixa. O que ainda não dá para
fazer é posicionar as caixas umas em relação às outras: pôr o menu na horizontal,
alinhar três cartões de produto lado a lado, fazer a coluna lateral acompanhar a
altura do conteúdo.

Isso é assunto do próximo encontro, com Flexbox, Grid e *media queries*, e é o que
falta para a Entrega 1 do trabalho prático. Os fundamentos de hoje continuam
valendo lá dentro: a caixa que o Flexbox posiciona é a mesma caixa da seção 8.

---

# Parte prática

Trabalhe sobre as páginas que você entregou na tarefa de HTML. Se elas não
estiverem prontas, use um documento novo com um `<h1>`, dois parágrafos e uma
lista de links.

## 15. A primeira folha de estilo

Na pasta do projeto, crie a pasta `css/` e, dentro dela, o arquivo `estilo.css`.
Ligue-o no `<head>` de `index.html`:

```html
<link rel="stylesheet" href="css/estilo.css">
```

No `estilo.css`, escreva:

```css
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Verdana, Arial, sans-serif;
  line-height: 1.6;
  color: #222222;
  background-color: #f7f7f7;
}
```

Salve, recarregue com `F5` e confira que a fonte mudou. Se nada aconteceu, o
caminho do `href` está errado: abra a aba **Network** do inspetor, recarregue e
procure a linha do `estilo.css`. O status 404 confirma o caminho errado.

Faça o mesmo `<link>` nas outras páginas. O mesmo arquivo atende todas.

## 16. Descobrir por que a regra não pegou

No HTML, dê ao `<nav>` do menu o atributo `class="menu"`. Depois acrescente ao
final do `estilo.css` estas três regras, exatamente nesta ordem:

```css
nav a { color: #1a3d6d; }
a { color: #cc0000; }
.menu a { color: #008000; }
```

Antes de recarregar, decida qual cor os links do menu vão ficar. Depois
recarregue e confira.

Agora clique com o botão direito num link do menu, escolha *Inspecionar*, e olhe o
painel **Styles**:

1. As regras aparecem da mais forte para a mais fraca, e as declarações perdedoras
   aparecem **riscadas**. O risco é a resposta à pergunta "por que a minha regra
   não pegou";
2. Ao lado de cada regra, o navegador mostra o arquivo e a linha onde ela está;
3. A aba **Computed** mostra o valor final de cada propriedade, já resolvido, e
   permite abrir a lista de todas as declarações que disputaram aquela
   propriedade;
4. Desmarque a caixinha ao lado de uma declaração para desligá-la e ver o efeito
   na hora.

Faça três experimentos, um de cada vez, prevendo o resultado antes:

* Troque `.menu a` por `#menu a` no CSS (e ajuste o HTML para ter
  `id="menu"`). O que muda na disputa?
* Acrescente `style="color: purple"` na tag do link. Quem vence agora?
* Volte tudo e acrescente `!important` na regra mais fraca das três.

## 17. Medir a caixa no inspetor

Crie um arquivo `teste-caixa.html` com estas duas caixas:

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="utf-8">
  <title>Modelo de caixa</title>
  <style>
    body { margin: 0; }
    .caixa {
      width: 300px;
      padding: 30px;
      border: 5px solid #000000;
      background-color: #ffd400;
      margin-bottom: 20px;
    }
    .corrigida {
      box-sizing: border-box;
      background-color: #00a0ff;
    }
  </style>
</head>
<body>
  <div class="caixa">Sem box-sizing</div>
  <div class="caixa corrigida">Com box-sizing: border-box</div>
</body>
</html>
```

Inspecione cada `<div>` e vá até o fim do painel **Computed**, onde aparece o
diagrama do modelo de caixa com os quatro valores. Confirme:

* a primeira caixa ocupa **370px** de largura, com `content` de 300px;
* a segunda ocupa **300px**, com `content` de 230px.

Passe o mouse sobre o diagrama e observe o destaque de cada camada na página.

Depois troque a `margin-bottom: 20px` da primeira caixa por uma
`margin-top: 20px` na segunda, mantendo `margin-bottom: 30px` na primeira. Meça o
espaço entre elas com a régua do inspetor, ou pela posição de cada caixa: são
30px, e não 50px, pelo colapso da seção 9.

## 18. Contraste e validação

Escolha a cor de fundo do cabeçalho e a cor do texto dentro dele. Cole os dois
hexadecimais no
[verificador do WebAIM](https://webaim.org/resources/contrastchecker/) e confira
se a razão passa de 4.5:1. Se não passar, escureça o fundo ou clareie o texto até
passar.

O inspetor mostra a mesma informação: clique no quadradinho de cor ao lado de uma
declaração `color`, e o seletor de cores exibe a razão de contraste com o fundo,
com um aviso quando o valor está abaixo do mínimo.

Por fim, valide a folha em
[jigsaw.w3.org/css-validator](https://jigsaw.w3.org/css-validator/#validate-by-input),
pela aba *By direct input*. Como o navegador descarta em silêncio o que não
entende, o validador costuma ser o único lugar onde um nome de propriedade
digitado errado aparece.

---

## Exercícios

**Estes exercícios valem nota** e compõem o item *Exercícios em sala* da média.
**A entrega vai até 03/09, às 23h59**, por `push` no GitLab. A tarefa é maior do
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
[MDN](https://developer.mozilla.org/pt-BR/docs/Web/CSS) e o professor.

---

## Resumo

* Toda página já vem estilizada pela folha do navegador. O seu CSS substitui
  aquelas decisões, e o que você não disser continua vindo de lá.
* Folha externa ligada por `<link>` é a forma usada. O atributo `style` na tag
  vence qualquer seletor e tira de você a chance de corrigir pela folha.
* Conflito se resolve por origem e importância, depois por especificidade, depois
  por ordem de aparição. Um `id` vence qualquer quantidade de classes.
* Seletor simples é seletor que dá para sobrescrever depois. Prefira classe a
  `id` para estilo.
* Propriedades de texto são herdadas; propriedades de caixa não são.
* `width` dimensiona só o conteúdo. Com `padding` e `border`, a caixa ocupa mais
  do que o valor declarado, até que você escreva `box-sizing: border-box`.
* Margens verticais adjacentes colapsam, e a maior prevalece.
* `rem` para texto e espaçamento, porque acompanha a configuração de quem lê. Não
  fixe o tamanho de fonte do `<html>` em pixels.
* Contraste de 4.5:1 para texto normal é requisito de acessibilidade, verificável
  em segundos.
* CSS inválido não gera erro na tela. Quando a regra não pegou, a resposta está no
  painel **Styles** do inspetor, na declaração riscada.
