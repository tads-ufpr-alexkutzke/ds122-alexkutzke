# Protocolo HTTP

## Bibliografia recomendada para o tema

* TANENBAUM, A. S. **Redes de Computadores**, cap. 7.3 (ver plano de ensino na UFPR Virtual);
* [MDN - HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP);
* [RFC 9110 - HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html) (leitura por consulta, não linear).

## Objetivos da aula

Ao final desta aula você deve ser capaz de:

1. Descrever a estrutura de uma mensagem HTTP byte a byte e identificar cada uma de suas partes;
2. Explicar o que significa o protocolo ser *sem estado* e quais consequências isso tem para qualquer aplicação web;
3. Identificar os três lugares por onde um cliente envia parâmetros ao servidor: caminho, consulta e corpo;
4. Escolher o método HTTP correto para uma operação, justificando com base em segurança e idempotência;
5. Interpretar códigos de status e cabeçalhos de resposta;
6. Construir requisições HTTP manualmente com a ferramenta `curl` e ler as respostas obtidas;
7. Atuar como servidor HTTP, respondendo manualmente a uma requisição feita por um navegador.

---

## 1. Modelo Cliente-Servidor

No modelo de programação para desktop existe, em geral, um único ator: o programa
roda na máquina do usuário, lê o teclado, escreve na tela e acessa arquivos locais.

O modelo *cliente-servidor* tem dois atores, em máquinas diferentes, conectados
por uma rede:

* **Cliente**: inicia a comunicação. Envia uma **requisição** e aguarda a resposta.
  O navegador é um cliente. O `curl` também é.
* **Servidor**: nunca inicia a comunicação. Fica permanentemente à espera
  (*escutando*) e, ao receber uma requisição, processa e devolve uma **resposta**.

Uma característica decisiva: **o servidor nunca inicia a comunicação**. Ele não
tem como avisar espontaneamente o navegador de que algo mudou na página. Toda
informação que chega ao cliente chega porque o cliente pediu.

Uma página que precisa exibir dados novos sem interação do usuário só tem uma
saída dentro do HTTP: perguntar de novo, de tempos em tempos. Contornar essa
limitação exige tecnologias construídas por cima do protocolo, como WebSocket e
*Server-Sent Events*, fora do escopo desta disciplina.

![Modelo Cliente-Servidor](images/http/http.gif)

## 2. O protocolo HTTP

Duas máquinas conectadas por rede conseguem trocar bytes. Isso não basta: elas
precisam concordar sobre o **significado** desses bytes. Quem manda primeiro?
Como se pede um documento? Como se responde que o documento não existe?

Um **protocolo** é exatamente esse acordo. Na web, o protocolo que define o
formato das requisições e das respostas é o **HTTP** (*HyperText Transfer
Protocol*), criado por Tim Berners-Lee no início dos anos 1990.

HTTP é um protocolo de **camada de aplicação**. Ele não se preocupa em fazer os
bytes chegarem ao destino, em ordem e sem perdas: isso é tarefa do TCP, na camada
de transporte, que fica abaixo dele. O HTTP assume que existe um canal confiável
e define o que trafega por ele.

### RFCs

As especificações que definem a Internet são publicadas como **RFCs** (*Request
for Comments*), numeradas sequencialmente e mantidas pela IETF. Uma RFC nunca é
editada depois de publicada: quando a especificação muda, publica-se uma RFC nova
que **torna obsoleta** (*obsoletes*) a anterior.

O HTTP/1.1 foi definido pela RFC 2616 em 1999. Essa RFC está obsoleta desde 2014,
e a maior parte do material antigo na Internet ainda a cita. As especificações
vigentes são:

| RFC | Ano | Define |
|---|---|---|
| 9110 | 2022 | Semântica do HTTP (métodos, status, cabeçalhos), comum a todas as versões |
| 9111 | 2022 | Cache |
| 9112 | 2022 | Sintaxe do HTTP/1.1 |
| 9113 | 2022 | HTTP/2 |
| 9114 | 2022 | HTTP/3 |

Ao abrir uma RFC, procure no topo os campos `Obsoletes` e `Obsoleted by`. É a
forma de saber se o documento que você está lendo ainda vale.

## 3. Primeiro contato: ver uma mensagem real

Antes de dissecar o protocolo, convém olhar uma conversa real. Os detalhes não
precisam ser entendidos ainda: cada um deles é uma seção desta aula. O que
importa agora é reconhecer a **forma** da mensagem.

### Pelo terminal

O `curl` é um cliente HTTP de linha de comando, já instalado no Linux, no macOS
e no Windows 10 e 11. A opção `-v` mostra a conversa:

```bash
curl -v https://example.com
```

Na saída, `>` marca as linhas que o `curl` **enviou**, `<` marca as que o
servidor **devolveu**, e `*` são comentários do próprio `curl` sobre a conexão.

Quatro coisas a notar:

1. tudo é **texto**;
2. a primeira linha é diferente das outras;
3. depois dela vem uma pilha de pares `Nome: valor`;
4. em seguida uma linha em branco, e só então o HTML.

### Pelo navegador

Abra qualquer site, pressione `F12` ou `Ctrl+Shift+I`, vá até a aba **Rede**
(*Network*) e recarregue a página com `Ctrl+R`. A lista que aparece traz uma
entrada por requisição. Clique em qualquer uma e procure a aba **Cabeçalhos**
(*Headers*).

São os mesmos dois blocos: o que o navegador mandou e o que o servidor
respondeu. Muda a apresentação.

### A mesma mensagem nas duas ferramentas

