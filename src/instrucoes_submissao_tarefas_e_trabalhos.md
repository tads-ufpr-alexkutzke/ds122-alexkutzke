# Instruções para submissão de tarefas

Para submeter tarefas e exercícios durante a disciplina de DS122, utilizaremos
apenas o gitlab.com [1]. Todas as tarefas serão disponibilizadas pelo
professor como repositórios Git nesse serviço. Por exemplo:

https://gitlab.com/ds122-alexkutzke/ds122-prepare-assignment

A submissão será um **fork** do repositório original da tarefa realizado pelo
aluno e armazenado no seu grupo criado (ver abaixo).

## Instruções gerais

Para mantermos uma organização das tarefas submetidas, siga os seguintes passos:

1. Acesse o Gitlab (se for seu primeiro acesso, crie um novo usuário, e lembre de colocar seu nome completo para que o professor possa saber que é você);
2. Crie um novo grupo com as seguintes características:
  * Nome: ds122-2023-2-turno-seu_login;
    * Por exemplo, se você é do turno noturno e seu login é `grr20189999`,
      o nome do grupo criado deve ser: `ds122-2023-2-N-grr20189999`.
  * Visibilidade do grupo: Privado;
3. Vá na tela de membros do grupo e adicione o usuário `alexkutzke` com o perfil `reporter` ao seu novo grupo;
4. Acesse o repositório da tarefa passado pelo professor;
5. Faça um fork do repositório e indique ao Gitlab para armazená-lo no grupo criado;
6. Realize a tarefa utilizando o repositório criado para alterar e salvar arquivos,
obedecendo prazo de entrega indicado pelo professor.

O grupo deve ser criado apenas uma vez por aluno e deve conter todos os
repositórios das tarefas realizadas durante a disciplina pelo aluno.

Com isso, o professor será capaz encontrar e avaliar com facilidade seus trabalhos.

Comandos para configuração de nome e email para o repositório (faça isso para cada repositório clonado
se estiver utilizando os computadores do SEPT):

```bash
# Na pasta do repositório clonado, digite:
$ git config user.name "Your name"
$ git config user.email "email@example.com"
```

Caso esteja utilizando seu próprio computador, execute os seguintes comandos apenas uma vez:

```bash
$ git config --global user.name "Seu nome"
$ git config --global user.email seuemail@example.com
```

## Trabalhos e tarefas em grupo

Quando devidamente indicado pelo professor, alguns trabalhos e tarefas poderão
ser realizados em grupo. Com isso, apenas um fork deverá ser criado por um membro
da equipe. Esse membro será o responsável por adicionar seus colegas como membros
do tipo `developer` ou `master` ao repositório criado (não ao grupo!).

[1]: https://gitlab.com
