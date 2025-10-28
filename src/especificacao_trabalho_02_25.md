Tecnologia em Análise e Desenvolvimento de Sistemas

Setor de Educação Profissional e Tecnológica - SEPT

Universidade Federal do Paraná - UFPR

---

*DS122 - Desenvolvimento de Aplicações Web 1*

Prof. Alexander Robert Kutzke

# Especificação de Trabalho Prático 02/2025

O trabalho prático envolve a criação de uma aplicação WEB completa. Ou seja,
que inclua a implementação de front-end, back-end e que possua integração com 
um banco de dados.

## Tema

A aplicação deve implementar um jogo de digitação utilizando Javascript e utilizar PHP para armazenar e exibir quadros de pontuação.

O funcionamento é o seguinte:

1. O usuário deve se registrar e se autenticar para acessar o sistema;
2. Uma vez autenticado, o usuário pode jogar partidas de um jogo de digitação;
3. A cada partida, o usuário acumula pontos, exibidos pelo sistema.
4. O usuário pode acessar seu histórico de partidas (e pontuação), bem como diferentes quadros de pontuação (pelo menos geral e ligas)

O jogo de digitação a ser implementado é livre, desde que envolva o princípio básico de digitação correta de palavras. Os jogos [typing.com](https://www.typing.com/student/lesson/333/skill-builder) e [ztype](https://zty.pe/) são bons exemplos desse princípio.

O sistema deve disponibilizar a inscrição do usuário em ligas. Ligas são um conjunto de usuários que competem entre si.
O usuário pode criar e se cadastrar em ligas. Para o cadastro do usuário em uma liga é necessário uma palavra-chave, definida pelo criador da liga.

A pontuação da liga deve ser exibida de duas formas: 

1) pontuação desde a criação da liga; e 
2) pontuação semanal.

Além da pontuação em suas respectivas ligas, o usuário também pode verificar sua pontuação geral, envolvendo todos os jogadores. Esse quadro também deve apresentar a pontuação desde a criação do sistema e pontuação semanal.

A qualquer momento, o usuário pode acessar um relatório com os dados de todas as partidas jogadas, com suas respectivas pontuações.

## Requisitos

A aplicação desenvolvida deve atender os seguintes requisitos:

* **Front-end**:
  * Uso de HTML5, CSS3 e JS;
  * Interface amigável; ;
  * Validação de campos de formulário;
  * Implementação do Jogo de digitação completamente em JS;

* **Back-end**;
  * Integração com um banco de dados;
  * Sistema de autenticação/autorização de usuário(s) salvo(s) em banco de dados;
  * Validação de campos de formulário e outras informações recebidas.

## Ambiente de Desenvolvimento

* O sistema deve ser desenvolvido utilizando **apenas** os recursos demonstrados
na disciplina DS122 (PHP, Javascript (JQuery), HTML5, CSS3 e algum banco de dados);
  * É permitido o uso de frameworks *front-end*, como Bootstrap e W3.CSS;
  * **Não** é permitido o uso de frameworks *back-end*.

## Entrega

**Datas de entrega e defesa na UFPR Virtual**.

O trabalho pode ser feito em **grupos de 2 até 4 alunos**.

O código deve ser entregue através da UFPR Virtual, por meio de link para repositório git.

O trabalho deverá ser defendido através de uma demonstração do seu funcionamento e explicação do código.
A defesa é realizada apenas para o professor, não para a turma.

## Documentação

O repositório deverá conter um arquivo chamado `README.md` com a descrição
do sistema e de seu funcionamento. Deve-se utilizar a sintaxe correta da
linguagem **Markdown** nesse documento (para saber mais, consulte: <https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet>).

## Critério para avaliação

Os critérios para avaliação serão os seguintes:

 * **Defesa e conhecimento do código** [50 pontos]:

 * **Funcionalidades e implementação** [50 pontos]:
    * Qualidade da interface do usuário [10 ponto];
    * Funcionamento do Jogo de digitação [20 ponto];
    * Funcionamento da aplicação back-end [20 pontos];

**Atenção**: em nenhuma hipótese serão aceitos trabalhos com qualquer traço de plágio. A identificação de plágio implica em nota zero a todos os integrantes do grupo.

## Uso de IA Generativa

O uso de IA Generativa é permitido segundo algumas regras, descritas abaixo, e perante entrega de relatório das interações realizadas.

O repositório com o código do trabalho deverá conter um arquivo chamado `AI_USAGE_LOG.md` com o seguinte conteúdo:

```markdown
# Relatório de Uso de Inteligência Artificial Generativa

Este documento registra todas as interações significativas com ferramentas de IA generativa (como Gemini, ChatGPT, Copilot, etc.) durante o desenvolvimento deste projeto. O objetivo é promover o uso ético e transparente da IA como ferramenta de apoio, e não como substituta para a compreensão dos conceitos fundamentais.

## Política de Uso
O uso de IA foi permitido para as seguintes finalidades:
- Geração de ideias e brainstorming de algoritmos.
- Explicação de conceitos complexos.
- Geração de código boilerplate (ex: estrutura de classes, leitura de arquivos).
- Sugestões de refatoração e otimização de código.
- Debugging e identificação de causas de erros.
- Geração de casos de teste.

É proibido submeter código gerado por IA sem compreendê-lo completamente e sem adaptá-lo ao projeto. Todo trecho de código influenciado pela IA deve ser referenciado neste log.

---

## Registro de Interações

*Copie e preencha o template abaixo para cada interação relevante.*

### Interação 1

- **Data:** 20/10/2025
- **Etapa do Projeto:** 1 - Compressão de Arquivos
- **Ferramenta de IA Utilizada:** Gemini Advanced
- **Objetivo da Consulta:** Eu estava com dificuldades para entender como gerenciar o dicionário do algoritmo LZW quando ele atinge o tamanho máximo. Precisava de uma estratégia para lidar com isso.

- **Prompt(s) Utilizado(s):**
  1. "No algoritmo de compressão LZW, o que acontece quando o dicionário atinge o tamanho máximo? Quais são as estratégias mais comuns para lidar com isso?"
  2. "Pode me dar um exemplo em Python de como implementar a estratégia de 'resetar o dicionário' no LZW?"

- **Resumo da Resposta da IA:**
  A IA explicou três estratégias: 1) parar de adicionar novas entradas, 2) resetar o dicionário para o estado inicial, e 3) usar uma política de descarte, como LRU (Least Recently Used), que é mais complexa. A IA forneceu um pseudocódigo para a estratégia de reset, que parecia a mais simples e eficaz para este projeto.

- **Análise e Aplicação:**
  A resposta da IA foi extremamente útil para clarear as opções. Optei por implementar a estratégia de resetar o dicionário. O código fornecido pela IA não foi usado diretamente, pois estava muito simplificado e não se encaixava na minha arquitetura de classes. No entanto, a lógica de verificar o tamanho do dicionário e invocar uma função `reset_dictionary()` foi a base para a minha implementação. Isso me poupou tempo de pesquisa em artigos e livros.

- **Referência no Código:**
  A lógica inspirada por esta interação foi implementada no arquivo `compressor/lzw.py`, especificamente na função `compress()`, por volta da linha 85.

---

### Interação 2

- **Data:** ...
- **Etapa do Projeto:** ...
- **Ferramenta de IA Utilizada:** ...
- **Objetivo da Consulta:** ...
- **Prompt(s) Utilizado(s):** ...
- **Resumo da Resposta da IA:** ...
- **Análise e Aplicação:** ...
- **Referência no Código:** ...

---
```
