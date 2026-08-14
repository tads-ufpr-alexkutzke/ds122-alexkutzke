# Autenticação no GitLab: token e chave SSH

A senha da sua conta não autentica `git push` nem `git pull`. Desde abril de
2026 o GitLab exige um segundo fator em todo login feito com senha, e os
comandos do Git não param para pedir código nenhum. No lugar da senha existem
duas credenciais:

* **token de acesso pessoal** (*personal access token*), usado com o endereço
  `https://` do repositório;
* **chave SSH**, usada com o endereço `git@gitlab.com:` do repositório.

Qual das duas usar depende da máquina em que você está:

| Onde você está | Credencial | Por quê |
|---|---|---|
| Computador do laboratório | token válido por um dia | a máquina é de todos, e a credencial não pode sobreviver à sua aula |
| Seu computador | chave SSH | configurada uma vez, serve o semestre inteiro |

O `fork`, o `clone`, o `add` e o `commit` não mudam. O que muda é o momento em
que o Git precisa provar ao servidor que você é você, o que acontece no `push`
e, em repositório privado, também no `clone` e no `pull`.

## Caminho 1: token de acesso pessoal, para o laboratório

### O problema específico das máquinas do SEPT

Toda a turma entra no Windows com a mesma conta, `Laboratório`. O que o Windows
guarda por usuário fica, portanto, compartilhado entre todos os alunos que
usarem aquela máquina. Isso inclui o **Gerenciador de Credenciais**, que é onde
o Git for Windows salva senhas e tokens por padrão.

Um token salvo ali continua disponível para quem sentar no computador depois de
você, com permissão de escrita nos seus repositórios. As duas medidas abaixo
existem por causa disso: o token vale um dia só, e o Git é instruído a não
guardar nada em disco.

### Passo 1: criar o token

No navegador, em uma **janela anônima**:

1. entre em <https://gitlab.com> com a conta da disciplina. O GitLab envia um
   código de verificação para o seu e-mail da UFPR e pede esse código na tela.
   É a autenticação em dois fatores, e ela aparece a cada login com senha;
2. no seu avatar, escolha **Edit profile**, e na barra lateral **Access >
   Personal access tokens**;
3. em **Generate token**, se aparecer uma lista de tipos, escolha **Legacy
   token**;
4. preencha:

| Campo | O que preencher |
|---|---|
| **Token name** | `laboratorio-DD-MM`, com a data da aula |
| **Expiration date** | o **dia seguinte** ao da aula |
| **Scopes** | apenas `write_repository` |

5. clique em **Generate token**.

O token aparece na tela **uma única vez**. Ao sair da página ou recarregá-la,
não há como vê-lo de novo. Copie-o agora.

O escopo `write_repository` permite ler e escrever repositórios, e nada mais.
Não marque `api`: esse escopo daria ao token o poder de alterar a sua conta
inteira.

### Passo 2: impedir que o Git salve a credencial

Antes de clonar qualquer coisa, no Git Bash:

```bash
git config --global credential.helper ""
```

O valor vazio descarta o gerenciador de credenciais que o instalador do Git for
Windows configura na máquina. A partir daí o Git pergunta usuário e senha a cada
`push`, e não escreve credencial nenhuma em disco.

A conferência é observar o comportamento: se o segundo `push` da aula pedir a
senha de novo, o descarte funcionou. Se ele passar direto, alguma credencial
ficou guardada, e o final desta seção explica como apagá-la.

### Passo 3: usar o token

O `push` pergunta duas coisas:

```
Username for 'https://gitlab.com': grr20249999
Password for 'https://grr20249999@gitlab.com':
```

O usuário é o seu GRR. A senha é o **token**, colado. O terminal não mostra
nada enquanto você cola, e isso é normal: o campo de senha não exibe o que
recebe. No Git Bash, colar é `Shift+Insert` ou o botão direito do mouse.

Guarde o token durante a aula em algum lugar seu, como o bloco de notas do
celular. Um arquivo de texto na área de trabalho da máquina do laboratório é
lido pelo próximo aluno.

