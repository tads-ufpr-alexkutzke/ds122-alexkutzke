# CSS3: layout e responsividade

## Bibliografia recomendada para o tema

* [MDN - CSS](https://developer.mozilla.org/pt-BR/docs/Web/CSS);
* [CSS-Tricks - A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/);
* [CSS-Tricks - A Complete Guide to Grid](https://css-tricks.com/complete-guide-css-grid-layout/).

> Este material continua os [fundamentos de CSS](./aula_05_00_css.md): seletores,
> cascata, especificidade, modelo de caixa, unidades, cores e tipografia. A caixa
> que o Flexbox e o Grid posicionam é a mesma caixa daquele conteúdo.

## Layouts modernos: Flexbox e CSS Grid

Os modelos de layout atuais substituem o uso de `float` e de tabelas para
posicionamento.

### Flexbox: layout em uma dimensão

Projetado para alinhar itens em uma única dimensão, linha ou coluna, e distribuir
o espaço disponível entre eles.

```css
.flex-container {
    display: flex;
    flex-direction: row;            /* organiza em linha */
    justify-content: space-between; /* distribuição no eixo principal */
    align-items: center;            /* alinhamento no eixo transversal */
    gap: 15px;                      /* espaçamento entre os filhos */
}
```

### CSS Grid: layout em duas dimensões

Projetado para organizar elementos simultaneamente em linhas e colunas.

```css
.grid-container {
    display: grid;
    /* três colunas: as das extremidades com 1 fração e a central com 2 */
    grid-template-columns: 1fr 2fr 1fr;
    grid-template-rows: auto 200px;
    gap: 20px;
}
```

## Design responsivo com media queries

A responsividade garante que a interface se adapte a diferentes tamanhos de tela.
O padrão de mercado é a abordagem *mobile-first*: o CSS base é escrito para telas
pequenas, e as *media queries* adaptam o layout para telas maiores.

```css
/* estilo base, telas pequenas */
.coluna {
    width: 100%;
}

/* a partir de 768px de largura */
@media screen and (min-width: 768px) {
    .coluna {
        width: 50%;
    }
}
```
