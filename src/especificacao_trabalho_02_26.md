Tecnologia em Análise e Desenvolvimento de Sistemas

Setor de Educação Profissional e Tecnológica - SEPT

Universidade Federal do Paraná - UFPR

---

*DS122 - Desenvolvimento de Aplicações Web 1*

Prof. Alexander Robert Kutzke

# Trabalho Prático Incremental, DS122 (2026/2)

## Enunciado Geral

Desenvolver um **Sistema Web de Catálogo de Produtos** ao longo do semestre, construído de forma incremental em três entregas. O sistema será um site completo com área pública (catálogo, busca, contato) e área administrativa (gerenciamento de produtos com autenticação).

O trabalho é **individual** e deve ser versionado via **GitLab** com diário de bordo documentando o processo de desenvolvimento.

O ramo de produtos do catálogo é de escolha do aluno, com preferência por temas de viés sustentável (pequenos produtores, negócios locais, produtos de baixo impacto), em alinhamento com os ODS 8 e 9 previstos no plano de ensino.

---

## Estrutura de Entregas

### Entrega 1: Front-end Estático (HTML5 + CSS3)
**Prazo:** 11/09/2026, na aula anterior à Prova 1  
**Peso na nota do trabalho:** 30%

**Requisitos mínimos:**

1. **Página Inicial (`index.html`)**
   - Cabeçalho com nome do sistema e menu de navegação (links para Catálogo, Sobre e Contato).
   - Seção de destaque com 3 produtos em evidência (cards estáticos).
   - Rodapé com informações do aluno.

2. **Página de Catálogo (`catalogo.html`)**
   - Listagem de ao menos 8 produtos em formato de grade (*grid*).
   - Cada produto deve exibir: imagem (placeholder), nome, descrição curta, preço.
   - Estrutura semântica com tags HTML5 apropriadas (`<article>`, `<section>`, `<figure>`, etc.).

3. **Página Sobre (`sobre.html`)**
   - Descrição da empresa fictícia e informações do desenvolvedor (aluno).

4. **Página de Contato (`contato.html`)**
   - Formulário com campos: nome, e-mail, assunto (select), mensagem.
   - Validações nativas HTML5 (`required`, `type="email"`, `minlength`, etc.).

5. **Estilização (CSS3)**
   - Layout responsivo utilizando **Flexbox** e/ou **CSS Grid**.
   - Uso de *Media Queries* para ao menos 2 breakpoints (desktop e mobile).
   - Paleta de cores, tipografia e espaçamento consistentes.
   - Arquivo CSS externo e organizado.

**Diário de bordo (Entrega 1):**
- Repositório GitLab com ao menos 3 commits com mensagens descritivas.
- Registro no `README.md` do que foi feito em cada etapa e das dificuldades encontradas.

---

### Entrega 2: Interatividade com JavaScript
**Prazo:** 09/10/2026, na aula anterior à Prova 2  
**Peso na nota do trabalho:** 30%

**Requisitos mínimos:**

1. **Filtro dinâmico no Catálogo**
   - Barra de busca textual que filtra produtos por nome em tempo real (evento `input`).
   - Filtro por faixa de preço (campos min/max).
   - Ambos os filtros combináveis.

2. **Destaque condicional**
   - Produtos com desconto acima de determinado valor recebem classe CSS de destaque via JS (ex: borda dourada para produtos com desconto).

3. **Validação do formulário de contato com JS**
   - Validação adicional em JavaScript (além da nativa HTML5): impedir envio se campos estiverem vazios, exibir mensagens de erro customizadas.
   - Feedback visual de sucesso ao enviar (ex: mensagem "Mensagem enviada com sucesso!" sem recarregar a página).

4. **Consumo de dados via Fetch**
   - Os produtos do catálogo devem ser carregados a partir de um arquivo `produtos.json` utilizando `fetch()`.
   - A renderização dos cards de produto deve ser feita dinamicamente via manipulação do DOM.

5. **Persistência com `localStorage` (opcional, bônus)**
   - Permitir que o usuário "favorite" produtos e persista a lista de favoritos entre recarregamentos.

**Mudanças estruturais:**
- A página de catálogo deixa de ter produtos *hardcoded* no HTML: os produtos são renderizados via JS a partir do JSON.

**Diário de bordo (Entrega 2):**
- Ao menos 3 novos commits com mensagens descritivas.
- Registro no `README.md` atualizado.

---