### Passo 4: antes de sair da máquina

- [ ] revogue o token, em **Edit profile > Access > Personal access tokens**,
      botão **Revoke**;
- [ ] apague a pasta do repositório clonado;
- [ ] saia da conta do GitLab e feche a janela anônima do navegador.

Se o Gerenciador de Credenciais tiver guardado alguma coisa, apague pela
interface do Windows: tecla Windows, digite `Gerenciador de Credenciais`,
escolha **Credenciais do Windows** e remova as entradas que começam com
`git:https://gitlab.com`.

## Caminho 2: chave SSH, para o seu computador

Uma chave SSH é um par de arquivos. A **chave privada** fica na sua máquina e
não sai de lá. A **chave pública** é cadastrada no GitLab. Feito isso, o `push`
não pede mais nada.

Não gere chave SSH no laboratório. A chave privada ficaria no perfil
compartilhado, e quem a copiar passa a poder escrever nos seus repositórios sem
precisar de senha nenhuma.

### Passo 1: gerar o par de chaves

```bash
ssh-keygen -t ed25519 -C "grr20249999@ufpr.br"
```

O programa pergunta onde salvar e qual *passphrase* usar. Aceite o caminho
sugerido com Enter. A *passphrase* pode ficar em branco no seu computador
pessoal, e nesse caso a proteção da chave passa a ser só o acesso à sua conta na
máquina.

Os dois arquivos ficam em `~/.ssh/`, que no Windows é
`C:\Users\SeuUsuario\.ssh`:

| Arquivo | O que é | Para onde vai |
|---|---|---|
| `id_ed25519.pub` | chave pública | cadastrada no GitLab |
| `id_ed25519` | chave privada | fica onde está, sempre |

### Passo 2: cadastrar a chave pública

Mostre o conteúdo do arquivo `.pub` e copie a linha inteira:

```bash
cat ~/.ssh/id_ed25519.pub
```

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH3q6Vr9tJ1nQ5pC0y8v7wZk4XfLm2BdG grr20249999@ufpr.br
```

No GitLab, no seu avatar, **Edit profile**, e na barra lateral **Access > SSH
keys > Add new key**. Cole em **Key**, dê um título que identifique a máquina,
como `notebook pessoal`, e confirme em **Add key**.

O arquivo sem `.pub` não vai para essa tela, não entra em repositório e não se
manda para ninguém.

### Passo 3: testar

```bash
ssh -T git@gitlab.com
```

```
Welcome to GitLab, @grr20249999!
```

Na primeira conexão o SSH mostra a impressão digital do servidor e pergunta se
você confia nele. Responda `yes`.

### Passo 4: usar o endereço SSH

O endereço SSH é o que aparece no botão **Code**, em **Clone with SSH**:

```bash
git clone git@gitlab.com:ds122-2026-2-n-grr20249999/nome-da-tarefa.git
```

Se o repositório já foi clonado por HTTPS, troque o endereço do remoto em vez de
clonar de novo:

```bash
git remote set-url origin git@gitlab.com:ds122-2026-2-n-grr20249999/nome-da-tarefa.git
git remote -v
```

## Problemas comuns

**`HTTP Basic: Access denied`.** Você digitou a senha da conta no lugar do
token. Repita o `push` e cole o token.

**O Git não pergunta nada e o `push` falha com erro de permissão.** Uma
credencial antiga, de outro aluno ou de outra conta sua, está guardada na
máquina. Apague as entradas `git:https://gitlab.com` no Gerenciador de
Credenciais do Windows e repita.

**`Permission denied (publickey)`.** A chave pública não foi cadastrada no
GitLab, ou você está em outra máquina, com outra chave. Rode `ssh -T
git@gitlab.com` para confirmar.

**Perdi o token.** Não há como recuperá-lo. Revogue o antigo e crie outro.

**O código de verificação por e-mail não chega.** Confira o spam do webmail da
UFPR. O código vale por alguns minutos; se demorar, peça um novo na própria tela
de login.
