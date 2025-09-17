# Exercícios Práticos: JavaScript, DOM e Eventos

## Nível 1: Fundamentos da Interatividade

### Exercício 1: Alerta de Boas-Vindas Personalizado

* **Objetivo:** Introduzir a captura de valor de um `input` e a resposta a um clique de botão.
* **Descrição:** Crie uma página HTML com:
    1.  Um campo de texto (`<input type="text">`) com um `id` para que o usuário possa digitar seu nome.
    2.  Um botão (`<button>`) com o texto "Enviar".
    3.  Ao clicar no botão, seu script JavaScript deve ler o nome digitado no campo de texto e exibir um alerta de boas-vindas na tela, como por exemplo: `Olá, [Nome do Usuário]! Bem-vindo(a)!`.
* **Conceitos-chave:** `document.getElementById()`, `addEventListener('click', ...)`, `.value`, `alert()`.
* **Desafio Bônus:** Se o usuário clicar no botão sem ter digitado nada, exiba um alerta diferente pedindo para que ele preencha o nome.

---

### Exercício 2: Paleta de Cores

* **Objetivo:** Praticar a manipulação de estilos CSS através do JavaScript.
* **Descrição:** Desenvolva uma página com:
    1.  Três ou mais botões, cada um representando uma cor (ex: "Vermelho", "Verde", "Azul").
    2.  Ao clicar em um dos botões, o `<body>` da página deve ter sua cor de fundo (`backgroundColor`) alterada para a cor correspondente.
* **Conceitos-chave:** `document.querySelector()`, `element.style`, manipulação de múltiplos elementos.
* **Desafio Bônus:** Adicione um botão "Aleatório" que, ao ser clicado, muda a cor de fundo para uma cor hexadecimal gerada aleatoriamente (ex: `#f1f5f8`).

---

### Exercício 3: Contador de Cliques

* **Objetivo:** Manipular o conteúdo de texto de um elemento e trabalhar com variáveis em JavaScript.
* **Descrição:** Crie uma página com:
    1.  Um parágrafo (`<p>`) que inicia exibindo o número `0`.
    2.  Um botão com o texto "Incrementar".
    3.  Cada vez que o botão for clicado, o número exibido no parágrafo deve ser incrementado em 1.
* **Conceitos-chave:** `textContent` ou `innerText`, declaração e atualização de variáveis.
* **Desafio Bônus:** Adicione um segundo botão, "Decrementar", que diminui o valor do contador. O contador nunca deve ficar abaixo de zero.

---

## Nível 2: Manipulação Dinâmica e Formulários

### Exercício 4: Lista de Tarefas (To-Do List)

* **Objetivo:** Praticar a criação, adição e remoção de elementos no DOM de forma dinâmica.
* **Descrição:** Construa uma interface de lista de tarefas que permita ao usuário:
    1.  Digitar uma nova tarefa em um campo de texto (`<input type="text">`).
    2.  Clicar em um botão "Adicionar" para que a tarefa seja inserida como um novo item (`<li>`) em uma lista não ordenada (`<ul>`) na página.
    3.  Cada item da lista deve ter um botão "Remover" ao seu lado. Ao ser clicado, este botão deve excluir o item da lista correspondente.
* **Conceitos-chave:** `document.createElement()`, `element.appendChild()`, `element.remove()`, `event.target`.
* **Desafio Bônus:** Adicione uma funcionalidade para que, ao clicar no texto de uma tarefa, ela seja marcada como "concluída" (por exemplo, aplicando um estilo de texto riscado com `text-decoration: line-through`).

---

### Exercício 5: Validação de Formulário em Tempo Real

* **Objetivo:** Utilizar eventos de formulário e teclado para fornecer feedback instantâneo ao usuário.
* **Descrição:** Crie um formulário de cadastro com campos para **nome**, **e-mail** e **senha**. Implemente as seguintes validações que acontecem enquanto o usuário digita:
    1.  **Nome:** Não pode estar vazio.
    2.  **E-mail:** Deve conter o caractere `@`.
    3.  **Senha:** Deve ter no mínimo 8 caracteres.
    Abaixo de cada campo, exiba uma mensagem de erro (ex: em um `<p>` com cor vermelha) assim que a regra for violada. A mensagem deve desaparecer quando o campo se tornar válido novamente.
* **Conceitos-chave:** `addEventListener('keyup', ...)` ou `addEventListener('input', ...)` , `element.classList` para estilização, condicionais, `.length`, `.includes()`.
* **Desafio Bônus:** Adicione um campo de "Confirmar Senha" e valide se o seu valor é idêntico ao do campo "Senha". Além disso, desabilite o botão de "Cadastrar" do formulário enquanto houver algum erro de validação.

---

### Exercício 6: Galeria de Imagens com Modal

