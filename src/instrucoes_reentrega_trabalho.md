# Reentrega das etapas 1 e 2 do trabalho

O prazo de reentrega da Entrega 1 (HTML5 e CSS3) e da Entrega 2 (JavaScript)
vai até **20/11/2026, às 23h59**. A reentrega é opcional e a nota já lançada só
pode subir.

As três entregas do trabalho usam o mesmo repositório, `ds122-project-2026-2`.
Até 20/11 você também estará convertendo o projeto para PHP, na Entrega 3, e
esses arquivos não podem entrar na correção das duas primeiras. Por isso a
reentrega é feita num **ramo** (*branch*) separado, chamado `reentrega`.

Um ramo é uma linha de commits paralela à do `main`. O que você commitar no
`reentrega` não aparece no `main`, e o contrário também vale.

## Como a reentrega é avaliada

- E1 e E2 são corrigidas pelo **último commit do ramo `reentrega` feito até
  20/11/2026, às 23h59**. Commits no `main` não contam para a reentrega.
- A reentrega só entra na recorreção se for **declarada por e-mail** até
  20/11/2026, às 23h59 (passo 6). Quem não declarar, ou não criar o ramo
  `reentrega`, mantém as notas de E1 e E2 já lançadas.
- A Entrega 3 continua no `main`, com o prazo de 27/11/2026.
- Os demais exercícios da disciplina não usam ramo: a reentrega deles é feita
  no próprio repositório de cada exercício, no `main`, como sempre, e também
  precisa ser declarada no mesmo e-mail.

## Passo a passo

Faça a reentrega numa **segunda pasta**, separada da que você usa na Entrega 3.
Trocar de ramo dentro da mesma pasta leva junto os arquivos ainda não
commitados, e os `.php` da Entrega 3 acabariam no ramo errado.

### 1. Clonar o repositório numa pasta nova

Fora da pasta atual do projeto, clone o seu fork de novo, com outro nome de
pasta. Use o mesmo endereço do seu primeiro clone (veja
[Autenticação no GitLab](./instrucoes_autenticacao_git.md)):

```bash
git clone <endereço do seu fork> ds122-project-reentrega
cd ds122-project-reentrega
```

### 2. Escolher o commit de partida

O ramo `reentrega` precisa começar de um commit **anterior ao PHP**.

Se você ainda não começou a Entrega 3, parta do `main` como está e pule para o
passo 3, sem informar commit.

Se já começou, liste os commits:

```bash
git log --oneline
```

Escolha o último commit antes de qualquer arquivo `.php`. Na dúvida, o comando
abaixo mostra o último commit até o prazo da Entrega 2 (11/10/2026), que é o
commit avaliado na correção dela:

```bash
git log -1 --oneline --before="2026-10-11 23:59"
```

O código no começo da linha (por exemplo, `7936f92`) identifica o commit.

### 3. Criar o ramo `reentrega`

Com o código do commit escolhido no passo 2:

```bash
git switch -c reentrega 7936f92
```

Se você partiu do `main` atual, sem informar commit:

```bash
git switch -c reentrega
```

O nome do ramo deve ser exatamente `reentrega`, em minúsculas e sem acento.

Confira o resultado antes de continuar:

```bash
git branch --show-current   # deve mostrar: reentrega
ls                          # não deve haver arquivo .php
```

### 4. Corrigir, commitar e enviar

Faça as correções apontadas na devolutiva de E1 e E2 e commite normalmente.
**No primeiro envio**, informe o ramo:

```bash
git add .
git commit -m "Corrige a validação do formulário de cadastro"
git push -u origin reentrega
```

Do segundo envio em diante, basta `git push`.

### 5. Conferir no GitLab

Na página do seu fork, o seletor de ramos (acima da lista de arquivos) deve
mostrar `reentrega`. Selecione o ramo e confira se os seus commits estão lá.

### 6. Declarar a reentrega por e-mail

Depois do último commit, envie **um único e-mail** para `alexander@ufpr.br`
até 20/11/2026, às 23h59, com:

- **Assunto**: `DS122: reentrega`;
- nome completo, GRR e turma (tarde ou noite);
- a lista do que foi reentregue: E1, E2 e os exercícios, pelo nome do
  repositório (por exemplo, `ds122-css-assignment`).

Exemplo:

```text
Assunto: DS122: reentrega

Nome: Maria da Silva
GRR: GRR20269999
Turma: noite

Reentreguei: E1 e E2 do trabalho (ramo reentrega), ds122-css-assignment,
ds122-dom-assignment.
```

O e-mail declara o que deve ser corrigido de novo. O que é avaliado continua
sendo o último commit até o prazo.

## Cuidados

- **Não mude o ramo padrão** do repositório em **Settings > Repository**. Ele
  continua sendo o `main`, que é onde a Entrega 3 é corrigida.
- Não é preciso levar as correções do `reentrega` para o `main`. Se a Entrega 3
  depender delas, refaça a correção também na pasta da Entrega 3.
- Antes de cada commit, confira em que pasta e em que ramo você está, com
  `pwd` e `git branch --show-current`.