| Conceito | No `curl -v` | No navegador |
|---|---|---|
| Endereço pedido | primeira linha após `>` | *Request URL* |
| Verbo | `GET`, no início dessa linha | *Request Method* |
| Deu certo? | primeira linha após `<` | *Status Code* |
| Metadados enviados | pares após `>` | *Request Headers* |
| Metadados recebidos | pares após `<` | *Response Headers* |
| Conteúdo | depois da linha em branco | aba *Resposta* |

As seis linhas da tabela são, nesta ordem, o roteiro das próximas seções. As
duas ferramentas voltam em detalhe na parte prática.

## 4. Anatomia de uma mensagem HTTP

Uma mensagem HTTP/1.1 é **texto**, e você pode digitá-la à mão. Toda mensagem, de requisição ou de resposta, tem a mesma forma:

```
<linha inicial>
<Nome-do-cabeçalho>: <valor>
<Nome-do-cabeçalho>: <valor>
<linha em branco>
<corpo, opcional>
```

Cada linha termina com a sequência de dois caracteres **CRLF** (`\r\n`, retorno de
carro seguido de nova linha). Não é apenas `\n`, como é usual em arquivos de
texto no Linux. Esse detalhe é sintaxe, não formatação: uma mensagem com o
terminador errado é rejeitada.

A **linha em branco** separa os cabeçalhos do corpo. Ela é obrigatória, mesmo
quando não há corpo. É por isso que, ao digitar uma requisição manualmente, é
preciso pressionar `Enter` duas vezes.

### Requisição

```http
GET /docs/index.html HTTP/1.1
Host: www.example.com
User-Agent: curl/8.5.0
Accept: text/html

```

A **linha inicial** de uma requisição tem três partes separadas por espaço:

1. o **método** (`GET`), que diz o que se quer fazer;
2. o **alvo** (`/docs/index.html`), o caminho do recurso dentro do servidor;
3. a **versão** do protocolo (`HTTP/1.1`).

Repare que o alvo não contém o nome do site. O nome vai no cabeçalho `Host`.

### Resposta

```http
HTTP/1.1 200 OK
Date: Mon, 23 May 2026 22:38:34 GMT
Server: nginx
Content-Type: text/html; charset=UTF-8
Content-Length: 47
ETag: "3f80f-1b6-3e1cb03b"

<html>
<body>
<p>Ola mundo</p>
</body>
</html>
```

O valor `47` é o número exato de bytes do corpo, contando o `\n` ao final de cada
uma das cinco linhas. Confira.

A **linha inicial** de uma resposta tem três partes:

1. a **versão** do protocolo;
2. o **código de status** (`200`), numérico, destinado ao programa;
3. a **frase de motivo** (`OK`), destinada ao humano e ignorada pelo programa.

### O cabeçalho Host

`Host` é obrigatório em HTTP/1.1 e não existia em HTTP/1.0. A razão é econômica:
um único servidor, com um único endereço IP, hospeda centenas de sites
diferentes. Quando a requisição chega, o servidor precisa decidir qual site
entregar, e a única informação disponível para isso é o cabeçalho `Host`. Essa
técnica se chama **hospedagem virtual por nome** (*name-based virtual hosting*).

O caminho completo é: o navegador consulta o **DNS** para descobrir o IP
correspondente ao nome `www.example.com`, abre uma conexão TCP com esse IP, e
então informa, dentro da requisição, com qual nome ele pretendia falar.

## 5. URL

Uma **URL** (*Uniform Resource Locator*) é uma cadeia de caracteres com estrutura
definida que identifica onde e como um recurso será acessado.

```
https://usuario@www.example.com:8080/produtos/lista.php?cat=livros&pag=2#topo
\___/   \_____/ \_____________/ \__/\__________________/\______________/\___/
  |        |           |          |          |                 |          |
esquema  userinfo     host      porta      caminho          consulta   fragmento
```

| Parte | Descrição |
|---|---|
| **esquema** | O protocolo a usar: `http`, `https`, `mailto`, `file`. Determina tudo o que vem depois. |
| **userinfo** | Credencial embutida. Praticamente em desuso e desencorajado por segurança. |
| **host** | Nome de domínio ou endereço IP do servidor. |
| **porta** | Número da porta TCP. Se omitida, vale 80 para `http` e 443 para `https`. |
| **caminho** | Identifica o recurso dentro do servidor. |
| **consulta** (*query string*) | Pares `chave=valor` separados por `&`, iniciados por `?`. É o que o PHP lerá em `$_GET`. |
| **fragmento** | Iniciado por `#`. Identifica uma parte interna do documento. |

Dois pontos que costumam gerar confusão:

**O fragmento nunca é enviado ao servidor.** Ele é processado inteiramente pelo
navegador, que usa esse valor para rolar a página até o elemento correspondente.
O servidor jamais toma conhecimento dele. Isso será demonstrado na parte prática.

**Caracteres fora do conjunto permitido precisam ser codificados.** Espaços,
acentos e os próprios caracteres `?`, `&` e `#` não podem aparecer cruamente numa
URL. Eles são substituídos por `%` seguido do valor hexadecimal de cada byte na
codificação UTF-8. Essa é a **codificação percentual**:

| Caractere | Codificado |
|---|---|
| espaço | `%20` |
| `á` | `%C3%A1` |
| `&` | `%26` |
| `#` | `%23` |

Assim, a busca por `análise e projeto` vira `q=an%C3%A1lise%20e%20projeto`.