### Entrega 3: Back-end com PHP + MySQL
**Prazo:** 27/11/2026, na aula anterior à Prova 3  
**Peso na nota do trabalho:** 40%

**Requisitos mínimos:**

1. **Banco de Dados MySQL**
   - Tabela `produtos`: id, nome, descricao, preco, imagem, categoria, data_cadastro.
   - Tabela `usuarios`: id, nome, email, senha (hash), data_cadastro.
   - Tabela `mensagens`: id, nome, email, assunto, mensagem, data_envio.
   - Arquivo `.sql` com a estrutura no repositório.

2. **Páginas dinâmicas (PHP)**
   - Página de catálogo dinâmica: produtos carregados do banco de dados.
   - Sistema de busca e filtro via PHP (parâmetros GET) combinado com consultas SQL parametrizadas.
   - Página de detalhes do produto (`produto.php?id=X`) gerada dinamicamente.

3. **Área Administrativa com autenticação**
   - Página de **login** (`admin/login.php`): formulário de e-mail e senha com verificação via PHP + sessão.
   - Após autenticação, área **protegida** com:
     - Listagem de produtos em tabela com ações (editar, excluir).
     - Formulário de cadastro/edição de produto.
     - Exclusão de produto com confirmação.
   - Página de **logout** que destrói a sessão.

4. **CRUD completo para Produtos**
   - **Create:** formulário de cadastro com validação no servidor.
   - **Read:** listagem paginada (ou ao menos com limite de itens por página).
   - **Update:** formulário de edição pré-preenchido com dados atuais.
   - **Delete:** exclusão com confirmação e tratamento de erros.

5. **Armazenamento de mensagens de contato**
   - O formulário de contato da Entrega 1 agora insere os dados na tabela `mensagens`.
   - Área administrativa exibe as mensagens recebidas.

6. **Segurança básica**
   - Uso de *prepared statements* em todas as consultas.
   - Senhas com hash (`password_hash`/`password_verify`).
   - Validação e sanitização de dados de entrada.
   - Proteção básica das páginas administrativas (verificação de sessão).

**Diário de bordo (Entrega 3):**
- Ao menos 5 novos commits com mensagens descritivas.
- Registro no `README.md` atualizado.

---

## Diário de Bordo (especificação detalhada)

O diário de bordo é parte obrigatória do trabalho e será mantido no arquivo `README.md` do repositório GitLab. Deve conter:

1. **Descrição do sistema:** o que a aplicação faz e como está organizada em arquivos e pastas.

2. **Registro do processo:** para cada entrega, um parágrafo sobre o que foi implementado, as dificuldades encontradas e como foram resolvidas.

3. **Histórico de commits:** cada *commit* com mensagem clara descrevendo o que foi implementado ou modificado. O histórico deve mostrar o trabalho distribuído ao longo das semanas, e não um único envio na véspera do prazo.

A ausência do diário de bordo implica desconto na nota do trabalho.

---

## Uso de IA Generativa

**O uso de ferramentas de IA generativa (ChatGPT, GitHub Copilot, Gemini, Claude e similares) para a produção de código deste trabalho não é permitido**, conforme as Formas de Avaliação do plano de ensino da disciplina.

A restrição vale para todas as atividades avaliativas: as três entregas do trabalho, os exercícios em sala e as provas.

O motivo está no plano de ensino: esta é uma disciplina do segundo período, e o esforço de escrever o próprio código é o que constrói as competências de raciocínio lógico, depuração e leitura de sintaxe. Ferramenta que remove esse esforço na fase inicial compromete a formação dessas competências. A restrição vale para esta disciplina e para este momento do curso.

Consequências:

- Código que o aluno não souber explicar é tratado como não autoral, com as mesmas consequências do plágio: nota zero na entrega correspondente.
- Cada prova contém uma questão integradora sobre o seu próprio projeto, em que o aluno modifica, estende ou explica o código entregue. É nessa questão que a autoria do trabalho é verificada.

O que continua permitido, e recomendado:

- Documentação oficial: MDN, php.net, dev.mysql.com.
- Material da disciplina, livros da bibliografia e busca na internet.
- Dúvidas com o professor, em aula ou no fórum da UFPR Virtual.
- Uso de IA para **estudar**, fora das atividades avaliativas: pedir explicação de um conceito, interpretar uma mensagem de erro, gerar exemplos genéricos de sintaxe. Nunca para produzir o código que será entregue.

---

