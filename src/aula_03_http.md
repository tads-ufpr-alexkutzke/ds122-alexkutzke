# Aula 03 - Protocolo HTTP

## Bibliografia recomendada para o tema:
* Cap 7.3 do livro do Tanembaum (ver plano de ensino no moodle);
* [MDN - HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP).

## Modelo Cliente-Servidor

Diferentemente do modelo de programação para desktop (em geral, com um único
ator), o modelo de programação *cliente-servidor* conta com dois atores:

* **Cliente**: Realiza **requisições**, uma a uma, e aguarda respostas;
* **Servidor**: Processa requisições e as **responde**, uma a uma.

## Protocolo HTTP

Na web, o protocolo que gerencia a troca de informações (requisições e respostas)
entre clientes e servidores é o HTTP (HyperText Transfer Protocol).

Segundo a Wikipedia<sup>1</sup>:

> O Hypertext Transfer Protocol (HTTP), em português Protocolo de Transferência de
> Hipertexto, é um protocolo de comunicação (na camada de aplicação segundo o
> Modelo OSI) utilizado para sistemas de informação de hipermídia, distribuídos e colaborativos.
> Ele é a base para a comunicação de dados da World Wide Web.
>
> Hipertexto é o texto estruturado que utiliza ligações lógicas (hiperlinks) entre
> nós contendo texto. O HTTP é o protocolo para a troca ou transferência de hipertexto.
>
> Coordenado pela World Wide Web Consortium e a Internet Engineering Task Force,
> culminou na publicação de uma série de Requests for Comments; mais notavelmente
> o RFC 2616, de junho de 1999, que definiu o HTTP/1.1. Em Junho de 2014 foram
> publicados 6 RFC's para maior clareza do protocolo HTTP/1.1. Em Março de 2015,
> foi divulgado o lançamento do HTTP/2.

RFC's (*Requests for comments*) são a forma oficial de como os protocolos, regras,
especificações e padrões de funcionamento da web são divulgados.

O criador do protocolo HTTP foi Tim Berners-Lee.

## Funcionamento do Protocolo HTTP

![Modelo Cliente-Servidor](images/http/http.gif)

## Uniform Resource Locator (URL)

URL's são a forma utilizada para se localizar serviços na Web. Trata-se de uma
sequência de caracteres, com estrutura definida, capaz de identificar onde e como
uma dado serviço será acessado.

Para mais detalhes e exemplo, consulte: https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web

Interessado? Pesquise sobre a diferença entre URL, URN  e URI (pode começar
por [aqui](https://developer.mozilla.org/en-US/docs/Glossary/URI))

## Exemplo de mensagem HTTP

Uma requisição:

```http
GET /index.html HTTP/1.1
Host: www.example.com
```

Uma resposta:

```http
HTTP/1.1 200 OK
Date: Mon, 23 May 2005 22:38:34 GMT
Content-Type: text/html; charset=UTF-8
Content-Encoding: UTF-8
Content-Length: 138
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
Server: Apache/1.3.3.7 (Unix) (Red-Hat/Linux)
ETag: "3f80f-1b6-3e1cb03b"
Accept-Ranges: bytes
Connection: close

<html>
<head>
  <title>An Example Page</title>
</head>
<body>
  Hello World, this is a very simple HTML document.
</body>
</html>
```

Toda mensagem HTTP recebe um código como resposta. Para uma breve lista deles,
consulte: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status

O protocolo possui diferentes métodos, sendo os mais conhecidos:

* GET: parâmetros enviados diretamente pela URL;
* POST: parâmetros enviados no corpo da mensagem.

Sobre todos os métodos e parâmetros possíveis em um cabeçalho HTTP, consulte:
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

Para realizar um teste de uma mensagem HTTP, é possível utilizar os comandos
`telnet` e `nc` (netcat). A seguir, um exemplo com `nc` é descrito.

Em ambientes Unix e GNU/Linux, é provável que o comando `nc` já esteja instalado.

Em sistemas Windows, é possível utilizar a maquina virtual com vagrant ou instalar o comando `nc`.

### Instalando o nc no Windows

Baixe o seguinte arquivo:

https://joncraton.org/files/nc111nt.zip

Descompacte o arquivo `nc.exe` em uma pasta de fácil acesso. **A senha para descompactar é "nc"**.


Agora, basta acessar pelo terminal (gitbash) a pasta onde o arquivo foi descompactado e utilizar o comando `nc` para executar o programa.

### Exemplo de mensagem HTTP com nc

Para simular uma mensagem HTTP, é preciso informar ao `nc` qual o endereço queremos
acessar (do servidor) e qual a porta devemos tentar conexão. Em geral, servidores
de página WEB *escutam* na porta 80.

```bash
nc example.com 80
```

O camando ficará em modo de espera para digitarmos a mensagem a ser enviada.
Se prefirir, copie e cole o exemplo abaixo:

```http
GET / HTTP/1.1
Host: example.com
```

Como cabeçalho HTTP deve terminar com uma linha vazia, lembre-se de pressionar
`Enter` duas vezes. Para sair do comando `nc`, pressione `CTRL-D`.

Se desejar, é possível salvar a mensagem em um arquivo, digamos `teste.http` e
passar o conteúdo do arquivo diretamente ao `nc` com o seguinte comando:

```bash
cat teste.http - | nc example.com 80
```

## Caraterísticas importantes do HTTP

* Protocolo sem estado (*state-less*);
* Conexões persistentes a partir do HTTP 1.1;
* Possibilidade do uso de *Cookies*:
  * Enviados no cabeçalho da mensagem;
  * Servidor solicita o salvamento de pequenos conjuntos chaveXvalor no cliente;
  * Permeia questões de segurança.
* HTTPS:
  * TLS/SSL;
  * Mensagens criptografadas;
  * Certificação do cliente e do servidor.

## Ferramentas de desenvolvimento no seu navegador

Qualquer navegador Web decente, disponibiliza ferramentas de desenvolvimento.
Com elas, é possível explorar todos os detalhes envolvidos nas páginas exibidas
pelo navegador, como: HTML, CSS, Javascript, mensagens HTTP trocadas, console
de comandos, etc.

Explore as ferramentas no seu navegador. Em geral, o atalho `CTRL+SHIFT+I` ativa
tais ferramentas.

**O uso dos navegadores Internet Explorer e Microsoft Edge não são indicados
para essa disciplina.**

Navegadores indicados:
* Mozilla Firefox;
* Chromium e afins;
* Opera.

## Prática

Faça requisições HTTP para diferentes sites e analise as respostas obtidas. Utilize 
algumas das ferramentas abaixo:

- Seu navegador;
- Comandos como o nc;
- Extensões para navegadores;
- https://httpie.org/run

---
<sup>1</sup> Tenha sempre em mente que são raras as vezes em que a Wikipedia
pode ser utilizada como uma referência científica relevante.