> **URI, URL e URN.** URI é o termo geral para identificador de recurso. URL é a
> URI que informa **onde** o recurso está e **como** obtê-lo. URN é a URI que
> apenas **nomeia** o recurso, sem dizer onde encontrá-lo (por exemplo,
> `urn:isbn:0451450523`). Na prática cotidiana, os dois primeiros termos são
> usados como sinônimos.

## 6. Passagem de parâmetros

Até aqui o cliente só soube dizer "me entregue o recurso deste caminho". Uma
aplicação web precisa de mais: o termo digitado na busca, a página da listagem
que se quer ver, o login e a senha, os campos de um cadastro. Sem o cliente
enviar dados, não existe aplicação, apenas um servidor de arquivos.

A mensagem HTTP tem três lugares onde esses dados cabem:

| Lugar | Exemplo | Onde fica na mensagem |
|---|---|---|
| Caminho | `/produtos/42` | linha inicial |
| Consulta (*query string*) | `/busca?q=teclado` | linha inicial, após o `?` |
| Corpo | `q=teclado` | depois da linha em branco |

Cabeçalhos também transportam dados, como `Cookie` e `Authorization`, mas são
metadados da requisição, e não parâmetros da operação.

### Consulta: parâmetros na própria URL

```
/busca?q=teclado&pag=2&ordem=preco
```

As regras já foram vistas na seção sobre URL, e valem repetir juntas:

* a consulta começa no primeiro `?`;
* cada parâmetro é um par `chave=valor`;
* os pares são separados por `&`;
* caracteres especiais vão codificados: `q=ar%20condicionado`.

O servidor recebe essa cadeia de caracteres e a converte numa estrutura de chave
e valor. Em PHP, essa estrutura se chama `$_GET`:

```php
$_GET['q']      // "teclado"
$_GET['pag']    // "2"
```

### Corpo: os mesmos pares, em outro lugar

```http
POST /busca HTTP/1.1
Host: loja.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

q=teclado&pag=2
```

A gramática é **exatamente a mesma** da consulta, só que depois da linha em
branco. O nome do formato diz isso: `x-www-form-urlencoded` significa
"codificado como numa URL". Em PHP muda apenas o nome da variável, para
`$_POST`.

### Quem gera isso na prática

Você raramente monta a URL à mão. Quem monta é o formulário HTML:

```html
<form action="/busca" method="get">
  <input name="q">
  <input name="pag">
</form>
```

Com `method="get"`, o navegador coloca os campos na consulta, produzindo
`/busca?q=teclado&pag=2`. Com `method="post"`, coloca os mesmos campos no corpo.
O formulário é o mesmo; muda o lugar por onde os dados viajam.

Resta a pergunta que a próxima seção responde: se os dois transportam os mesmos
dados, qual escolher, e por quê?

## 7. Métodos

O método é o verbo da requisição. Os mais frequentes:

| Método | Significado |
|---|---|
| `GET` | Solicita a representação de um recurso. |
| `POST` | Envia dados para serem processados pelo recurso indicado. |
| `PUT` | Substitui integralmente o recurso pelo conteúdo enviado. |
| `DELETE` | Remove o recurso. |
| `HEAD` | Igual ao `GET`, mas o servidor devolve apenas os cabeçalhos, sem o corpo. |
| `OPTIONS` | Pergunta quais métodos o servidor aceita para aquele recurso. |
| `PATCH` | Altera parcialmente o recurso. |

### GET e POST não se distinguem pelo lugar dos dados

É comum ouvir que "GET manda os dados pela URL e POST manda pelo corpo". Isso
descreve o efeito, e não a causa, e leva a decisões erradas de projeto. A
distinção que importa está em duas propriedades definidas na RFC 9110.

**Método seguro** (*safe*): não altera o estado do servidor. É apenas leitura.
`GET` e `HEAD` são seguros. `POST`, `PUT`, `DELETE` e `PATCH` não são.

**Método idempotente**: executá-lo uma vez ou N vezes seguidas produz o mesmo
estado final no servidor. `GET`, `HEAD`, `PUT` e `DELETE` são idempotentes.
`POST` não é.

| Método | Seguro | Idempotente |
|---|---|---|
| `GET` | sim | sim |
| `HEAD` | sim | sim |
| `PUT` | não | sim |
| `DELETE` | não | sim |
| `POST` | não | não |

`DELETE` é idempotente embora não seja seguro: apagar o mesmo recurso duas vezes
deixa o servidor no mesmo estado das duas vezes, ainda que a segunda resposta
seja `404`. `POST` não é idempotente porque enviar duas vezes o formulário de
compra cria duas compras.

Tudo o que o navegador faz decorre dessas propriedades:

* Ele pode buscar antecipadamente um link, porque `GET` é seguro e não causa dano;
* Ele recarrega um `GET` sem perguntar nada, e exige confirmação antes de reenviar
  um `POST`;
* Servidores intermediários (*proxies*) e o cache do navegador armazenam respostas
  de `GET`, e nunca de `POST`;
* O histórico do navegador guarda a URL, portanto guarda os dados de um `GET`.

Consequência prática: uma senha, mesmo em conexão HTTPS, não deve viajar por
`GET`. Ela ficaria no histórico do navegador e nos registros de acesso do
servidor. E um link que apaga um registro do banco não deve ser um `GET`, porque
qualquer mecanismo que percorra links da página o executaria sem intenção.

## 8. Códigos de status

O primeiro dígito define a classe:

| Classe | Significado |
|---|---|
| `1xx` | Informativo. Processamento em andamento. |
| `2xx` | Sucesso. |
| `3xx` | Redirecionamento. O cliente precisa de uma ação adicional. |
| `4xx` | Erro do cliente. A requisição está errada. |
| `5xx` | Erro do servidor. A requisição estava certa, o servidor falhou. |