## Questões Integradoras nas Provas (Exemplos)

### Na Prova 1 (HTTP + HTML5 + CSS3)

**Contexto:** O aluno tem acesso ao seu próprio repositório (código da Entrega 1).

**Questão Integradora (5-6 pontos dos 25 da prova):**

> **Parte A (3,0):** Com base no seu projeto, crie uma nova página `produto-destaque.html` que exiba um único produto com layout de "página de detalhes". A página deve conter: imagem grande do produto, nome, descrição completa, preço em destaque e um botão "Tenho interesse" que leva ao formulário de contato. Utilize **HTML5 semântico** e mantenha a **identidade visual** (CSS) do seu projeto.
>
> **Parte B (2,0):** Adicione ao seu arquivo CSS uma regra de *media query* para dispositivos com largura máxima de 480px que reorganize a página de detalhes em coluna única.
>
> **Parte C (1,0):** Identifique no seu código um ponto onde a semântica HTML5 poderia ser melhorada e reescreva o trecho.

---

### Na Prova 2 (JavaScript)

**Contexto:** O aluno tem acesso ao repositório com as Entregas 1 e 2.

**Questão Integradora (5-6 pontos dos 25 da prova):**

> **Parte A (3,0):** Implemente no seu catálogo um sistema de **ordenação de produtos**. Adicione um `<select>` com as opções: "Ordenar por preço (do menor para o maior)", "Ordenar por preço (do maior para o menor)", "Ordenar por nome (A-Z)". A ordenação deve ocorrer dinamicamente via JavaScript, manipulando o DOM, sem recarregar a página.
>
> **Parte B (2,0):** Utilizando `localStorage`, implemente um contador de visualizações: cada vez que o usuário clica em um produto do catálogo para ver detalhes, incremente um contador para aquele produto. Exiba o contador ao lado do nome do produto no catálogo.
>
> **Parte C (1,0):** Explique, em comentários no código, o funcionamento do `fetch()` utilizado no seu projeto: o que a função retorna, como os dados são processados e o que acontece em caso de erro.

---

### Na Prova 3 (PHP + MySQL)

**Contexto:** O aluno tem acesso ao repositório completo (todas as entregas).

**Questão Integradora (5-6 pontos dos 25 da prova):**

> **Parte A (3,0):** Adicione ao seu sistema uma nova funcionalidade: **cadastro de categorias de produtos**. Crie:
> - A tabela `categorias` (id, nome, descricao) no MySQL.
> - O formulário de cadastro de categoria na área administrativa.
> - A listagem de categorias.
> - Altere o formulário de cadastro/edição de produto para ter um `<select>` de categoria populado dinamicamente do banco.
>
> **Parte B (2,0):** Implemente um **filtro por categoria** na página de catálogo pública. O filtro deve ser um `<select>` que, ao ser submetido via GET, recarrega a página exibindo apenas produtos da categoria selecionada. Use *prepared statements*.
>
> **Parte C (1,0):** Identifique no seu código duas medidas de segurança implementadas e explique (em comentários) qual vulnerabilidade cada uma previne.

---

## Critérios de Avaliação do Trabalho

| Critério | Peso |
|---|---|
| Funcionalidade: todos os requisitos implementados e funcionando | 35% |
| Qualidade do código: organização, indentação, nomenclatura, boas práticas | 20% |
| Design e usabilidade: layout responsivo, consistência visual, experiência do usuário | 15% |
| Segurança (Entrega 3): prepared statements, hash de senhas, validação server-side | 10% |
| Diário de bordo: commits significativos e distribuídos no tempo, descrição do processo | 20% |

---

## Cronograma de Entregas

| Entrega | Conteúdo | Prazo | Peso |
|---|---|---|---|
| Entrega 1 | HTML5 + CSS3 (Front-end estático) | 11/09/2026 (sex) | 30% |
| Entrega 2 | JavaScript (Interatividade) | 09/10/2026 (sex) | 30% |
| Entrega 3 | PHP + MySQL (Back-end completo) | 27/11/2026 (sex) | 40% |

> **Observação sobre prazos:** Toda entrega vence na **aula anterior à prova correspondente**, às 23h59, por `push` no **GitLab**. A semana entre a entrega e a prova é o que permite ao professor preparar a questão integradora, que pede para modificar, estender ou explicar o seu próprio código. O diário de bordo, no `README.md`, deve estar atualizado no repositório. Entregas com atraso terão desconto de 20% por dia útil.
