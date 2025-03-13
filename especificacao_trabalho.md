Tecnologia em Análise e Desenvolvimento de Sistemas

Setor de Educação Profissional e Tecnológica - SEPT

Universidade Federal do Paraná - UFPR

---

*DS122 - Desenvolvimento de Aplicações Web 1*

Prof. Alexander Robert Kutzke

# Especificação de Trabalho Prático

O trabalho prático envolve a criação de uma aplicação WEB completa. Ou seja,
que inclua a implementação de front-end, back-end e que possua integração com 
um banco de dados.

## Tema

O tema da aplicação a ser desenvolvida é livre. 

## Requisitos

A aplicação desenvolvida deve atender os seguintes requisitos:

 * **Front-end**:
  * Uso de HTML5, CSS3 e JS;
	* Interface amigável; ;
	* Validação de campos de formulário;
 * **Back-end**;
  * Integração com um banco de dados:
	  * Possuir, ao menos, duas tabelas com relacionamento entre si;
		* Modelagem consistente do banco de dados;
	* Criação, edição e remoção de itens do banco de dados;
	* Sistema de autenticação/autorização de usuário(s) salvo(s) em banco de dados;
  * Validação de campos de formulário e outras informações recebidas.

## Ambiente de Desenvolvimento

* O sistema deve ser desenvolvido utilizando **apenas** os recursos demonstrados
na disciplina DS122 (PHP, Javascript (JQuery), HTML5, CSS3 e algum banco de dados);
  * É permitido o uso de frameworks *front-end*, como Bootstrap e W3.CSS;
  * **Não** é permitido o uso de frameworks *back-end*.

## Entrega

**Datas de entrega e defesa no moodle**.

O trabalho pode ser feito em **grupos de 2 até 5 alunos**.

O código deve ser entregue através do moodle da disciplina, por meio de link para repositório git.

O trabalho deverá ser defendido através de uma rápida demonstração de seu funcionamento e explicação do código.
A defesa é realizada apenas para o professor, não para a turma.

## Documentação

O repositório deverá conter um arquivo chamado `README.md` com a descrição
do sistema e de seu funcionamento. Deve-se utilizar a sintaxe correta da
linguagem **Markdown** nesse documento (para saber mais, consulte: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

## Critério para avaliação

Os critérios para avaliação serão os seguintes:

 * **Defesa e conceitos** [4 pontos]:
    * Documentação e entrega [1 ponto]; 
    * Organização do banco de dados [1 ponto];
    * Estrutura e clareza do código [1 ponto]
    * Qualidade da defesa e domínio do código [1 ponto];

 * **Funcionalidades e implementação** [6 pontos]:
   * Sistema de autenticação (login) [1 ponto];
   * Cadastro, alteração e remoção de informações no banco de dados [1 ponto];
   * Qualidade da interface do usuário [1 ponto];
   * Validação das informações de formulários e afins [1 ponto];
	 * Funcionamento da aplicação e qualidade da implementação [2 pontos];

## Sugestão de tema

Uma possível aplicação a ser desenvolvida é a seguinte:

> Um blog simples no qual um *usuário administrador* pode inserir novos *posts*. Todos
> os *posts* possuem uma data de criação e de atualização, as quais são exibidas
> aos leitores. Cada *post* pertence à uma *categoria* e pode possuir zero ou mais
> *comentários*. Leitores podem visualizar os *posts* existentes em uma *categoria* e
> fazer comentários nos *posts*. Para melhorar a qualidade do blog, os *posts* escritos
> pelo administrador podem receber tags HTML em seu conteúdo.