A fronteira entre `4xx` e `5xx` é uma atribuição de culpa. Ao depurar um sistema,
`4xx` manda você olhar o código que fez a requisição, `5xx` manda olhar o
servidor.

Os que interessam nesta disciplina:

| Código | Nome | Quando ocorre |
|---|---|---|
| `200` | OK | Sucesso, com corpo na resposta. |
| `201` | Created | Recurso criado. Acompanha o cabeçalho `Location` com a URL do recurso novo. |
| `204` | No Content | Sucesso, sem corpo. A resposta não pode ter corpo. |
| `301` | Moved Permanently | Mudou de endereço definitivamente. O navegador memoriza e passa a ir direto ao novo endereço. |
| `302` | Found | Mudou temporariamente. O navegador não memoriza. |
| `304` | Not Modified | O recurso não mudou desde a última vez. Resposta sem corpo. Ver seção de cache. |
| `400` | Bad Request | A requisição está malformada e o servidor não consegue interpretá-la. |
| `401` | Unauthorized | Falta autenticação. Acompanha o cabeçalho `WWW-Authenticate`. |
| `403` | Forbidden | O servidor entendeu e sabe quem você é, mas recusa. |
| `404` | Not Found | O recurso não existe naquele caminho. |
| `405` | Method Not Allowed | O recurso existe, o método não é aceito. Acompanha `Allow`. |
| `500` | Internal Server Error | Falha no servidor. Em PHP, geralmente um erro fatal no script. |

Duas distinções cobradas com frequência:

* `401` contra `403`: o primeiro significa "identifique-se", o segundo significa
  "já sei quem você é e ainda assim não pode".
* `301` contra `302`: o `301` é memorizado pelo navegador de forma persistente.
  Publicar um `301` errado é difícil de reverter, porque os navegadores dos
  usuários continuarão indo ao endereço antigo mesmo depois da correção.

## 9. Cabeçalhos

Cabeçalhos são pares `Nome: valor` que carregam os metadados da mensagem. O nome
não diferencia maiúsculas de minúsculas. Existem centenas; os relevantes agora:

**Enviados pelo cliente**

| Cabeçalho | Função |
|---|---|
| `Host` | Nome do site desejado. Obrigatório. |
| `User-Agent` | Identificação do programa cliente. |
| `Accept` | Tipos de conteúdo que o cliente sabe processar. |
| `Accept-Language` | Idiomas preferidos. |
| `Accept-Encoding` | Compressões suportadas (`gzip`, `br`). |
| `Content-Type` | Formato do corpo, quando a requisição tem corpo. |
| `Content-Length` | Tamanho do corpo em bytes. |
| `Cookie` | Dados devolvidos ao servidor. Ver seção 8. |
| `Authorization` | Credenciais de acesso. |

**Enviados pelo servidor**

| Cabeçalho | Função |
|---|---|
| `Content-Type` | Formato do corpo da resposta. |
| `Content-Length` | Tamanho do corpo em bytes. |
| `Location` | Endereço de destino em respostas `3xx`, ou do recurso criado em `201`. |
| `Set-Cookie` | Pede ao cliente que armazene um dado. |
| `Cache-Control` | Regras de armazenamento em cache. |
| `ETag` | Identificador da versão atual do recurso. |
| `Server` | Identificação do software servidor. |

### Negociação de conteúdo

Os cabeçalhos `Accept*` permitem que cliente e servidor combinem o formato da
resposta. O cliente declara o que aceita, o servidor escolhe entre o que sabe
produzir. A mesma URL pode devolver HTML para um navegador e JSON para um
programa, dependendo do `Accept` enviado.

### Content-Type e charset

`Content-Type` informa como interpretar os bytes do corpo. Os valores seguem o
padrão MIME:

| Valor | Conteúdo |
|---|---|
| `text/html` | Documento HTML |
| `text/css` | Folha de estilo |
| `application/javascript` | Script |
| `application/json` | Dados em JSON |
| `image/png` | Imagem PNG |
| `application/x-www-form-urlencoded` | Formulário HTML padrão |
| `multipart/form-data` | Formulário com upload de arquivo |

O parâmetro `charset` indica a codificação de caracteres. `Content-Type:
text/html; charset=UTF-8` diz que os bytes devem ser lidos como UTF-8. Um
`charset` errado ou ausente é a causa mais comum de acentos aparecerem como
`Ã§Ã£o` na tela. O problema quase nunca está no arquivo: está na declaração.

## 10. Onde a mensagem termina

Pergunta que parece trivial e não é: recebida a última linha de cabeçalho, como o
cliente sabe em que ponto a resposta acabou? O TCP entrega um fluxo contínuo de
bytes e não sinaliza fronteiras de mensagem.

O HTTP resolve isso de três formas, todas em uso:

**1. `Content-Length`.** O servidor informa o tamanho exato do corpo em bytes e o
cliente conta. Exige que o servidor conheça o tamanho total antes de começar a
enviar.

**2. `Transfer-Encoding: chunked`.** O corpo é enviado em blocos, cada um
precedido pelo seu tamanho em hexadecimal, e o fim é marcado por um bloco de
tamanho zero. Permite ao servidor começar a responder antes de saber o tamanho
final, o que é necessário quando a resposta é gerada dinamicamente. É a base do
envio contínuo de dados (*streaming*).

```
HTTP/1.1 200 OK
Transfer-Encoding: chunked

7
Ola mun
3
do!
0

```

Os números `7` e `3` são os tamanhos dos blocos, em bytes, escritos em
hexadecimal. O bloco de tamanho `0` encerra a mensagem. O corpo reconstruído é
`Ola mundo`.

