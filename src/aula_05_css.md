# Aula CSS3: Fundamentos, Layout e Responsividade

## Bibliografia Recomendada

* [MDN - CSS: Cascading Style Sheets](https://developer.mozilla.org/pt-BR/docs/Web/CSS)
* [CSS-Tricks - A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
* [CSS-Tricks - A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)

## Cascading Style Sheets (CSS3)

O CSS (Cascading Style Sheets) é a linguagem de folha de estilo utilizada para descrever a apresentação de um documento escrito em HTML. Sua principal função no desenvolvimento web é garantir a estrita separação entre a estrutura/semântica (HTML) e a apresentação visual (CSS).

### Formas de Adicionar CSS ao HTML

Existem três métodos de integração, sendo o uso de arquivos externos o único recomendado para sistemas em produção:

1. **Atributo Style (Inline):** Aplica o estilo diretamente na tag. Tem a maior especificidade, mas fere o princípio de separação de responsabilidades. Não recomendado.
```html
<p style="color: red; text-align: center;">Texto do parágrafo</p>

```


2. **Tag `<style>` (Interno):** Inserido no `<head>` do documento HTML.
```html
<head>
    <style>
        p { color: red; }
    </style>
</head>

```


3. **Arquivo Externo (Recomendado):** Importação de um arquivo `.css` via tag `<link>` no `<head>`.
```html
<head>
    <link rel="stylesheet" href="estilo.css">
</head>

```


## Sintaxe e Seletores

A sintaxe baseia-se em uma declaração composta por uma **propriedade** e um **valor**, encapsulada em um bloco vinculado a um **seletor**.

```css
/* Seletor de Elemento (Tag) */
h1 {
    color: white;
    text-align: center;
}

/* Seletor de Classe (.) - Reutilizável em múltiplos elementos */
.alerta {
    color: red;
    font-weight: bold;
}

/* Seletor de ID (#) - Deve ser único na página */
#menu-principal {
    background-color: #333;
}

/* Pseudo-classes (Estado do elemento) */
a:hover {
    text-decoration: underline;
}

```

## Cascata e Especificidade

O termo "Cascading" refere-se ao algoritmo que o navegador utiliza para determinar qual regra CSS será aplicada quando há conflitos.

A resolução de conflitos segue a hierarquia de **Especificidade**:

1. **Inline style:** Maior peso.
2. **IDs:** Peso alto (ex: `#cabecalho`).
3. **Classes, atributos e pseudo-classes:** Peso médio (ex: `.card`, `[type="text"]`, `:hover`).
4. **Elementos e pseudo-elementos:** Menor peso (ex: `h1`, `div`, `::before`).

Se duas regras tiverem a mesma especificidade, a que foi declarada **por último** no código (cascata) prevalece.

## Box Model (Modelo de Caixa) e Unidades de Medida

No CSS, todo elemento HTML é renderizado como uma caixa retangular.

* **Content:** A área onde o texto ou imagem é exibido.
* **Padding:** Espaçamento interno (entre o conteúdo e a borda).
* **Border:** A borda que envolve o padding e o conteúdo.
* **Margin:** Espaçamento externo (afasta o elemento dos elementos adjacentes).

**Boa prática:** Utilize a propriedade `box-sizing: border-box;` no reset global para garantir que o cálculo da largura (`width`) e altura (`height`) inclua o `padding` e a `border`, evitando quebras de layout.

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

```


### Unidades de Medida Modernas

O uso exclusivo de pixels (`px`) é desencorajado em layouts modernos.

* **`rem`**: Relativo ao tamanho da fonte do elemento raiz (`<html>`). Ideal para fontes e margens, pois respeita a configuração de acessibilidade do navegador do usuário.
* **`%`**: Percentual em relação ao elemento pai.
* **`vw` / `vh**`: Largura (`viewport width`) e altura (`viewport height`) relativas à janela do navegador.

---

## Exercícios Práticos: Fundamentos de CSS3 (Pré-Layout)

Criar um arquivo `index.html` e um arquivo `style.css` na mesma pasta, realizando a vinculação através da tag `<link>`.

### Exercício 01: Teste de Especificidade e Cascata

**Objetivo:** Compreender a hierarquia de aplicação de estilos e o cálculo de especificidade do navegador.

**HTML base (`index.html`):**

```html
<div class="artigo" id="noticia-principal">
    <h2 class="titulo">Lançamento do Novo Sistema</h2>
    <p class="texto destaque">O sistema entrará em produção na próxima semana.</p>
</div>

```

**Requisitos (CSS):**

1. Crie uma regra utilizando o seletor de tag `p` e defina a cor do texto como azul.
2. Crie uma regra utilizando o seletor de classe `.texto` e defina a cor do texto como verde.
3. Crie uma regra utilizando o seletor de classe `.destaque` e defina a cor do texto como laranja.
4. Crie uma regra encadeada `.artigo p.destaque` e defina a cor como roxo.
5. Adicione um estilo *inline* diretamente na tag `<p>` no HTML definindo a cor como vermelho.
6. **Análise:** O aluno deve documentar em forma de comentário (`/* ... */`) qual cor prevaleceu em cada etapa e o motivo técnico com base na regra de especificidade.


### Exercício 02: Tipografia, Cores e Unidades Relativas (`rem`)

**Objetivo:** Aplicar fontes externas e utilizar a unidade de medida `rem` atrelada ao elemento raiz.

**Requisitos (CSS):**

1. Importe a fonte "Roboto" ou "Open Sans" do Google Fonts via `@import` no início do arquivo CSS.
2. Defina no seletor `html` o `font-size: 16px;`.
3. Defina a fonte importada para o seletor `body` e aplique uma cor de fundo utilizando a notação Hexadecimal (ex: `#f4f4f9`).
4. Estilize um elemento `<h1>`: defina o tamanho da fonte para `2.5rem` e a cor do texto utilizando a notação `rgba()` com 80% de opacidade.
5. Estilize um elemento `<p>`: defina o tamanho para `1rem`, o espaçamento entre linhas (`line-height`) para `1.5` e o alinhamento de texto como justificado.


### Exercício 03: O Box Model e `box-sizing`

**Objetivo:** Visualizar o impacto do `padding` e `border` no tamanho total de um elemento e corrigir o comportamento padrão com `box-sizing`.

**HTML base:**

```html
<div class="caixa padrao">Caixa 1: Content-Box</div>
<div class="caixa corrigida">Caixa 2: Border-Box</div>

```

**Requisitos (CSS):**

1. Para a classe `.caixa`, defina `width: 300px`, `height: 150px`, cor de fundo amarela e `margin-bottom: 20px`.
2. Para ambas as caixas, adicione um `padding` de `30px` e uma `border` sólida preta de `5px`.
3. Na classe `.corrigida`, adicione a propriedade `box-sizing: border-box;`.
4. **Análise:** O aluno deve inspecionar os elementos no navegador (F12) e anotar as dimensões finais renderizadas de cada caixa.


### Exercício 04: Display e Centralização de Bloco

**Objetivo:** Manipular os valores da propriedade `display` (`block`, `inline`, `inline-block`) e aplicar centralização horizontal com margens automáticas.

**HTML base:**

```html
<section class="container">
    <a href="#" class="botao">Clique Aqui</a>
    <a href="#" class="botao">Saiba Mais</a>
</section>

```

**Requisitos (CSS):**

1. Para a classe `.container`, defina `width: 50%`, uma cor de fundo cinza clara e aplique `margin: 0 auto;` para centralizar a seção horizontalmente na página.
2. A tag `<a>` por padrão é `inline`. Altere a classe `.botao` para `display: inline-block;`.
3. Defina para os botões:
* `padding: 10px 20px;`
* `margin-right: 15px;`
* Cor de fundo azul, texto branco e remova o sublinhado (`text-decoration`).


4. Adicione uma pseudo-classe `:hover` aos botões para alterar a cor de fundo para um azul mais escuro quando o mouse passar sobre eles.

---

## Layouts Modernos: Flexbox e CSS Grid

Modelos de layout atuais substituem o uso de `float` e tabelas para posicionamento.

### 1. Flexbox (Layout Unidimensional)

Projetado para alinhar itens em uma única dimensão (linha ou coluna) e distribuir o espaço dinamicamente.

```css
.flex-container {
    display: flex;
    flex-direction: row;            /* Organiza em linha */
    justify-content: space-between; /* Espaçamento horizontal */
    align-items: center;            /* Alinhamento vertical cruzado */
    gap: 15px;                      /* Espaçamento entre os filhos (CSS moderno) */
}

```

### 2. CSS Grid (Layout Bidimensional)

Projetado para organizar elementos simultaneamente em linhas e colunas.

```css
.grid-container {
    display: grid;
    /* Cria 3 colunas: as das extremidades com 1 fração de espaço e a central com 2 */
    grid-template-columns: 1fr 2fr 1fr;
    grid-template-rows: auto 200px;
    gap: 20px;
}

```

## Design Responsivo (Media Queries)

A responsividade garante que a interface se adapte a diferentes tamanhos de tela. O padrão de mercado é a abordagem **Mobile-first**: o CSS base é escrito para telas pequenas, e media queries são utilizadas para adaptar o layout para telas maiores.

```css
/* Estilo base (Mobile - telas pequenas) */
.coluna {
    width: 100%;
}

/* Breakpoint para Tablets e Desktops (mínimo de 768px de largura) */
@media screen and (min-width: 768px) {
    .coluna {
        width: 50%; /* Passa a ocupar metade da tela */
    }
}

```
