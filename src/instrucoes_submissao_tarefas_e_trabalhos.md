# Instruções para submissão de tarefas

Para submeter tarefas e exercícios durante a disciplina de DS122, utilizaremos
apenas o [gitlab.com](https://gitlab.com). Todas as tarefas serão disponibilizadas pelo
professor como repositórios Git nesse serviço. Por exemplo:

<https://gitlab.com/ds122-alexkutzke/ds122-prepare-assignment>

A submissão será um **fork** do repositório original da tarefa realizado pelo
aluno e armazenado no seu grupo criado (ver abaixo).

## Instruções gerais

Para mantermos uma organização das tarefas submetidas, siga os seguintes passos:

1. Acesse o GitLab e **crie um usuário para a disciplina**, com estas três
   características:
  * **E-mail**: o seu e-mail institucional da UFPR;
  * **Nome de usuário** (*username*): o seu GRR, em minúsculas. Por exemplo,
    `grr20249999`;
  * **Nome completo**: preenchido no perfil (**Edit profile**).

    Com o GRR no nome de usuário e o e-mail institucional, o professor
    identifica você sem ambiguidade. E, com uma conta separada, os trabalhos da
    disciplina não se misturam aos seus projetos e grupos pessoais.

    Se você já usa o GitLab, mantenha a sua conta pessoal como está e crie esta
    outra para a disciplina. O passo a passo do cadastro, com as telas que o
    GitLab apresenta pelo caminho, está em
    [Criação da conta no GitLab](./instrucoes_criacao_conta_gitlab.md);
2. Crie um novo grupo com as características abaixo. **A tela de boas-vindas do
   cadastro já obriga a criar um grupo**, então é provável que o seu já exista:
   nesse caso, confira o nome e a visibilidade em **Settings > General** em vez
   de criar outro:
  * Nome: **ds122-ano-semestre-turno-grr**;
    * Por exemplo, em 2026, segundo semestre, se você é do turno noturno e seu login é `grr20249999`,
      o nome do grupo criado deve ser: `ds122-2026-2-n-grr20249999`.
    * Use `n` para o turno da noite e `t` para o turno da tarde;
    * Utilize **letras minúsculas**;
  * Visibilidade do grupo: **Privado**;
3. Vá na tela de membros do grupo e adicione o usuário `alexkutzke` (o primeiro da lista) com o perfil `reporter` ao seu novo grupo;
4. Acesse o repositório da tarefa passado pelo professor;
5. Faça um fork do repositório e indique ao Gitlab para armazená-lo no grupo criado;
6. Antes do primeiro `git clone`, prepare a credencial conforme
   [Autenticação no GitLab](./instrucoes_autenticacao_git.md): um token de
   acesso pessoal nos computadores do laboratório, uma chave SSH no seu
   computador. A senha da conta não autentica operações de Git;
7. Realize a tarefa utilizando o repositório criado para alterar e salvar arquivos,
obedecendo prazo de entrega indicado pelo professor.

O grupo deve ser criado apenas uma vez por aluno e deve conter todos os
repositórios das tarefas realizadas durante a disciplina pelo aluno.

Com isso, o professor será capaz encontrar e avaliar com facilidade seus trabalhos.

> **Dois limites do plano gratuito do GitLab que valem conhecer.** Um grupo
> privado aceita até cinco membros; o seu terá dois, você e o professor, então
> não há problema. Contas criadas a partir de 27/01/2026 podem manter no máximo
> três grupos de nível superior, então evite criar um grupo novo para cada
> disciplina sem necessidade.

Comandos para configuração de nome e email para o repositório (faça isso para cada repositório clonado
**se estiver utilizando os computadores do SEPT**):

```bash
# Na pasta do repositório clonado, digite:
$ git config user.name "Your name"
$ git config user.email "email@example.com"
```

Caso esteja utilizando **seu próprio computador**, execute os seguintes comandos apenas uma vez:

```bash
$ git config --global user.name "Seu nome"
$ git config --global user.email seuemail@example.com
```

## Trabalhos e tarefas em grupo

Quando devidamente indicado pelo professor, alguns trabalhos e tarefas poderão
ser realizados em grupo. Com isso, apenas um fork deverá ser criado por um membro
da equipe. Esse membro será o responsável por adicionar seus colegas como membros
do tipo `developer` ou `master` ao repositório criado (não ao grupo!).