**3. Fechamento da conexão.** O servidor envia `Connection: close`, transmite o
corpo e encerra a conexão TCP. O fim da conexão é o fim da mensagem. Era o
mecanismo do HTTP/1.0 e é ineficiente, porque impede reaproveitar a conexão.

Este é o mecanismo por trás de dois sintomas comuns: uma requisição que fica
pendurada indefinidamente (o cliente ainda espera bytes prometidos por um
`Content-Length` maior que o corpo real) e uma resposta truncada.

## 11. Protocolo sem estado

O HTTP é **sem estado** (*stateless*): cada requisição é interpretada de forma
isolada, e o servidor não guarda nenhuma lembrança das requisições anteriores.
Duas requisições do mesmo usuário, com um segundo de intervalo, são para o
servidor dois eventos sem relação entre si.

Isso não é limitação acidental, é decisão de projeto. Sem estado, qualquer
servidor da fazenda pode atender qualquer requisição, e derrubar um servidor não
derruba as sessões em curso. É o que permitiu a web escalar.

O preço é evidente: se o servidor não se lembra de nada, como existe "usuário
logado"? A única saída é o **cliente reenviar**, em toda requisição, alguma
informação que permita ao servidor reconstruir o contexto.

Os mecanismos para isso:

**Cookies.** O servidor responde com `Set-Cookie: nome=valor`. O navegador
armazena o par e passa a incluí-lo, em toda requisição subsequente para aquele
domínio, no cabeçalho `Cookie: nome=valor`. O cookie é apenas texto trafegando em
cabeçalho, e o navegador o devolve automaticamente.

**Sessões.** O cookie guarda apenas um identificador aleatório; os dados ficam no
servidor, associados a esse identificador. É o modelo que usaremos com PHP na
Aula 14.

**Tokens.** Uma credencial assinada, enviada no cabeçalho `Authorization`, comum
em APIs.

Todos resolvem o mesmo problema pela mesma via: **fazer o cliente carregar o
contexto**, porque o protocolo não o carrega.

## 12. Conexões e versões

**HTTP/1.0.** Uma conexão TCP por recurso. Uma página com trinta imagens exigia
trinta conexões, cada uma com o custo de abertura e fechamento.

**HTTP/1.1.** Introduziu **conexões persistentes**: a conexão TCP permanece aberta
e várias requisições são feitas em sequência por ela. Persiste um limite: as
requisições são atendidas em ordem, e uma resposta lenta bloqueia as seguintes na
mesma conexão. Esse fenômeno chama-se **bloqueio de cabeça de fila**
(*head-of-line blocking*). Os navegadores contornavam abrindo em torno de seis
conexões paralelas por domínio.

**HTTP/2** (2015). Mensagens deixam de ser texto e passam a ser **binárias**,
organizadas em quadros. Traz **multiplexação**: várias requisições e respostas
trafegam simultaneamente e intercaladas na mesma conexão, eliminando o bloqueio
no nível do HTTP. Comprime cabeçalhos com HPACK, o que importa porque cabeçalhos
repetitivos são enviados em toda requisição.

**HTTP/3** (2022). Substitui o TCP pelo **QUIC**, que roda sobre UDP e implementa
confiabilidade por conta própria. Elimina o bloqueio de cabeça de fila que
persistia na camada TCP, e funde o estabelecimento de conexão com o handshake
criptográfico, reduzindo o número de idas e vindas até o primeiro byte.

A **semântica** definida na RFC 9110 (métodos, status, cabeçalhos) é a mesma nas
três versões. O que muda é a codificação e o transporte. Por isso o que você
aprende aqui vale para todas.

> Estudamos HTTP/1.1 em detalhe porque ele é texto e pode ser digitado à mão. A
> versão 2 é a mesma conversa, em formato ilegível para humanos.

## 13. HTTPS

**HTTPS** é HTTP transportado dentro de um túnel **TLS** (*Transport Layer
Security*, sucessor do SSL). O protocolo HTTP não muda: as mesmas mensagens de
texto trafegam, agora cifradas.

Ao abrir a conexão, cliente e servidor executam um *handshake*: negociam
algoritmos, o servidor apresenta seu **certificado digital**, e ambos derivam uma
chave de sessão usada para cifrar o restante da conversa.

**O que o TLS garante:**

* **Confidencialidade**: quem observa a rede não lê o conteúdo das mensagens,
  incluindo caminho da URL, cabeçalhos, cookies e corpo;
* **Integridade**: alterações no tráfego são detectadas;
* **Autenticação do servidor**: o certificado, assinado por uma autoridade
  certificadora reconhecida pelo navegador, comprova que o servidor controla
  aquele nome de domínio.

**O que o TLS não garante:**

* Não esconde **com quem** você fala. O endereço IP de destino é visível, e o nome
  do host viaja em claro no handshake (campo SNI);
* Não atesta idoneidade. Certificados são gratuitos e obtidos em minutos. Um site
  fraudulento tem cadeado igual ao de um banco. O cadeado prova identidade de
  domínio, e nada além disso;
* Não protege o dado depois que ele chega. Segurança do banco de dados, das
  senhas e do código do servidor são problemas separados.

**Conteúdo misto** (*mixed content*): se uma página servida por HTTPS carrega um
script por HTTP, um atacante pode substituir esse script e controlar a página
inteira. A garantia da página é anulada pelo elo mais fraco, e por isso os
navegadores bloqueiam a carga.

## 14. Origem e a política de mesma origem

**Origem** é a tripla **esquema + host + porta**. Duas URLs são da mesma origem
somente se as três coincidirem:

