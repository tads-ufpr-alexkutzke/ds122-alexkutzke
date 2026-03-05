# Aula 02 - HTML5: Estrutura Semântica e Acessibilidade

## Bibliografia Recomendada

* [MDN Web Docs - HTML](https://developer.mozilla.org/pt-BR/docs/Web/HTML)
* [W3C - HTML5 Semantic Elements](https://www.w3schools.com/html/html5_semantic_elements.asp)

---

## HyperText Markup Language

O HTML (HyperText Markup Language), é, como o nome diz, uma linguagem de marcação
para criação de documento hipertexto.

### Estrutura Base e Declaração de Idioma

Diferente das versões anteriores, o HTML5 simplificou o `doctype`. É obrigatório definir o atributo `lang` na tag `html` para acessibilidade (leitores de tela) e SEO.

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Título do Documento</title>
</head>
<body>
    
</body>
</html>

```


## Tags e atributos

A linguagem conta com um conjunto de
palavras-chave denominadas `tags`. De maniera geral, uma `tag` possui o seguinte
formato:

```html
<nome_tag atributo1="valor1" atributo2="valor2" ...>conteúdo</nome_tag>
```

Onde `<nome_tag ...>` é chamada de tag de abertura e `</nome_tag>` é denominada
tag de fechamento. Vale ressaltar que nem todas as tads possuem fechamento,
nesse caso, a sintaxe é a seguinte:

```html
<nome_tag atributo1="valor1" atributo2="valor2" ... />
```

Cada tag pode receber zero ou mais **atributos**. Um atributo indica
características referentes a tag em questão. Por exemplo:

```html
<a href="http://www.tads.ufpr.br">Moodle do TADS</a>
```

A tag acima irá produzir um link (ou âncora) para a url `http://www.tads.ufpr.br`,
sendo exibida na página com o texto `Moodle do TADS`. Algo semelhante a isso:
[Moodle do TADS](http://www.tads.ufpr.br).

Isso ocorre pois a tag `a` indica a criação de um link e, por meio do parâmentro
`href`, o navegador sabe para qual URL o link deve levar ao ser clicado.

## Características do HTML

O HTML deve ser utilizado para especificar apenas duas coisas:
* O **conteúdo** do documento (títulos, parágrafos, figuras, etc.);
* A **estrutura** do documento (isso é um título, aquilo é um link).

Ou seja, o HTML deve ser utilizado para definir a **semântica** do seu conteúdo,
seu significado. **Não se deve utilizar o HTML para indicar nenhum tipo de formatação
ou posicionamento do conteúdo**.

## Sintaxe e Boas Práticas

* **Case Sensitivity:** Embora o navegador aceite maiúsculas, o padrão de mercado e as normas da W3C exigem tags e atributos em **minúsculo**.
* **Aspas:** Atributos devem ser envolvidos por aspas duplas (`""`).
* **Self-closing tags:** No HTML5, a barra em tags vazias (ex: `<br />`) é opcional, mas o uso de `<br>` ou `<img>` é preferível por simplicidade.
* **Ferramental:** Utilize editores de texto para produzir seus arquivos HTML. O **VS Code** pode ser uma boa opção, mas você pode utilizar qual preferir.

---

## HTML5

O HTML5 é a quinta e atual versão principal da HyperText Markup Language, o padrão da W3C para a estruturação de conteúdos na Web. Diferente de suas versões anteriores, o HTML5 evoluiu de uma simples linguagem de marcação para uma plataforma de desenvolvimento completa, introduzindo elementos semânticos (como `<article>`, `<section>` e `<nav>`), suporte nativo a multimídia (tags `<video>` e `<audio>`), e APIs poderosas para armazenamento local e geolocalização. Sua arquitetura foi projetada para reduzir a dependência de plugins proprietários e garantir que as aplicações web funcionem de forma consistente e responsiva em diversos dispositivos e navegadores.

---

## Práticas modernas de Semântica e Estruturação do DOM

As principais tags introduzidas no **HTML5** que o diferenciam das versões anteriores (como o HTML 4.01) focam em substituir o uso genérico de `<div>` por elementos que descrevem a função do conteúdo para navegadores e tecnologias assistivas.

### Tags de Estrutura Semântica

* **`<header>`**: Define o cabeçalho de uma página ou seção, geralmente contendo logotipos e navegação.
* **`<nav>`**: Identifica blocos que contêm links de navegação.
* **`<main>`**: Delimita o conteúdo principal e exclusivo do documento.
* **`<article>`**: Representa uma composição autônoma (post, comentário, notícia).
* **`<section>`**: Agrupa conteúdos logicamente relacionados em um mesmo tema.
* **`<aside>`**: Define conteúdos tangenciais, como barras laterais ou anúncios.
* **`<footer>`**: Define o rodapé de uma página ou seção.

### Tags de Mídia e Gráficos

* **`<video>`**: Permite a reprodução de arquivos de vídeo nativamente, sem necessidade de plugins como o Flash.
* **`<audio>`**: Proporciona suporte nativo para execução de arquivos sonoros.
* **`<canvas>`**: Define uma área para renderização de gráficos e animações dinâmicas via JavaScript.
* **`<svg>`**: Permite a incorporação direta de gráficos vetoriais escaláveis no código HTML.

### Tags de Organização de Conteúdo e Formulários

* **`<figure>` e `<figcaption>`**: Agrupam uma mídia (imagem, gráfico) e sua respectiva legenda.
* **`<mark>`**: Utilizada para destacar ou realçar trechos de texto.
* **`<time>`**: Representa datas e horários em um formato legível por máquinas.
* **`<datalist>`**: Define uma lista de opções pré-definidas para controles de entrada de formulário.

---

## Tags de Conteúdo e Acessibilidade

### Imagens e Atributo `alt`

O atributo `alt` é obrigatório para acessibilidade. Descreve a imagem para deficientes visuais e mecanismos de busca.

```html
<img src="logo-ufpr.png" alt="Logotipo da Universidade Federal do Paraná">
```

### Links (Âncoras)

Para links externos, utilize `rel="external"` ou `target="_blank"`.

```html
<a href="https://www.tads.ufpr.br" target="_blank" rel="noopener">Portal TADS</a>
```

---

## Formulários e Validação Nativa

O HTML5 introduziu tipos de input que realizam validação no lado do cliente sem necessidade de JavaScript inicial.

### Exemplo de Formulário Técnico

```html
<form action="processar.php" method="POST">
    <fieldset>
        <legend>Dados Pessoais</legend>
        
        <label for="nome">Nome Completo:</label>
        <input type="text" id="nome" name="nome" required placeholder="Digite seu nome">

        <label for="email">E-mail:</label>
        <input type="email" id="email" name="email" required>

        <label for="nascimento">Data de Nascimento:</label>
        <input type="date" id="nascimento" name="nascimento">
    </fieldset>

    <fieldset>
        <legend>Preferências</legend>
        
        <label>Gênero:</label>
        <input type="radio" id="m" name="genero" value="M"> <label for="m">Masculino</label>
        <input type="radio" id="f" name="genero" value="F"> <label for="f">Feminino</label>

        <label for="veiculo">Veículo:</label>
        <select id="veiculo" name="veiculo">
            <option value="">Selecione...</option>
            <option value="carro">Carro</option>
            <option value="moto">Moto</option>
        </select>
    </fieldset>

    <button type="submit">Enviar Dados</button>
</form>

```

### Principais Atributos de Validação

* `required`: Torna o campo obrigatório.
* `pattern`: Define uma Expressão Regular (Regex) para validação.
* `min` / `max`: Define limites numéricos ou de data.
* `step`: Define o intervalo de incremento para números.

---

## Elementos de Bloco vs. Em Linha (Inline)

* **Block-level:** Ocupam toda a largura disponível e começam em uma nova linha. Exemplos: `<div>`, `<h1>`, `<p>`, `<ul>`, `<li>`, `<header>`.
* **Inline:** Ocupam apenas a largura necessária e não quebram linha. Exemplos: `<span>`, `<a>`, `<strong>`, `<em>`, `<img>`.

---

## Exercícios Práticos: Estrutura e Semântica HTML5

Os exercícios abaixo devem ser realizados no **VS Code**, seguindo a indentação padrão (2 ou 4 espaços) e as normas de acessibilidade discutidas.

---

### Exercício 01: Esqueleto Semântico

Crie um arquivo chamado `index.html` que represente a estrutura de um portal de notícias. O documento deve conter:

1. **Header**: Contendo um `<h1>` com o nome do portal e um `<nav>` com uma lista não ordenada (`<ul>`) de categorias (Home, Brasil, Tecnologia, Contato).
2. **Main**: Contendo dois elementos `<article>`.
* Cada `article` deve ter um `<h2>` (título da notícia) e um `<p>` (resumo).


3. **Aside**: Uma barra lateral com links para "Postagens mais lidas".
4. **Footer**: Informações de copyright e endereço.

---

### Exercício 02: Formulário de Cadastro de Produtos

Desenvolva um formulário para um sistema de estoque (`cadastro_produto.html`). Utilize as tags de formulário e validações nativas:

* **Nome do Produto**: Campo de texto obrigatório.
* **Código SKU**: Campo de texto que aceite apenas o padrão de 3 letras seguidas de 4 números (Dica: use o atributo `pattern="[A-Z]{3}[0-9]{4}"`).
* **Quantidade em Estoque**: Campo numérico (`number`) com valor mínimo de 0 e máximo de 100.
* **Categoria**: Um componente `<select>` com as opções: Eletrônicos, Móveis e Alimentos.
* **Descrição**: Um `<textarea>` com limite de 200 caracteres.
* **Data de Recebimento**: Campo do tipo `date`.
* **Botões**: Um botão para "Limpar" (`type="reset"`) e outro para "Cadastrar" (`type="submit"`).

---

### Exercício 03: Tabela de Dados Técnicos

Crie uma página que exiba uma tabela de especificações de hardware. A tabela deve seguir a estrutura semântica correta:

* Utilize as tags `<thead>`, `<tbody>` e `<tfoot>`.
* Colunas: Componente, Especificação e Preço Médio.
* Inclua pelo menos 3 linhas de dados (ex: Processador, Memória RAM, SSD).
* No `<tfoot>`, utilize o atributo `colspan` para criar uma célula que ocupe as duas primeiras colunas com o texto "Total Estimado" e exiba o valor na terceira coluna.

---

### Exercício 04: Acessibilidade e Mídia

Implemente uma página de galeria simples:

1. Insira uma imagem de um componente de hardware.
2. Garanta que o atributo `alt` descreva a imagem tecnicamente.
3. Utilize a tag `<figure>` para envolver a imagem e a tag `<figcaption>` para adicionar uma legenda visível abaixo dela.
4. Crie um link que, ao ser clicado, faça o download de um manual em PDF (Dica: utilize o atributo `download` na tag `<a>`).

---

### Validação

Para validar seus exercícios, utilize o [W3C Markup Validation Service](https://validator.w3.org/#validate_by_upload). O código não deve apresentar erros ou avisos (warnings).