* **Objetivo:** Criar uma experiência de usuário mais rica com a exibição de um modal.
* **Descrição:** Desenvolva uma galeria com várias imagens em miniatura.
    1.  Crie uma seção na página com pelo menos 4 imagens pequenas (`<img>`).
    2.  Ao clicar em qualquer uma das miniaturas, uma janela modal deve aparecer, sobrepondo o restante do conteúdo.
    3.  O modal deve conter a imagem que foi clicada, porém em um tamanho maior.
    4.  O modal também deve ter um botão "Fechar" (um "X" no canto, por exemplo) que, ao ser clicado, o esconde.
* **Conceitos-chave:** Manipulação da propriedade `display` ou de classes para mostrar/esconder elementos, capturar atributos (`getAttribute('src')`), estrutura HTML/CSS para overlays.
* **Desafio Bônus:** Permita que o usuário feche o modal também ao pressionar a tecla `Esc` no teclado ou ao clicar fora da área da imagem (no overlay).

---

## Nível 3: Aplicações Complexas e Gerenciamento de Estado

### Exercício 7: Jogo da Velha (Tic-Tac-Toe)

* **Objetivo:** Gerenciar o estado de um jogo, implementar lógica de vitória e reiniciar o estado da aplicação.
* **Descrição:** Crie um jogo da velha funcional. A interface deve ser um grid 3x3 (pode ser feito com `<div>`s ou `<button>`s).
    1.  Os jogadores, "X" e "O", devem se alternar a cada clique em uma célula vazia.
    2.  Após cada jogada, o script deve verificar se houve um vencedor (três marcas iguais em qualquer linha, coluna ou diagonal).
    3.  Se houver um vencedor, exiba uma mensagem (ex: "O jogador X venceu!") e impeça novas jogadas.
    4.  Se todas as células forem preenchidas sem um vencedor, declare um empate.
    5.  Adicione um botão "Reiniciar Jogo" que limpa o tabuleiro e reinicia a lógica.
* **Conceitos-chave:** Arrays para representar o estado do tabuleiro, lógica condicional complexa, gerenciamento de turnos, desabilitar elementos (`element.disabled`).
* **Desafio Bônus:** Mantenha um placar que mostre quantas vitórias cada jogador (X e O) acumulou durante a sessão.

---

### Exercício 8: Arrastar e Soltar (Drag and Drop)

* **Objetivo:** Implementar interações avançadas utilizando a API de Drag and Drop do HTML5.
* **Descrição:** Crie uma interface com duas colunas principais.
    1.  A primeira coluna, "A Fazer", deve conter uma lista de itens (ex: `<div>`s com texto). Esses itens devem ser "arrastáveis".
    2.  A segunda coluna, "Concluído", deve ser uma "área de soltura".
    3.  O usuário deve ser capaz de clicar e arrastar um item da coluna "A Fazer" e soltá-lo dentro da coluna "Concluído". Ao ser solto, o item deve se mover permanentemente para a nova coluna.
* **Conceitos-chave:** Atributo `draggable="true"`, eventos `dragstart`, `dragover`, `drop`, `event.preventDefault()`, `event.dataTransfer`.
* **Desafio Bônus:** Permita que os itens também possam ser arrastados de volta da coluna "Concluído" para a coluna "A Fazer". Adicione um feedback visual (ex: uma borda pontilhada) na coluna "Concluído" quando um item estiver sendo arrastado sobre ela.

---

### Exercício 9: Filtro Dinâmico de Produtos

* **Objetivo:** Simular uma aplicação de e-commerce, manipulando um conjunto de dados e atualizando a UI dinamicamente.
* **Descrição:** Crie uma página para exibir uma lista de produtos.
    1.  No seu arquivo JavaScript, crie um array de objetos. Cada objeto representará um produto e deve ter propriedades como `id`, `nome`, `preco` e `categoria` (ex: "Eletrônicos", "Roupas").
    2.  Renderize todos os produtos na página, criando os elementos HTML correspondentes.
    3.  Adicione os seguintes controles de filtro na página:
        * Um campo de busca (`<input type="text">`) para filtrar por nome.
        * Um seletor (`<select>`) ou checkboxes para filtrar por categoria.
        * Um `input` do tipo `range` para filtrar por preço máximo.
    4.  À medida que o usuário interage com **qualquer um** dos filtros, a lista de produtos exibida na tela deve ser atualizada em tempo real para mostrar apenas os itens que correspondem a **todos** os critérios selecionados.
* **Conceitos-chave:** Manipulação de arrays (`filter()`, `map()`, `forEach()`), combinação de múltiplos eventos (`input`, `change`), renderização dinâmica a partir de um array de dados.
* **Desafio Bônus:** Adicione botões para ordenar os produtos filtrados por preço (menor para maior e maior para menor).