| URL A | URL B | Mesma origem? |
|---|---|---|
| `https://site.com/a` | `https://site.com/b/c` | sim, o caminho não conta |
| `http://site.com` | `https://site.com` | não, esquema difere |
| `https://site.com` | `https://api.site.com` | não, host difere |
| `http://site.com` | `http://site.com:8080` | não, porta difere |

A **política de mesma origem** (*same-origin policy*) é uma regra imposta pelo
navegador: JavaScript executando numa origem não pode ler a resposta de uma
requisição feita a outra origem. Sem essa regra, uma página maliciosa aberta numa
aba faria requisições ao seu banco em outra aba, aproveitando os cookies já
armazenados, e leria o resultado.

**CORS** (*Cross-Origin Resource Sharing*) é o mecanismo pelo qual o servidor de
destino autoriza explicitamente essa leitura, respondendo com o cabeçalho
`Access-Control-Allow-Origin`. Para requisições que possam alterar dados, o
navegador antes envia uma requisição de verificação prévia com o método
`OPTIONS`, e só prossegue se autorizado.

Dois pontos importantes:

1. A restrição é do **navegador**, não do protocolo. O `curl` acessa qualquer
   origem sem obstáculo, porque não implementa essa política. Isso explica por que
   uma URL funciona no terminal e falha no console do navegador.
2. Quem autoriza é o **servidor de destino**. Não há nada que você possa escrever
   no seu JavaScript para contornar a recusa.

Voltaremos a isso na Aula 07, ao consumir APIs com a Fetch API.

## 15. Cache e requisições condicionais

Rebaixar tráfego desnecessário é papel do cache. O HTTP oferece dois níveis.

**Cache por prazo.** O servidor responde com `Cache-Control: max-age=3600`,
declarando que a resposta vale por 3600 segundos. Durante esse período o
navegador usa a cópia local e **não faz requisição nenhuma**.

**Requisição condicional.** Vencido o prazo, o cliente não precisa baixar tudo de
novo: pergunta se mudou. Para isso ele guardou um identificador de versão vindo
do servidor:

* `ETag: "abc123"`, uma etiqueta opaca que identifica a versão do recurso. O
  cliente reenvia em `If-None-Match: "abc123"`;
* `Last-Modified: <data>`, a data da última alteração. O cliente reenvia em
  `If-Modified-Since: <data>`.

Se o recurso não mudou, o servidor responde `304 Not Modified`, **sem corpo**. A
economia é o corpo inteiro; apenas os cabeçalhos trafegam.

Valores úteis de `Cache-Control`:

| Valor | Efeito |
|---|---|
| `max-age=N` | Válido por N segundos |
| `no-cache` | Pode armazenar, mas precisa revalidar antes de usar |
| `no-store` | Proibido armazenar. Usado em páginas com dados sensíveis |
| `private` | Só o navegador pode guardar, servidores intermediários não |

Este mecanismo explica um problema que você encontrará na Aula 05: você altera o
arquivo CSS, recarrega a página, e o navegador continua exibindo o estilo antigo.
Ele está dentro do prazo declarado pelo servidor e sequer perguntou. A combinação
`Ctrl+Shift+R` força o recarregamento ignorando o cache.

---

# Parte prática

Nesta parte você vai construir requisições HTTP manualmente e, em seguida, atuar
como servidor. Registre os comandos e as saídas: eles compõem a entrega do dia.

## 16. Preparando o ambiente

A ferramenta principal é o **`curl`**, um cliente HTTP de linha de comando.

* **Linux e macOS**: já instalado.
* **Windows 10 e 11**: já instalado como `curl.exe`.

> **Atenção, usuários de Windows.** No PowerShell, o nome `curl` é um apelido para
> o comando `Invoke-WebRequest`, que tem sintaxe completamente diferente. Se
> estiver no PowerShell, escreva `curl.exe` com a extensão. No `cmd` e no Git
> Bash, `curl` funciona normalmente. Recomenda-se usar o **Git Bash**, já
> instalado na Aula 01.

Verifique com:

```bash
curl --version
```

## 17. Ver a conversa completa, agora com nome nas coisas

Retome o comando do primeiro contato:

```bash
curl -v https://example.com
```

Você já sabe que `>` é o que foi enviado e `<` o que foi recebido. Agora
identifique, uma a uma: a resolução do nome pelo DNS, o handshake TLS, a linha
de requisição com método, alvo e versão, os cabeçalhos enviados, a linha de
status, os cabeçalhos de resposta, a linha em branco e o corpo.

Cada um desses itens foi uma seção da parte teórica.

## 18. Somente os cabeçalhos

```bash
curl -s -o /dev/null -D - https://developer.mozilla.org
```

* `-s` silencia a barra de progresso;
* `-o /dev/null` descarta o corpo;
* `-D -` escreve os cabeçalhos de resposta na saída padrão.

A opção `-I` faz algo parecido, porém enviando o método `HEAD` em vez de `GET`:

```bash
curl -I https://developer.mozilla.org
```

A diferença importa: são requisições distintas, e alguns servidores respondem
diferente a cada uma.

## 19. Enviar dados

Formulário HTML tradicional. A opção `-d` envia um corpo e, sozinha, define o
método como `POST` e o cabeçalho `Content-Type:
application/x-www-form-urlencoded`:

```bash
curl -v -d "nome=Ana&curso=TADS" https://postman-echo.com/post
```

Este é exatamente o formato que o PHP lerá em `$_POST` na Aula 12.

