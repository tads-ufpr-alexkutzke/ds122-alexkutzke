# Git e GitLab

[Slides desta aula (PDF)](slides/aula_02_git.pdf)

![Ideia Git](./images/git/git.gif)

## Bibliografia recomendada para o tema

* CHACON, S.; STRAUB, B. **Pro Git**, 2ª ed. Capítulos 1, 2 e 5.
  [Disponível em português, gratuitamente](https://git-scm.com/book/pt-br/v2);
* [Guia rápido para o Git](http://rogerdudler.github.io/git-guide/index.pt_BR.html),
  de Roger Dudler, para consulta de comandos;
* [Documentação do GitLab](https://docs.gitlab.com/).

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Explicar que problema o controle de versão resolve e por que salvar cópias
   numeradas de uma pasta não o resolve;
2. Descrever as três áreas de um repositório Git local e dizer em qual delas um
   arquivo está, a partir da saída de `git status`;
3. Executar o ciclo `add`, `commit` e `push`, e ler o que cada comando devolveu;
4. Escrever mensagens de *commit* que descrevam a alteração feita;
5. Ler o histórico de um repositório com `git log`, `git show` e `git diff`;
6. Resolver um conflito de mesclagem editando o arquivo marcado pelo Git;
7. Entregar um exercício da disciplina pelo GitLab, do *fork* ao `push`.

---

## 1. O problema do controle de versão

Você já resolveu esse problema à mão. A pasta do trabalho da disciplina anterior
provavelmente contém algo assim:

```
trabalho.odt
trabalho_v2.odt
trabalho_v2_revisado.odt
trabalho_FINAL.odt
trabalho_FINAL_agora_vai.odt
```

O arranjo funciona no sentido mais fraco possível: nada foi perdido. Fora isso,
ele não responde a nenhuma pergunta útil. Qual é a versão mais recente, a
`FINAL` ou a `FINAL_agora_vai`? O que exatamente mudou de `v2` para
`v2_revisado`? Aquele parágrafo que você apagou por engano na terça, em qual
arquivo ele ainda existe? E se o trabalho fosse em dupla, quem escreveu o quê?

Um **sistema de controle de versão** guarda o histórico de um conjunto de
arquivos e responde a essas perguntas. O que ele registra não é só o conteúdo
atual, e sim a **sequência de estados** pela qual o conjunto passou, com autoria,
data e uma descrição em cada passo.

O Git é o sistema de controle de versão dominante hoje. Foi criado em 2005 por
Linus Torvalds para o desenvolvimento do núcleo do Linux, é software livre e
roda inteiramente na sua máquina.

### O que o Git resolve nesta disciplina

**Histórico.** Você quebra o CSS às 22h e não faz ideia do que mexeu. Com o
histórico, você compara o estado atual com o do último *commit* e vê a linha
alterada.

**Duas máquinas.** Você começa o exercício no laboratório e termina em casa. Sem
um repositório central, a sincronização é feita com *pen drive* ou anexo de
e-mail, e uma hora as duas cópias divergem.

**Entrega auditável.** O professor precisa saber o que você entregou e quando. O
histórico de *commits* mostra também **como** você chegou lá, e é por isso que o
diário de bordo do trabalho prático é avaliado.

## 2. Primeiro contato: um repositório do zero

Antes da explicação, veja a coisa acontecendo. Acompanhe a demonstração do
professor e, se puder, digite junto. Os detalhes de cada comando são as próximas
seções desta aula; por ora observe apenas a **forma** do ciclo.

```bash
mkdir catalogo
cd catalogo
git init
```

```
Initialized empty Git repository in /home/ana/catalogo/.git/
```

Crie um arquivo qualquer na pasta e pergunte ao Git como estão as coisas:

```bash
echo '<h1>Catálogo de Produtos</h1>' > index.html
git status
```

```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	index.html

nothing added to commit but untracked files present (use "git add" to track)
```

Agora grave um ponto na história:

```bash
git add index.html
git commit -m "Cria página inicial do catálogo"
```

```
[main (root-commit) 5d09b83] Cria página inicial do catálogo
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
```

E confira o que ficou registrado:

```bash
git log
```

```
commit 5d09b83191365b932a94e1c90b68b58d5fe12c55
Author: Ana Souza <ana@example.com>
Date:   Sat Aug 8 16:51:53 2026 -0300

    Cria página inicial do catálogo
```

Quatro coisas a notar:

1. o Git não pediu para escolher uma pasta de destino nem um servidor: tudo o
   que ele fez foi dentro de `catalogo/`;
2. o arquivo passou por um estado intermediário, entre "existe na pasta" e "está
   gravado no histórico";
3. o registro gravado tem autor, data e mensagem;
4. e tem um identificador longo, `5d09b8319...`, que ninguém digitou.

Cada um desses quatro pontos é uma seção a seguir.

## 3. O repositório é a pasta `.git`

Quando você executou `git init`, o Git criou uma subpasta oculta chamada `.git`
dentro de `catalogo/`. É ali que mora o histórico inteiro: todas as versões de
todos os arquivos, todos os autores, todas as datas.

```bash
ls -a
```

```
.  ..  .git  index.html
```

Duas consequências práticas:

* **Copiar a pasta `catalogo/` copia o repositório inteiro**, histórico
  incluído. Apagar `.git` transforma o repositório de volta numa pasta comum,
  com os arquivos no estado atual e sem nenhum histórico.
* **O Git não precisa de rede.** Todos os comandos desta seção e das próximas
  funcionam com o cabo desconectado. A rede só entra quando existe um
  repositório remoto, o que é assunto da seção 10.

> **Nunca crie um repositório dentro de outro, a não se que saiba o que está fazendo.** 
> Se você executar `git init`
> numa pasta que já está dentro de um repositório, os dois passam a disputar os
> mesmos arquivos e a confusão é difícil de desfazer. Antes de `git init`,
> confira com `git status` se você já não está dentro de um repositório.

## 4. As três áreas

Um arquivo, num repositório Git, está em uma de três áreas.

| Área | Nome em inglês | O que é |
|---|---|---|
| Diretório de trabalho | *working tree* | A pasta como o editor de texto a vê |
| Área de preparação | *staging area*, *index* | A lista do que vai entrar no próximo *commit* |
| Repositório | *repository* | O histórico já gravado, dentro de `.git` |

O comando `git add` move a alteração do diretório de trabalho para a área de
preparação. O comando `git commit` move o que está na área de preparação para o
repositório.

A área de preparação é a parte que causa mais estranheza no primeiro contato,
porque parece um passo burocrático a mais. Ela existe para você **escolher o que
entra em cada commit**. Você mexeu em cinco arquivos, dois deles resolvem um
problema e três resolvem outro: dá para gravar dois *commits* separados, cada um
com sua mensagem, em vez de um pacote único e ininteligível.

Veja o mecanismo funcionando. Altere um arquivo já versionado e crie outro:

```bash
echo '<p>Em construção.</p>' >> index.html
echo 'body { font-family: sans-serif; }' > estilo.css
git status
```

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	estilo.css

no changes added to commit (use "git add" and/or "git commit -a")
```

O Git separa os dois casos. O `index.html` está **modificado** (*modified*):
o Git já conhece esse arquivo e percebeu que ele mudou. O `estilo.css` está
**não rastreado** (*untracked*): o Git nunca o viu antes e não vai gravá-lo sem
ordem explícita.

Prepare só o `index.html` e grave:

```bash
git add index.html
git commit -m "Adiciona aviso de página em construção"
```

```
[main 7f95d85] Adiciona aviso de página em construção
 1 file changed, 1 insertion(+)
```

```bash
git status --short
```

```
?? estilo.css
```

O `estilo.css` continua de fora. Foi decisão sua, expressa pelo `git add` que
você não deu. **Arquivo que não passou por `git add` não está no commit**, e
essa é a causa mais comum de "entreguei e o professor disse que faltava
arquivo".

A opção `--short` mostra o mesmo estado em formato compacto, com duas colunas: a
primeira é a área de preparação, a segunda é o diretório de trabalho. Na tabela
abaixo, o `_` representa uma coluna em branco; no terminal você verá um espaço.

| Código | Significado |
|---|---|
| `??` | Arquivo não rastreado |
| `_M` | Modificado, ainda não preparado |
| `M_` | Modificado e preparado |
| `A_` | Arquivo novo, já preparado |
| `MM` | Preparado e modificado de novo depois disso |

`git status` é o comando que você mais vai usar. Execute-o antes de cada `add`,
antes de cada `commit` e depois de cada `push`.

## 5. O commit

Um *commit* é o registro de um estado completo do projeto, e não a lista do que
mudou. O Git guarda a **fotografia** de todos os arquivos naquele instante.
(Internamente ele evita duplicar o conteúdo de arquivos que não mudaram, mas
isso é otimização de armazenamento e não altera o modelo conceitual.)

Cada *commit* carrega:

* o conteúdo de todos os arquivos versionados naquele instante;
* o **autor**, com nome e e-mail, vindos da configuração do Git;
* a **data**;
* a **mensagem** que você escreveu;
* o **commit anterior**, chamado de *pai*;
* um **identificador** de 40 caracteres hexadecimais, o *hash*.

O identificador é calculado a partir de todo o resto: conteúdo, autor, data,
mensagem e pai. Alterar qualquer um desses itens produz um identificador
diferente. É daí que vem a garantia de integridade do histórico, e é por isso
que reescrever o passado num repositório compartilhado dá tanto trabalho.

Na prática ninguém digita os 40 caracteres. Os sete primeiros bastam para
identificar o *commit*, e é isso que o `git log --oneline` mostra:

```bash
git log --oneline
```

```
7f95d85 Adiciona aviso de página em construção
5d09b83 Cria página inicial do catálogo
```

O campo *pai* é o que dá ao histórico a forma de corrente: cada *commit* aponta
para o anterior, e é possível caminhar do estado atual até o primeiro registro.

### Nome e e-mail nos commits

O autor de cada *commit* vem da configuração do Git, não do serviço onde o
repositório está hospedado. Se você não configurar nada, o Git inventa algo a
partir do usuário do sistema operacional, e o histórico fica assinado por
`aluno@lab-a15-pc07`.

No seu computador, configure uma vez:

```bash
git config --global user.name "Seu Nome Completo"
git config --global user.email "seuemail@example.com"
```

Nos computadores do laboratório, a máquina é de todo mundo. Não use `--global`:
configure dentro de cada repositório clonado.

```bash
git config user.name "Seu Nome Completo"
git config user.email "seuemail@example.com"
```

Para ver o que está valendo e de qual arquivo cada valor veio:

```bash
git config --list --show-origin | grep user
```

### O nome do ramo inicial

Ao executar `git init` numa instalação recém-feita, o Git avisa:

```
hint: Using 'master' as the name for the initial branch. This default branch name
hint: will change to "main" in Git 3.0. To configure the initial branch name
hint: to use in all of your new repositories, which will suppress this warning,
hint: call:
hint:
hint: 	git config --global init.defaultBranch <name>
```

O GitLab usa `main` como padrão. Para que os seus repositórios locais nasçam
com o mesmo nome, e para o aviso parar de aparecer:

```bash
git config --global init.defaultBranch main
```

O conceito de *ramo* (*branch*) não é assunto desta disciplina. Trate `main`
como o nome da única linha do tempo com que vamos trabalhar.

## 6. Mensagens de commit

A mensagem é o único campo do *commit* que depende de você, e é o que torna o
histórico legível ou inútil. Compare:

```
9b1c2d7 update
4a8e330 asdf
c1d0f92 correções
```

```
9b1c2d7 Corrige alinhamento dos cards no catálogo em telas estreitas
4a8e330 Adiciona validação de e-mail no formulário de contato
c1d0f92 Substitui imagens placeholder pelas fotos definitivas
```

O segundo histórico permite achar quando algo entrou sem abrir um único arquivo.

Convenções que valem para as entregas da disciplina:

* **Escreva o que o commit faz**, no presente do indicativo, com verbo no
  início: "Adiciona", "Corrige", "Remove", "Atualiza".
* **Uma linha de até cerca de 50 caracteres.** Se precisar de mais explicação,
  o `git commit` sem `-m` abre um editor onde você escreve o título, deixa uma
  linha em branco e escreve o corpo.
* **Um commit por alteração com sentido próprio.** Nem um *commit* por letra
  digitada, nem um *commit* único no fim da aula chamado `entrega`.

## 7. Ler o histórico

O `git log` mostra os *commits*, do mais recente para o mais antigo:

```bash
git log --oneline
```

Para ver o que um *commit* específico mudou, com o texto das linhas alteradas:

```bash
git show 7f95d85
```

```
commit 7f95d857f84c74ac5325759dcf0ac9de11ed1816
Author: Ana Souza <ana@example.com>
Date:   Sat Aug 8 16:52:03 2026 -0300

    Adiciona aviso de página em construção

diff --git a/index.html b/index.html
index 434dae7..cede13d 100644
--- a/index.html
+++ b/index.html
@@ -1 +1,2 @@
 <h1>Catálogo de Produtos</h1>
+<p>Em construção.</p>
```

O bloco a partir de `diff --git` é o formato padrão de comparação. As linhas com
`+` foram acrescentadas, as com `-` foram removidas, e as sem marca são contexto,
mostradas apenas para você se localizar.

A linha `@@ -1 +1,2 @@` é o cabeçalho do pedaço (*hunk*) que vem logo abaixo dela.
Um arquivo grande com alterações em pontos distantes gera vários pedaços, cada um
com seu cabeçalho, e é ele que diz onde no arquivo aquele bloco começa. O formato
é `@@ -início,quantidade +início,quantidade @@`, em que o `-` se refere à versão
antiga do arquivo e o `+` à versão nova.

No exemplo acima, `-1` diz que, no arquivo antigo, o pedaço começa na linha 1 e
tem 1 linha (quando a quantidade é 1, o Git omite o número). Já `+1,2` diz que,
no arquivo novo, o mesmo pedaço começa na linha 1 e passou a ter 2 linhas. Casa
com o que o diff mostra: o `<h1>` continuou onde estava e o `<p>` entrou depois
dele.

Em um arquivo maior, você veria algo como `@@ -12,7 +12,9 @@`: sete linhas a
partir da linha 12 no arquivo antigo viraram nove linhas a partir da linha 12 no
novo. Os números não precisam ser decorados; o que importa é saber que eles
localizam a alteração dentro do arquivo, e não contam quantas linhas mudaram.

O `git diff`, sem argumentos, compara o **diretório de trabalho com a área de
preparação**, ou seja, mostra o que você alterou e ainda não preparou:

```bash
git diff
```

Depois de um `git add`, esse mesmo comando não mostra mais nada, porque a
alteração deixou de estar pendente. Para ver o que está preparado e vai entrar
no próximo *commit*:

```bash
git diff --staged
```

Confundir os dois é frequente. `git diff` responde "o que eu mexi e ainda não
marquei"; `git diff --staged` responde "o que vai entrar se eu der `commit`
agora".

## 8. Desfazer

Enquanto o *commit* não foi feito, desfazer é simples.

Para tirar um arquivo da área de preparação, mantendo as alterações no
diretório de trabalho:

```bash
git restore --staged index.html
```

Para descartar as alterações do diretório de trabalho e voltar o arquivo ao
estado do último *commit*:

```bash
git restore index.html
```

> **`git restore` sem `--staged` apaga o seu trabalho e não tem desfazer.** O
> conteúdo descartado nunca chegou a ser gravado em nenhum *commit*, então não
> existe cópia em lugar nenhum. Confira com `git diff` o que você está prestes a
> jogar fora antes de executar o comando.

Depois que o *commit* foi feito, desfazer envolve comandos que não são assunto
desta disciplina (`git revert`, `git reset`). Uma saída suficiente para o nosso
caso: faça um *commit* novo que corrija o problema. O histórico registra o erro
e a correção, o que é honesto e, num diário de bordo, é exatamente o que se quer
ver.

## 9. Arquivos que ficam de fora: `.gitignore`

Nem tudo o que está na pasta deve entrar no repositório. Senhas, arquivos
temporários do editor, saída de compilação e bibliotecas baixadas ficam de fora.

Crie um arquivo chamado `.gitignore` na raiz do repositório, com um padrão por
linha:

```
senha.txt
*.log
node_modules/
.DS_Store
```

Com esse arquivo no lugar, o `git status` deixa de listar `senha.txt` e
`debug.log`:

```bash
git status --short
```

```
?? .gitignore
?? estilo.css
```

Para descobrir qual regra está escondendo um arquivo:

```bash
git check-ignore -v senha.txt debug.log
```

```
.gitignore:1:senha.txt	senha.txt
.gitignore:2:*.log	debug.log
```

Duas ressalvas. O próprio `.gitignore` deve ser versionado, para valer também
para quem clonar o repositório. E o arquivo só afeta arquivos **não rastreados**:
se você já deu `git add` num arquivo, acrescentá-lo ao `.gitignore` depois não o
remove do repositório.

## 10. Repositório remoto

Até aqui tudo aconteceu numa máquina só. Um **repositório remoto** é uma cópia
do repositório hospedada em outro lugar, geralmente num servidor, que serve de
ponto de encontro entre as suas máquinas e entre você e o professor.

O apelido padrão do remoto principal é `origin`. Não é palavra reservada, é
convenção.

```bash
git remote -v
```

```
origin	https://gitlab.com/ana/catalogo.git (fetch)
origin	https://gitlab.com/ana/catalogo.git (push)
```

Três comandos movem dados entre o seu repositório e o remoto:

| Comando | Direção | O que faz |
|---|---|---|
| `git clone URL` | remoto para local | Cria uma cópia local completa, com histórico e `origin` já configurado |
| `git push` | local para remoto | Envia os *commits* que só existem na sua máquina |
| `git pull` | remoto para local | Traz os *commits* que só existem no remoto e os integra ao seu histórico |

Repare que `push` e `pull` transferem **commits**, e não arquivos soltos. Só vai
para o servidor aquilo que passou por `git commit`.

### O endereço do remoto é uma URL, e o transporte é HTTP

O endereço `https://gitlab.com/ana/catalogo.git` tem exatamente a estrutura
vista na aula sobre HTTP: esquema, host e caminho. Quando você executa
`git push` num remoto `https://`, o Git faz requisições HTTP ao servidor, com
método, cabeçalhos e código de status, como qualquer outra requisição.

Isso explica dois comportamentos que você vai encontrar. Se o repositório não
existir ou for privado, o servidor responde com um erro de autenticação e o Git
pede usuário e senha. E se a rede do laboratório bloquear a porta, o `push`
falha por tempo esgotado, sem que haja nada de errado com o seu repositório.

O primeiro `push` de um ramo novo precisa dizer para onde vai:

```bash
git push -u origin main
```

```
To https://gitlab.com/ana/catalogo.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

A opção `-u` grava a associação entre o seu `main` local e o `main` do `origin`.
Depois disso, `git push` e `git pull` sem argumentos já sabem o destino.

## 11. GitLab

Um **repositório remoto** precisa estar hospedado em algum lugar. Poderia ser um
servidor da própria universidade, mas na prática usa-se um serviço de hospedagem
que, além do Git, oferece interface web, controle de acesso, relatório de
problemas e revisão de código. Esse tipo de serviço é chamado de **forja**
(*forge*). GitHub, GitLab e Codeberg são forjas.

Nenhum desses recursos faz parte do Git. O Git é o programa que roda na sua
máquina; a forja é um serviço construído em volta dele. Trocar de forja não
altera em nada os comandos das seções anteriores.

O **[GitLab](https://gitlab.com)** é o serviço adotado nesta disciplina. O plano
gratuito permite repositórios privados sem limite de quantidade, que é o que
viabiliza o arranjo descrito adiante.

### Grupo

Um **grupo** no GitLab é um espaço que reúne vários repositórios sob um mesmo
controle de acesso. Quem é membro do grupo alcança todos os repositórios dentro
dele, sem precisar ser adicionado um a um.

Nesta disciplina, cada aluno cria **um grupo privado seu** e adiciona o
professor como membro. Todas as tarefas do semestre vão para dentro desse grupo.
Você mantém o controle do que é seu, o professor enxerga o conjunto, e ninguém
mais tem acesso.

### Fork

Um ***fork*** é uma cópia de um repositório feita **no servidor**, sob a sua
conta ou grupo. O original continua onde estava, sem alteração, e você passa a
ter um repositório próprio com o mesmo conteúdo e o mesmo histórico, sobre o
qual tem permissão de escrita.

É esse o mecanismo das entregas: o professor publica um repositório com o
enunciado, cada aluno faz o *fork* para dentro do próprio grupo, e resolve ali.

O `fork` acontece no site. O `clone` acontece na sua máquina. A sequência
completa de uma entrega é `fork` no GitLab, `clone` para o computador, `add`,
`commit`, `push`.

## 12. Entrega de exercícios nesta disciplina

O procedimento detalhado, com as telas e os nomes dos botões, está em
[Entrega de exercícios e trabalhos no GitLab](./instrucoes_submissao_tarefas_e_trabalhos.md).
Em resumo:

* **Uma vez no semestre**: criar o grupo privado `ds122-2026-2-turno-grr` e
  adicionar o usuário `alexkutzke` como `reporter`.
* **A cada tarefa**: *fork* do repositório-modelo para dentro do seu grupo,
  resolver, `push`.

Não existe link a enviar. O professor recolhe as entregas pelo nome do grupo,
com um programa que clona os repositórios de todos os alunos. Por isso o nome do
grupo precisa seguir exatamente o padrão, e o `alexkutzke` precisa estar como
`reporter`: grupo com nome fora do padrão não é encontrado, e o que não é
encontrado não é corrigido.

O endereço do repositório-modelo de cada tarefa e o prazo de entrega ficam na
UFPR Virtual, e não neste material, porque mudam a cada semestre.

---

# Parte prática

A partir daqui você digita. A primeira metade repete, com calma, o ciclo local
demonstrado na seção 2, agora com nome nas coisas. A segunda leva o repositório
ao GitLab.

## 13. Preparar o ambiente

Confirme que o Git está instalado:

```bash
git --version
```

```
git version 2.55.0
```

Se o comando não for encontrado, instale a partir de
<https://git-scm.com/downloads>. No Windows, o instalador oficial traz junto o
**Git Bash**, um terminal onde todos os comandos desta aula funcionam como
escritos. Use-o. No PowerShell alguns exemplos se comportam de outro jeito.

Configure a identidade e o nome do ramo inicial. **No laboratório, execute os
dois primeiros comandos sem `--global`, e dentro de cada repositório clonado.**

```bash
git config --global user.name "Seu Nome Completo"
git config --global user.email "seuemail@example.com"
git config --global init.defaultBranch main
```

Confira:

```bash
git config --list --show-origin | grep -E "user|defaultBranch"
```

## 14. O ciclo local, passo a passo

Crie um repositório novo e percorra o ciclo devagar, executando `git status`
entre cada par de comandos e lendo a saída antes de seguir.

```bash
mkdir pratica-git
cd pratica-git
git init
git status
```

Crie dois arquivos:

```bash
echo '<h1>Página de prática</h1>' > index.html
echo 'body { margin: 0; }' > estilo.css
git status
```

Os dois aparecem como não rastreados. Prepare **apenas um** e observe a
diferença:

```bash
git add index.html
git status
```

O `index.html` aparece agora sob `Changes to be committed`, e o `estilo.css`
continua sob `Untracked files`. Grave:

```bash
git commit -m "Cria página de prática"
git log --oneline
```

Só o `index.html` entrou. Prepare e grave o segundo arquivo, e confirme que o
histórico tem dois *commits*:

```bash
git add estilo.css
git commit -m "Adiciona folha de estilo"
git log --oneline
```

Agora altere um arquivo já versionado e compare antes de gravar:

```bash
echo '<p>Segunda linha.</p>' >> index.html
git diff
git add index.html
git diff
git diff --staged
```

O segundo `git diff` não devolve nada, e o `git diff --staged` mostra a linha
acrescentada. Grave e confira com `git show`:

```bash
git commit -m "Acrescenta parágrafo à página de prática"
git show
```

## 15. Ignorar arquivos

Ainda em `pratica-git`, crie um arquivo que não deve ser versionado e verifique
que o Git o oferece:

```bash
echo 'minha-senha-secreta' > senha.txt
git status --short
```

Crie o `.gitignore` e verifique de novo:

```bash
printf 'senha.txt\n*.log\n' > .gitignore
git status --short
git check-ignore -v senha.txt
```

O `senha.txt` sumiu da listagem. Versione o `.gitignore`:

```bash
git add .gitignore
git commit -m "Ignora arquivos de senha e de log"
```

## 16. Conta e grupo no GitLab

A conta usada na disciplina é criada com dados institucionais, mesmo que você já
tenha uma conta pessoal no GitLab:

1. Acesse <https://gitlab.com/users/sign_up> e cadastre-se com o **e-mail da
   UFPR**, usando o seu **GRR como nome de usuário**, em minúsculas
   (`grr20249999`). Confirme o e-mail que o GitLab envia;
2. Na tela **Welcome to GitLab**, que aparece logo depois, preencha `UFPR` em
   **Company name** e use o nome do grupo da disciplina em **Group name**
   (próxima seção). O passo a passo campo a campo está em
   [Criação da conta no GitLab](./instrucoes_criacao_conta_gitlab.md);
3. Em **Edit profile**, confira o nome completo.

O GRR no nome de usuário e o e-mail institucional resolvem a identificação: o
professor sabe de quem é cada repositório sem perguntar. A conta separada
também mantém os trabalhos da disciplina fora dos seus projetos e grupos
pessoais.

O grupo que vai guardar todas as tarefas do semestre é criado **uma vez só**, e
a tela **Welcome to GitLab** já obriga a criar um durante o cadastro. Quem
seguiu o passo a passo chega aqui com o grupo pronto e só confere os itens 4 e
5; quem não tem grupo nenhum, cria agora:

4. No menu **+**, escolha **New group**, e depois **Create group**;
5. Em **Group name**, use `ds122-2026-2-turno-grr`, tudo em minúsculas,
   trocando `turno` por `n` ou `t` e `grr` pelo seu GRR. Exemplo:
   `ds122-2026-2-t-grr20261234`. Num grupo já existente, o nome e o endereço se
   corrigem em **Settings > General**;
6. Em **Visibility level**, marque **Private**;
7. Com o grupo no lugar, entre em **Manage > Members**, clique em **Invite
   members**, procure por `alexkutzke` e atribua o papel **Reporter**.

O papel `Reporter` deixa o professor ler e comentar, sem poder alterar o seu
código.

## 17. Fork, clone e push

Esta é a retomada do ciclo da seção 2, agora com um repositório remoto no meio.

1. Abra o endereço do repositório da tarefa, publicado na UFPR Virtual.
2. Clique em **Fork**, escolha **o seu grupo** em **Project URL**, deixe a
   visibilidade em **Private** e clique em **Fork project**.
3. Na página do seu *fork*, copie o endereço HTTPS e clone:

```bash
git clone https://gitlab.com/ds122-2026-2-t-grr20261234/nome-da-tarefa.git
cd nome-da-tarefa
```

Confira o que o `clone` configurou sozinho:

```bash
git remote -v
git log --oneline
git branch --show-current
```

O `origin` já está apontando para o seu *fork*, o histórico do repositório
original veio junto e o ramo atual é `main`. Nada disso precisou de `git init`
nem de `git remote add`: o `clone` faz tudo.

4. Resolva o primeiro item da tarefa, grave e envie:

```bash
git add .
git commit -m "Resolve exercício 1"
git push
```

5. **Abra o seu fork no navegador e confirme que a alteração está lá.** Enquanto
   não aparecer na página do GitLab, a tarefa não foi entregue.

Repita `add`, `commit` e `push` a cada item resolvido.

## 18. Quando o push é recusado

Situação real: você deu `push` de casa na quarta e, na sexta, no laboratório,
está com uma cópia antiga. O Git recusa o envio:

```bash
git push origin main
```

```
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://gitlab.com/ana/catalogo.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally.
```

A recusa protege o histórico: aceitar o envio apagaria do servidor os *commits*
que você não tem. A saída é trazer o que falta antes de enviar:

```bash
git pull
git push
```

Numa instalação nova, o `git pull` pode parar com esta mensagem:

```
hint: You have divergent branches and need to specify how to reconcile them.
fatal: Need to specify how to reconcile divergent branches.
```

O Git está perguntando como juntar as duas linhas do tempo. Escolha a mesclagem,
que é a opção adequada aqui, e repita o `pull`:

```bash
git config --global pull.rebase false
git pull
```

```
Merge made by the 'ort' strategy.
 contato.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 contato.html
```

O Git criou um *commit* de mesclagem, que tem dois pais e une as duas linhas.
Agora o `git push` é aceito.

**Hábito que evita o problema:** dê `git pull` ao **começar** a trabalhar, antes
de editar qualquer arquivo, e `git push` ao terminar.

## 19. Conflito de mesclagem

O `pull` da seção anterior funcionou porque as duas máquinas mexeram em arquivos
diferentes. Quando as duas alteram **a mesma linha do mesmo arquivo**, o Git não
tem como decidir e para, pedindo que você decida.

Provoque o caso. Na página do seu *fork*, no GitLab, edite o `README.md` pelo
próprio site (ícone de lápis), altere a primeira linha e confirme a alteração.
Na sua máquina, sem dar `pull`, altere **a mesma linha** para outra coisa e
grave:

```bash
git commit -am "Altera o título pela máquina local"
git pull
```

```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

O `git status` diz onde está o problema:

```
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   README.md
```

Abra o arquivo no editor. O Git escreveu as duas versões dentro dele, separadas
por marcadores:

```
<<<<<<< HEAD
# Catálogo Verde
=======
# Catálogo de Produtos da Ana
>>>>>>> c34ab3031b9f162880e357de2a5bc27e68d9b6ca
```

Entre `<<<<<<< HEAD` e `=======` está a **sua** versão local. Entre `=======` e
`>>>>>>>` está a versão que veio do servidor, identificada pelo *hash* do
*commit*.

Resolver o conflito é editar o arquivo até ele ficar como deve ficar, o que
inclui **apagar as três linhas de marcador**. Você pode ficar com uma das
versões, com a outra, ou escrever uma terceira. Depois disso, marque como
resolvido e grave:

```bash
git add README.md
git commit
git push
```

O `git commit` sem `-m`, aqui, abre o editor com uma mensagem de mesclagem já
preenchida. Basta salvar e sair.

> Conflito não é erro nem defeito do Git. É o Git recusando-se a escolher por
> você entre duas alterações incompatíveis. O erro seria resolvê-lo apagando o
> trabalho do colega sem ler.

## 20. Exercícios

**Estes exercícios valem nota** e compõem o item *Exercícios em sala* da média.
**A entrega é até o final do encontro de hoje.**

A entrega é o próprio *fork* no GitLab, com os *commits* pedidos e o `push`
feito. **Não há link a enviar em lugar nenhum**: o professor recolhe os
repositórios pelo nome do grupo. O que precisa estar certo é o nome do grupo, o
`alexkutzke` como `reporter` e o *fork* dentro do grupo.

O endereço do repositório-modelo está na atividade da UFPR Virtual. Trabalhe em
dupla no formato de [Mob Programming](./00_mob_programming.md): um dos dois faz
o *fork* no próprio grupo e adiciona o colega ao repositório como `developer`.
Registrem no `README.md` o nome completo e o GRR dos dois. Ao usar IA generativa
durante a aula, aplique o
[pré-prompt sugerido](./00_pre_prompt_para_ia_gen_durante_aulas.md).

São cinco itens, e todos mexem em pouca coisa: editar o `README.md` e criar
alguns arquivos. O que está sendo avaliado é o **histórico** que vocês
produzirem, e não o conteúdo dos arquivos.

**1.** Faça o *fork* do repositório da tarefa, clone-o e configure `user.name` e
`user.email` dentro dele. No `README.md`, escreva o nome completo e o GRR dos
dois integrantes. Grave e envie:

```bash
git add README.md
git commit -m "Registra os integrantes da dupla"
git push
```

Confira no navegador que o *commit* apareceu no GitLab antes de seguir.

**2.** Crie um arquivo `anotacoes.md` com um resumo, em suas palavras, da
diferença entre o diretório de trabalho, a área de preparação e o repositório.
Grave em um *commit* separado, com mensagem descritiva.

**3.** Crie dois arquivos novos de uma vez, `contato.html` e `sobre.html`, com
uma linha qualquer em cada. Prepare **apenas o `contato.html`** e grave o
*commit*. Logo depois, cole no `README.md` a saída de `git status --short` e
escreva uma frase explicando por que o `sobre.html` ficou de fora. Grave esse
segundo *commit* e envie os dois com `git push`.

**4.** Crie um arquivo `rascunho.log` e um `.gitignore` que o esconda. Cole no
`README.md` a saída de `git check-ignore -v rascunho.log`. Versione o
`.gitignore` e envie.

**5.** Provoque um conflito seguindo a seção 19: edite o `README.md` pelo site
do GitLab, edite **a mesma linha** na sua máquina, e dê `git pull`. Resolva o
conflito, grave e envie. Ainda no `README.md`, descreva em até três frases o que
o Git mostrou e como vocês decidiram qual versão manter.

Ao terminar, abra o *fork* no navegador e confirme que todos os *commits* estão
lá. Commit que não foi enviado não existe para quem corrige.

### Desafio

Para quem terminar antes. Um *commit* guarda a fotografia completa do projeto,
mas o Git não duplica o conteúdo de arquivos que não mudaram. Descubra, usando
`git cat-file -p` sobre o *hash* de dois *commits* seguidos, o que de fato muda
entre eles na estrutura interna. Comece por `git cat-file -p HEAD` e siga os
identificadores que aparecerem.

---

## Resumo

* Controle de versão guarda a sequência de estados de um projeto, com autoria,
  data e descrição em cada passo. Cópias numeradas de uma pasta não fazem isso.
* O repositório é a pasta `.git`. O Git funciona sem rede; o remoto é opcional.
* Um arquivo está no diretório de trabalho, na área de preparação ou no
  repositório. O `git add` avança uma área, o `git commit` avança a outra.
* Arquivo que não passou por `git add` não entra no *commit*.
* O *commit* guarda a fotografia do projeto, o autor, a data, a mensagem, o
  *commit* pai e um identificador calculado a partir de tudo isso.
* `git status` responde onde você está; `git diff` e `git diff --staged`
  respondem o que mudou, em relação a áreas diferentes.
* O remoto é uma cópia hospedada. `clone` traz, `push` envia, `pull` traz e
  integra. Os três transferem *commits*, nunca arquivos soltos.
* Uma forja é o serviço em volta do Git. Trocar de forja não muda os comandos.
* O *fork* é uma cópia feita no servidor, sob a sua conta, e é o que torna a
  entrega possível sem dar acesso de escrita ao repositório do professor.
* Conflito acontece quando duas alterações tocam a mesma linha. O Git marca o
  arquivo e a decisão é sua.
