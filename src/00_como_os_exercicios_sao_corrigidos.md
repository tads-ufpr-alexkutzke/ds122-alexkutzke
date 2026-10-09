# Como os exercícios são corrigidos

Cada exercício traz, no `README.md`, a tabela **Como será corrigido**, com os
itens e o peso de cada um. Esta página reúne as regras que valem em todos os
exercícios, a partir do de JavaScript: como a nota é calculada e quanto custa
cada falha que se repete de um exercício para outro.

O `README.md` de um exercício pode trazer regra própria. Quando trouxer, vale a
dele.

## A nota

A nota vai de 0 a 100, em múltiplos de 5. O peso de cada item do `README.md` é
convertido para essa escala: num exercício com pesos que somam 10, um item de
peso 2 vale 20 pontos.

Cada falha encontrada desconta pontos **do item a que ela pertence**, e um item
nunca perde mais do que vale. Se o histórico de *commits* vale 10 pontos, você
perde no máximo 10 por ele, ainda que acumule mais de um problema.

No fim, a nota é arredondada para o múltiplo de 5 mais próximo. Quando cair no
meio do caminho, sobe. A nota 100 só sai quando nada ficou faltando.

## Descontos que valem em todo exercício

### O que o enunciado pede

| Situação | Desconto |
|---|---:|
| cada marcador (`*`) de lista do enunciado não cumprido, ou cada instrução explícita não seguida ("dê ao `<nav>` o atributo `class="menu"`", "sem usar `outline: none`") | 3 |

O marcador conta uma vez, ainda que falte mais de uma parte dele. Se o enunciado
pede `padding`, borda e a mesma largura nos campos e faltou a largura, o
desconto é 3; se faltaram as três coisas, também é 3.

### Histórico no GitLab

| Situação | Desconto |
|---|---:|
| o exercício inteiro num *commit* só | 10 |
| itens que o enunciado manda gravar em separado saíram no mesmo *commit* | 3 |
| mensagem de *commit* que não diz o que foi feito | 3 |

Uma mensagem como `Ex 1`, `update`, `index.html` ou a mesma mensagem repetida em
*commits* seguidos não diz o que foi feito. `Estiliza os cartoes do catalogo` diz.
Cada um desses descontos é aplicado uma vez por exercício.

### O `respostas.md`

| Situação | Desconto |
|---|---:|
| seção inteira em branco | 5 por seção |
| cada pedido da seção que ficou sem resposta, ou com resposta que contradiz o gabarito ou o seu próprio código | 2 por pedido, no máximo 5 por seção |
| correção que você diz ter feito e que não está no arquivo | metade do valor do defeito |
| seção **Integrantes** com o texto do modelo, ou GRR errado | 2 |

Pedido é cada coisa que o enunciado manda registrar: uma contagem, uma medida,
uma conta, uma previsão. Uma seção respondida com erro nunca custa mais do que a
mesma seção em branco.

Resposta conceitual incompleta, mas correta, não desconta.

### Caminhos de arquivo

| Situação | Desconto |
|---|---:|
| `href` ou `src` que começa com barra (`/css/estilo.css`) ou que usa barra invertida (`css\estilo.css`) | 3 |

O caminho tem de funcionar a partir do repositório, em qualquer computador e no
servidor. Caminho da sua máquina, como `C:\Users\...`, faz o arquivo não
carregar em lugar nenhum, e aí vale o desconto do exercício para recurso que não
carrega, que costuma ser o maior da tabela.

### Contraste de cores

Contraste só desconta quando a razão, calculada pela fórmula da WCAG, fica
abaixo de 4.5:1. Não há tolerância: 4.49:1 reprova. Confira o seu par no
[verificador do WebAIM](https://webaim.org/resources/contrastchecker/) antes de
entregar, inclusive as cores do `:hover`.

## O que não desconta

- arquivo a mais deixado no repositório, como uma página de teste;
- `README.md` ou `.gitignore` apagados (o comentário da correção vai avisar);
- nome de arquivo com acento ou com maiúscula e minúscula diferentes do pedido;
- arquivo em outra pasta, quando o enunciado não manda mover;
- escolha defensável que funciona: paleta de cores, nome de classe, largura
  fixa em vez de porcentagem, solução mais longa do que a necessária.

## Correção automática

Parte da correção usa ferramentas automáticas, que leem o repositório e apontam
o que falta segundo as regras desta página e do `README.md`. A nota lançada é
decisão do professor.

Mexer no repositório para enganar a correção automática é tratado como tentativa
de fraude na avaliação. Por exemplo: alterar os arquivos de verificação que
vieram no modelo, ou escrever código que só serve para passar na verificação sem
cumprir o que o enunciado pede.

## Se você discordar da nota

O comentário da correção, publicado como *issue* no seu *fork*, diz o que foi
descontado e onde. Procure o professor na aula seguinte com o comentário aberto.
Se o erro for da correção, a nota é refeita.