> Os endereços `postman-echo.com` e `httpbin.org` são serviços públicos que
> devolvem uma descrição da requisição recebida. Dependem de disponibilidade
> externa. Se estiverem fora do ar, uma alternativa é `httpbingo.org`.

## 20. Demonstrar a ausência de estado

Execute os quatro comandos na ordem:

```bash
curl -s -o /dev/null -c jar.txt "https://postman-echo.com/cookies/set?turma=ds122"
cat jar.txt
curl -s -b jar.txt https://postman-echo.com/cookies
curl -s https://postman-echo.com/cookies
```

* `-c jar.txt` grava em arquivo os cookies recebidos, fazendo o papel do
  armazenamento do navegador;
* `-b jar.txt` reenvia esses cookies na requisição.

O terceiro comando devolve `turma: ds122`; o quarto, idêntico exceto pela
ausência de `-b`, devolve vazio. O servidor não guardou nada. Quem carrega o
estado é o cliente.

Duas observações sobre a saída real. O primeiro comando responde `302`, porque
o serviço redireciona depois de gravar o cookie. E o arquivo `jar.txt` conterá,
além do cookie que você criou, outros cookies definidos pela infraestrutura do
site. Cada linha do arquivo traz domínio, caminho, exigência de HTTPS, data de
validade, nome e valor.

## 21. Ser o servidor

Até aqui você foi o cliente. Agora inverta os papéis, usando o `nc` (*netcat*),
um utilitário que abre conexões TCP brutas. A sintaxe varia conforme a versão
instalada:

```bash
nc -l 8080          # macOS, OpenBSD
nc -l -p 8080       # GNU netcat
ncat -l 8080        # Fedora e derivados (pacote nmap-ncat)
```

Com o comando rodando e o terminal aparentemente travado, abra no navegador:

```
http://localhost:8080/pagina?a=1&b=2#secao
```

A requisição que o navegador montou aparece no terminal. Três observações:

**1.** O navegador envia muito mais cabeçalhos do que o `curl`: `User-Agent`,
`Accept`, `Accept-Encoding`, `Accept-Language`, `Sec-Fetch-*` e outros. Compare
com a saída do `curl -v`.

**2.** A consulta `?a=1&b=2` aparece na linha de requisição. O fragmento `#secao`
**não aparece em lugar nenhum**, confirmando que ele nunca sai do navegador.

**3.** O navegador continua carregando, porque ninguém respondeu. Digite no
terminal do `nc` a resposta abaixo, pressione `Enter` duas vezes após a linha
`Content-Length` e observe a página renderizar:

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 38

<h1>Resposta digitada byte a byte</h1>
```

Confira a contagem: `<h1>` tem 4 bytes, o texto tem 29 e `</h1>` tem 5,
totalizando 38. Se você errar esse número para mais, o navegador ficará
aguardando bytes que nunca chegam.

Encerre com `Ctrl-C`. Você acabou de operar como servidor web.

> Se o `nc` não estiver disponível, `python3 -m http.server 8080` registra as
> requisições recebidas, embora sem exibir os cabeçalhos.

## 22. Ferramentas do navegador

Todo navegador oferece ferramentas de desenvolvimento, acessíveis por
`Ctrl+Shift+I` ou `F12`. A aba **Rede** (*Network*) mostra cada requisição da
página, com cabeçalhos, corpo, status e tempo.

Recomendados: **Firefox** e navegadores baseados em **Chromium** (Chrome,
Edge, Brave, Vivaldi).

Ponte entre as duas ferramentas: na aba Rede, clique com o botão direito sobre
uma requisição e escolha **Copiar como cURL**. Cole o resultado no terminal e
execute. Você obterá a mesma resposta, e verá que navegador e `curl` falam
exatamente o mesmo protocolo. A diferença está na quantidade de cabeçalhos.

---

## 23. Mais exemplos com curl

Os comandos desta seção não são vistos em aula por falta de tempo. Valem como
aprofundamento e são necessários para dois dos exercícios.

### Ver os bytes literais

```bash
curl --trace-ascii - https://example.com | head -40
```

### Montar a requisição com cabeçalhos próprios

A opção `-H` acrescenta um cabeçalho à requisição. Compare o mesmo dado em dois
formatos:

```bash
curl -s https://viacep.com.br/ws/80060000/json/
curl -s https://viacep.com.br/ws/80060000/xml/
```

Verifique o `Content-Type` de cada resposta com `-D -`. Note que este servidor
escolhe o formato pelo **caminho da URL**, e não pelo cabeçalho `Accept`. Enviar
`Accept: application/xml` para o caminho `/json/` não muda nada. Negociação de
conteúdo é um recurso do protocolo, e cabe a cada servidor implementá-lo ou não.

Um servidor que de fato negocia, pelo idioma:

```bash
curl -s -H "Accept-Language: ja" https://www.google.com | head -c 300
curl -s -H "Accept-Language: pt-BR" https://www.google.com | head -c 300
```

Altere a identificação do cliente:

```bash
curl -v -A "DS122-bot/1.0" https://example.com -o /dev/null
```

### Redirecionamentos

```bash
curl -s -o /dev/null -w "%{response_code}\n" http://ufpr.br
```

A opção `-w` (*write-out*) imprime informações sobre a requisição depois de
concluída. O código obtido explica por que tentar HTTP simples em sites reais é
frustrante: a resposta é um redirecionamento, e não o conteúdo.

Para ver a cadeia inteira e segui-la:

```bash
curl -sIL http://ufpr.br | grep -E "^HTTP|^[Ll]ocation"
curl -s -o /dev/null -L -w "%{num_redirects} saltos, destino: %{url_effective}\n" http://ufpr.br
```

A opção `-L` faz o `curl` seguir os redirecionamentos, comportamento que o
navegador tem por padrão.

### Enviar JSON, e enviar por GET

Para enviar JSON é preciso declarar o tipo, porque o padrão do `curl` é o formato
de formulário:

```bash
curl -v -H "Content-Type: application/json" -d '{"nome":"Ana","curso":"TADS"}' https://postman-echo.com/post
```

Para enviar por `GET`, com codificação percentual automática:

```bash
curl -v -G --data-urlencode "q=análise e desenvolvimento" https://postman-echo.com/get
```

Observe na saída como o espaço virou `%20` e o `á` virou `%C3%A1`.

### Cache condicional

Obtenha o identificador de versão de um recurso:

```bash
curl -sI https://developer.mozilla.org/favicon.ico | grep -iE "etag|last-modified|cache-control"
```

Copie o valor de `ETag`, incluindo as aspas, e reenvie:

```bash
curl -s -o /dev/null -w "código: %{response_code}  bytes: %{size_download}\n" \
     -H 'If-None-Match: "COLE_O_ETAG_AQUI"' \
     https://developer.mozilla.org/favicon.ico
