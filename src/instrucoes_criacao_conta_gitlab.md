# Criação da conta no GitLab

Toda entrega da disciplina passa pelo GitLab. A conta é criada uma vez e serve o
semestre inteiro.

**Crie a conta antes da aula.** A confirmação por e-mail às vezes demora, e o
cadastro feito em sala consome o tempo que seria usado para o conteúdo.

Duas exigências da disciplina:

* **e-mail da UFPR** no cadastro;
* **nome de usuário igual ao seu GRR**, em minúsculas.

Com isso o professor localiza cada aluno sem ambiguidade, e a conta da
disciplina fica separada dos seus projetos pessoais.

> O GitLab muda essas telas com alguma frequência. Se o que aparecer no seu
> navegador não bater exatamente com o descrito aqui, siga os dois princípios:
> conta individual e plano gratuito. Nada na disciplina exige plano pago.

## Antes de começar

* o seu GRR;
* acesso ao seu e-mail da UFPR, para confirmar o cadastro;
* uma senha de pelo menos 8 caracteres.

Se você já usa o GitLab com uma conta pessoal, **mantenha essa conta como está e
crie outra** para a disciplina.

## Passo 1: o formulário de cadastro

Acesse <https://gitlab.com/users/sign_up> e preencha:

| Campo do formulário | O que preencher |
|---|---|
| **First name** | seu primeiro nome |
| **Last name** | seu sobrenome, como no registro acadêmico |
| **Username** | o seu GRR, todo em minúsculas: `grr20249999` |
| **Company email** | o seu e-mail da UFPR |
| **Password** | mínimo de 8 caracteres |
| Caixa de aviso sobre produtos e eventos | pode deixar desmarcada, é propaganda |

Sobre esse formulário:

* O rótulo do e-mail diz **Company email** e recomenda um endereço de trabalho.
  O e-mail da UFPR atende. Não use Gmail ou Hotmail pessoal.
* O **Username** entra no endereço de todos os seus repositórios e é o que o
  professor procura na hora de corrigir. Digite com atenção.
* Não use os botões **Continue with Google** ou **Continue with GitHub**: eles
  vinculam a conta ao seu perfil pessoal, que é justamente o que queremos
  evitar.

Se o GitLab avisar que o nome de usuário já está em uso, acrescente `-ufpr` ao
final (`grr20249999-ufpr`) e informe ao professor qual ficou.

## Passo 2: confirmação do e-mail

O GitLab envia uma mensagem de confirmação. Procure na caixa de entrada e
também no spam do webmail da UFPR. Se nada chegar em cerca de dez minutos, peça
o reenvio na tela de login.

Pode aparecer uma verificação por telefone, com envio de código por SMS. É o
sistema antifraude do GitLab, e informar o número resolve.

> Se alguma tela exigir **cartão de crédito** para continuar, pare e avise o
> professor. O cartão só é pedido para usar os servidores de integração
> contínua do GitLab, recurso que a disciplina não utiliza.

## Passo 3: as perguntas de boas-vindas

Depois da confirmação, o GitLab faz algumas perguntas antes de liberar a tela
principal. As respostas que interessam:

| Pergunta | Resposta |
|---|---|
| **Role** | `Student` |
| **Who will be using GitLab?** | **Just me** |
| **What would you like to do?** | `Create a new project`, ou a opção equivalente |

A resposta **Just me** é a que importa: ela evita a tela seguinte, que pede nome
e dados de empresa.

## Passo 4: se o GitLab insistir em criar um grupo

Em algumas versões do cadastro não há como pular essa etapa. Nesse caso, use
desde já o grupo da disciplina, que você precisaria criar de qualquer forma:

* **Group name**: `ds122-2026-2-turno-grr`, tudo em minúsculas, com `n` ou `t`
  no lugar de `turno` e o seu GRR no lugar de `grr`. Exemplo:
  `ds122-2026-2-n-grr20249999`;
* **Visibility level**: **Private**.

Se a tela pedir **Organization name** ou **Company name**, escreva `UFPR`. Se
pedir número de funcionários, setor de atuação ou algo do tipo, responda
qualquer coisa: esses campos não afetam a sua conta.

Terminado o cadastro, volte a
[Entrega de exercícios e trabalhos no GitLab](./instrucoes_submissao_tarefas_e_trabalhos.md)
e adicione o usuário `alexkutzke` ao grupo com o papel `Reporter`.

## Passo 5: o plano

O GitLab oferece o teste do plano **Ultimate** por 30 dias em vários pontos do
cadastro. Procure a opção do plano **Free**, com texto parecido com
`Continue with Free` ou `Skip trial`.

Cair no teste por engano não gera cobrança: o GitLab não pede cartão de crédito
para iniciá-lo, e ao fim dos 30 dias a conta volta sozinha ao plano gratuito.
Tudo o que a disciplina usa está no plano gratuito.

## Passo 6: nome completo no perfil

No menu do seu avatar, escolha **Edit profile** e preencha o campo **Full name**
com o seu nome completo. O professor usa esse campo para conferir a autoria das
entregas.

## Conferência final

Antes de fechar o navegador, confirme:

- [ ] o nome de usuário é o seu GRR, em minúsculas;
- [ ] o e-mail cadastrado é o da UFPR, e já foi confirmado;
- [ ] o nome completo está preenchido no perfil;
- [ ] o grupo `ds122-2026-2-turno-grr` existe, é **privado** e tem o
      `alexkutzke` como `Reporter`.

## Problemas comuns

**Digitei o nome de usuário errado.** Dá para trocar em **Settings > Account >
Change username**. Faça isso antes de criar qualquer *fork*, porque a troca
altera o endereço de todos os repositórios.

**Já tenho conta pessoal e o navegador entra sempre nela.** Use uma janela
anônima para a conta da disciplina, ou saia da conta pessoal antes de entrar na
outra.

**O e-mail de confirmação não chega.** Confira o spam do webmail da UFPR e peça
o reenvio na tela de login. Persistindo, avise o professor pela UFPR Virtual.
