# Aula — Frameworks Front-end e Bootstrap 5

## Bibliografia recomendada para o tema:

### Frameworks
* [Bootstrap 5 Documentation](https://getbootstrap.com/docs/5.3/): framework front-end mais popular;
  * [Bootstrap 5 Tutorial — w3schools](https://www.w3schools.com/bootstrap5/);
* [Tailwind CSS](https://tailwindcss.com/): abordagem utility-first;
* [Materialize](http://materializecss.com/): baseado no Material Design.

### Outros recursos
* [Shoelace](https://shoelace.style)

## Frameworks Front-end

**Front-end** é o termo utilizado para se fazer referência à parte de uma aplicação Web que é executada na máquina cliente. Ou seja, em geral, envolvem as tecnologias HTML, CSS e Javascript e se tratam, em grande medida, de detalhes da **interface** dos sistemas Web.

**Frameworks front-end** são conjuntos de especificações, arquivos e bibliotecas utilizados para facilitar o desenvolvimento da interface de aplicações Web. Por exemplo, um framework front-end pode ser um conjunto de arquivos CSS e JS que podem ser utilizados em diferentes aplicações Web, desde que algumas regras para sua utilização sejam seguidas.

Em geral, o uso de um framework front-end envolve os seguintes passos:

* Carregamento dos arquivos CSS e JS do framework (por CDN ou download);
* Organização dos arquivos HTML de acordo com as especificações do framework;
* Utilização de classes CSS e componentes disponibilizados pelo framework.

O framework mais utilizado atualmente é o **Bootstrap**, em sua versão 5.

## Bootstrap 5

**Bootstrap 5** é a versão mais recente do framework front-end mais popular para desenvolvimento Web. Com ele é possível criar interfaces **responsivas** com abordagem **mobile-first**.

Suas principais características:

* Facilidade de uso;
* Responsividade;
* Abordagem "mobile-first";
* Compatibilidade com navegadores modernos;
* **Não depende de jQuery** — utiliza JavaScript puro (Vanilla JS);
* Suporte nativo a Customização via variáveis CSS e SASS;
* Novos componentes e utilitários.

Para utilizar o Bootstrap 5, carregue seus arquivos a partir de CDN:

```html
<!-- Bootstrap 5 CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Bootstrap 5 JS Bundle (inclui Popper para dropdowns, tooltips, etc.) -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" defer></script>
```

> **Nota:** Diferente das versões anteriores, o Bootstrap 5 **não requer jQuery**. O JavaScript é escrito em Vanilla JS.

A partir daí, basta utilizar as classes CSS e os componentes do Bootstrap nos seus documentos HTML.

### Exemplo de página com Bootstrap 5

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Página Bootstrap 5</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

  <!-- Hero Section -->
  <div class="bg-primary text-white text-center py-5">
    <h1>Minha Primeira Página Bootstrap</h1>
    <p class="lead">Redimensione esta página responsiva para ver o efeito!</p>
  </div>

  <div class="container my-5">
    <div class="row">
      <div class="col-md-4">
        <h3>Coluna 1</h3>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
      </div>
      <div class="col-md-4">
        <h3>Coluna 2</h3>
        <p>Ut enim ad minim veniam, quis nostrud exercitation ullamco.</p>
      </div>
      <div class="col-md-4">
        <h3>Coluna 3</h3>
        <p>Duis aute irure dolor in reprehenderit in voluptate velit.</p>
      </div>
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

> **O que mudou do Bootstrap 3 para o 5?** A classe `jumbotron` foi removida — agora usamos classes utilitárias (`bg-primary`, `py-5`, `text-center`) para obter o mesmo efeito. Margens e paddings são controlados por classes como `my-5` (margin no eixo Y) em vez de CSS customizado.

### Sistema de Grid

O sistema de grid do Bootstrap divide a tela em **12 colunas** virtuais. Qualquer subconjunto dessas colunas pode ser aninhado em novas divisões de 12 partes.

![bootstrap grid system](https://www.teaching-materials.org/bootstrap-hosting-github/image/12-grid-overlay.png)

Esse sistema permite a criação facilitada de colunas responsivas que se adaptam automaticamente ao tamanho da tela.

Para utilizar o grid, usa-se classes no formato `col-BREAKPOINT-LARGURA`:

* **Breakpoints:**
  * ` ` (sem infixo): extra small — smartphones (padrão, < 576px)
  * `sm`: small — tablets (≥ 576px)
  * `md`: medium — desktops (≥ 768px)
  * `lg`: large — desktops grandes (≥ 992px)
  * `xl`: extra large (≥ 1200px)
  * `xxl`: extra extra large (≥ 1400px)
* **Largura:** de 1 a 12 (ou `auto`)

> **Diferença do Bootstrap 3:** A classe `col-xs-*` foi substituída simplesmente por `col-*` (sem infixo). Exemplo: `col-xs-6` → `col-6`.

#### Exemplos de Grid

```html
<div class="container">
  <!-- Linha com 12 colunas de largura 1 -->
  <div class="row">
    <div class="col-1 border">1</div>
    <div class="col-1 border">2</div>
    <div class="col-1 border">3</div>
    <div class="col-1 border">4</div>
    <div class="col-1 border">5</div>
    <div class="col-1 border">6</div>
    <div class="col-1 border">7</div>
    <div class="col-1 border">8</div>
    <div class="col-1 border">9</div>
    <div class="col-1 border">10</div>
    <div class="col-1 border">11</div>
    <div class="col-1 border">12</div>
  </div>

  <!-- 8 + 4 -->
  <div class="row mt-3">
    <div class="col-md-8 border">.col-md-8</div>
    <div class="col-md-4 border">.col-md-4</div>
  </div>

  <!-- 4 + 4 + 4 -->
  <div class="row mt-3">
    <div class="col-md-4 border">.col-md-4</div>
    <div class="col-md-4 border">.col-md-4</div>
    <div class="col-md-4 border">.col-md-4</div>
  </div>

  <!-- 6 + 6 -->
  <div class="row mt-3">
    <div class="col-md-6 border">.col-md-6</div>
    <div class="col-md-6 border">.col-md-6</div>
  </div>
</div>
```

### Breakpoints múltiplos (design responsivo)

É possível especificar larguras diferentes para cada breakpoint:

```html
<!-- Em mobile (col-12): ocupa toda a largura. Em desktop (col-md-8): ocupa 8 colunas -->
<div class="row">
  <div class="col-12 col-md-8 border">.col-12 .col-md-8</div>
  <div class="col-6 col-md-4 border">.col-6 .col-md-4</div>
</div>

<!-- 50% em mobile, 33.3% em desktop -->
<div class="row mt-3">
  <div class="col-6 col-md-4 border">Coluna 1</div>
  <div class="col-6 col-md-4 border">Coluna 2</div>
  <div class="col-6 col-md-4 border">Coluna 3</div>
</div>
```

### Componentes principais do Bootstrap 5

O Bootstrap 5 oferece dezenas de componentes prontos. Os mais utilizados:

* **Navbar:** barra de navegação responsiva com suporte a menu "hamburger" em mobile.
* **Cards:** contêiner flexível para exibir conteúdo (substitui os antigos `panel` e `thumbnail`).
* **Buttons:** botões estilizados com variantes de cor (`btn-primary`, `btn-danger`, `btn-outline-*`).
* **Forms:** campos de formulário com aparência consistente, validação e layout em grid.
* **Tables:** tabelas estilizadas com variantes (`table-striped`, `table-hover`, `table-responsive`).
* **Alerts:** mensagens de feedback coloridas com suporte a fechamento.
* **Modal:** janelas de diálogo sobrepostas (popups).
* **Badges:** pequenos indicadores numéricos ou de status.
* **Spinners:** indicadores de carregamento animados (substitui o antigo `glyphicon`).

#### Exemplo: Cards

```html
<div class="row">
  <div class="col-md-4 mb-3">
    <div class="card">
      <img src="produto1.jpg" class="card-img-top" alt="Produto A">
      <div class="card-body">
        <h5 class="card-title">Produto A</h5>
        <p class="card-text">Descrição breve do produto.</p>
        <a href="#" class="btn btn-primary">Comprar</a>
      </div>
    </div>
  </div>

  <div class="col-md-4 mb-3">
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">Produto B</h5>
        <p class="card-text">Outro produto em destaque.</p>
        <a href="#" class="btn btn-outline-secondary">Detalhes</a>
      </div>
    </div>
  </div>
</div>
```

#### Exemplo: Navbar responsiva

```html
<nav class="navbar navbar-expand-lg bg-dark navbar-dark">
  <div class="container">
    <a class="navbar-brand" href="#">Minha Loja</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#menuPrincipal">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="menuPrincipal">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="#">Home</a></li>
        <li class="nav-item"><a class="nav-link" href="#">Produtos</a></li>
        <li class="nav-item"><a class="nav-link" href="#">Contato</a></li>
      </ul>
    </div>
  </div>
</nav>
```

> **Atenção:** No Bootstrap 5, atributos `data-*` foram renomeados para `data-bs-*`. Exemplo: `data-toggle` → `data-bs-toggle`, `data-target` → `data-bs-target`.

### Utilitários (Utilities)

O Bootstrap 5 oferece classes utilitárias para ajustes rápidos sem escrever CSS customizado:

```html
<!-- Espaçamento (margin e padding) -->
<div class="mt-3 mb-5 ms-2 me-4 pt-2 pb-3 ps-4 pe-1">
  Conteúdo com margens e paddings controlados por classes.
</div>
<!-- m = margin, p = padding; t/b/s/e = top/bottom/start/end; 0-5 = intensidade -->

<!-- Cores de fundo -->
<div class="bg-primary text-white p-3">Fundo azul</div>
<div class="bg-success text-white p-3">Fundo verde</div>
<div class="bg-warning text-dark p-3">Fundo amarelo</div>

<!-- Texto -->
<p class="text-center fw-bold text-muted">Texto centralizado, negrito, cinza</p>

<!-- Bordas -->
<div class="border rounded p-3">Caixa com borda arredondada</div>

<!-- Display e flex -->
<div class="d-flex justify-content-between align-items-center">
  <span>Esquerda</span>
  <span>Direita</span>
</div>
```

### Resumo das principais diferenças: Bootstrap 3 → Bootstrap 5

| Bootstrap 3 | Bootstrap 5 |
|---|---|
| Depende de jQuery | JavaScript puro (Vanilla JS) |
| `col-xs-*` | `col-*` (sem infixo) |
| `col-md-push-*` / `col-md-pull-*` | `order-md-*` |
| `.jumbotron` | Classes utilitárias (`py-5`, `bg-light`) |
| `.panel`, `.thumbnail`, `.well` | `.card` |
| `data-toggle`, `data-target` | `data-bs-toggle`, `data-bs-target` |
| `.glyphicon` | Ícones removidos (use bibliotecas externas como Bootstrap Icons) |
| `.img-responsive` | `.img-fluid` |
| `.hidden-*` | `.d-none`, `.d-md-block` |
| `.pull-left` / `.pull-right` | `.float-start` / `.float-end` |

Para saber mais sobre funcionalidades do Bootstrap 5, consulte a [documentação oficial](https://getbootstrap.com/docs/5.3/).