```

O resultado deve ser código `304` e zero bytes de corpo.

### Versões e fases da conexão

Descubra qual versão do protocolo está em uso, e force a versão 1.1 para comparar:

```bash
curl -s -o /dev/null -w "versão: %{http_version}  código: %{response_code}\n" https://www.cloudflare.com
curl -s -o /dev/null --http1.1 -w "versão: %{http_version}\n" https://www.cloudflare.com
```

Meça o tempo de cada fase da conexão:

```bash
curl -s -o /dev/null -w "DNS:       %{time_namelookup}s
TCP:       %{time_connect}s
TLS:       %{time_appconnect}s
1o byte:   %{time_starttransfer}s
total:     %{time_total}s
" https://www.ufpr.br
```

Os valores são acumulados desde o início. A diferença entre `TCP` e `DNS` é o
tempo do handshake TCP; entre `TLS` e `TCP`, o custo da criptografia; entre
`1o byte` e `TLS`, o tempo que o servidor levou para processar a requisição.

## 24. Exercícios

**Não há entrega nem nota nesta aula.** Os exercícios abaixo são de fixação e
devem ser feitos ao longo da semana, no seu ritmo.

Ainda assim, faça-os. O conteúdo desta aula cai na Prova 1, e digitar os
comandos é a única forma de fixar o que foi visto aqui. Sugestão de método: para
cada item, anote o comando usado, o trecho relevante da saída e a resposta da
pergunta, num arquivo seu. Na semana da prova, esse arquivo vira resumo.

Dúvidas na aula seguinte ou no fórum da UFPR Virtual.

**1.** Escolha três sites de sua preferência. Para cada um, registre o código de
status, a versão do HTTP em uso e o valor do cabeçalho `Server`, quando presente.
Explique as diferenças observadas entre eles.

**2.** Encontre uma URL que responda `301` e outra que responda `404`. Para a
primeira, mostre a cadeia completa de redirecionamento até o destino final e
informe quantos saltos foram necessários.

**3.** Envie o par de dados `nome=SeuNome&periodo=2` por `GET` e por `POST` para
um serviço de eco. Mostre onde os dados aparecem em cada caso. Explique por que a
URL do `GET` fica no histórico do navegador e a do `POST` não, e relacione isso
com as propriedades de segurança e idempotência dos métodos.

**4.** Demonstre experimentalmente que o HTTP não guarda estado, usando as opções
`-c` e `-b`. Descreva, em no máximo três frases, o que o servidor recebeu em cada
uma das requisições.

**5.** (usa a seção *Mais exemplos com curl*) Obtenha o `ETag` de um recurso qualquer, reenvie-o em `If-None-Match` e
registre o código de status e o tamanho do corpo da segunda resposta. Explique
que economia o mecanismo produziu.

**6.** Execute o `nc` como servidor, acesse
`http://localhost:8080/teste?x=1#topo` pelo navegador e cole a requisição
recebida. Responda: por que `#topo` não aparece na requisição, e quem processa
esse valor?

**7.** (usa a seção *Mais exemplos com curl*) Use a opção `-w` para medir as fases da conexão com um site brasileiro e
com um site hospedado fora do país. Compare os tempos de DNS, TCP e TLS e
proponha uma explicação para a diferença.

### Desafio

Para quem quiser ir além. Escreva um servidor HTTP mínimo, em qualquer linguagem, que abra uma porta TCP,
aceite uma conexão, leia a requisição recebida e devolva uma resposta HTTP válida
com `Content-Type` e `Content-Length` corretos. Não use biblioteca de servidor
web pronta: trabalhe diretamente com sockets. Em Python, cerca de vinte linhas
resolvem.

---

## Resumo

* HTTP é um acordo de formato entre cliente e servidor. O cliente sempre inicia.
* Toda mensagem tem linha inicial, cabeçalhos, linha em branco e corpo opcional.
* Métodos se distinguem por serem seguros e idempotentes, e é daí que decorre o
  comportamento do navegador, do cache e do histórico.
* Códigos de status separam sucesso, redirecionamento, culpa do cliente e culpa
  do servidor.
* O protocolo não guarda estado. Cookies, sessões e tokens existem para o cliente
  carregar o contexto a cada requisição.
* HTTPS acrescenta confidencialidade, integridade e autenticação do servidor, e
  nada mais.
* Origem é esquema, host e porta juntos. A restrição de mesma origem é do
  navegador, e o servidor de destino é quem autoriza a exceção.
