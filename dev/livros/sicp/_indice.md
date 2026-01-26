---
title: sicp
tags:
  - estudos
updated-at: 11/02/2026 17:00
created-at: 17/11/2025 20:48
---

> Anotações sobre o [curso](https://ocw.mit.edu/courses/6-001-structure-and-interpretation-of-computer-programs-spring-2005/video_galleries/video-lectures/) e o [livro](https://web.mit.edu/6.001/6.037/sicp.pdf) _SICP — Structure and Interpretation of Computer Programs - 2nd Edition_.

> [!info]
> - Para o conteúdo, a linguagem escolhida foi **Scheme** (dialeto **Lisp**);
> - O terminal REPL (_Read-Eval-Print-Loop_) usado nos exemplos é o mesmo de https://try.scheme.org/;
> 	- O interpretador presente no REPL segue a implementação [Gambit](https://en.wikipedia.org/wiki/Gambit_(Scheme_implementation)) da linguagem Scheme. 
> - Nos blocos de código de exemplo:
> 	- Tudo após um cifrão seguido de um sinal de "maior que" (`$>`) é o que foi inserido no terminal;
> 	- Tudo após um sinal de "ponto e vírgula" (`;`) é um comentário contendo o valor de um resultado;
> 	- Tudo após um sinal de "ponto e vírgula" seguido de uma interrogação (`;?`) é um comentário contendo as etapas da operação realizada para se chegar no valor de um resultado;
> 	- Na primeira linha, tudo após um sinal de "ponto e vírgula" é um comentário contendo o nome de um arquivo que armazena o conteúdo.

> [!example] Tabela de conteúdos
> - [1. Construindo abstrações com funções](dev/livros/sicp/capitulo-1/_indice.md)
> 	- [Introdução](dev/livros/sicp/capitulo-1/_indice.md#Introdução)
> 	- [1.1. Elementos da programação.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.%20Elementos%20da%20programação)
> 		- [1.1.1. Expressões.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.1.%20Expressões)
> 		- [1.1.2. Nomeação e ambiente.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.2.%20Nomeação%20e%20ambiente)
> 		- [1.1.3. Avaliando combinações.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.3.%20Avaliando%20combinações)
> 		- [1.1.4. Funções complexas (compostas).](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.4.%20Funções%20complexas%20(compostas))
> 		- [1.1.5. Modelo de substituição para aplicação de funções.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.5.%20Modelo%20de%20substituição%20para%20aplicação%20de%20funções)
> 			- [Ordem aplicativa versus ordem normal.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Ordem%20aplicativa%20versus%20ordem%20normal)
> 		- [1.1.6. Expressões condicionais e predicados.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.6.%20Expressões%20condicionais%20e%20predicados)
> 		- [1.1.7. Exemplo: método de aproximação sucessiva de raiz quadrada.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.7.%20Exemplo%20método%20de%20aproximação%20sucessiva%20de%20raiz%20quadrada)
> 		- [1.1.8. Funções como abstrações de caixa-preta.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.8.%20Funções%20como%20abstrações%20de%20caixa-preta)
> 			- [Nomes locais.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Nomes%20locais)
> 			- [Definição interna e estrutura de blocos.](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Definição%20interna%20e%20estrutura%20de%20blocos)
> 	- [1.2. Funções e os processos que elas geram.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.%20Funções%20e%20os%20processos%20que%20elas%20geram)
> 		- [1.2.1. Recursão e iteração lineares.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.1.%20Recursão%20e%20iteração%20lineares)
> 		- [1.2.2. Recursão em árvore.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.2.%20Recursão%20em%20árvore)
> 		- [1.2.3. Ordens de crescimento.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.3.%20Ordens%20de%20crescimento)
> 		- [1.2.4. Exponenciação.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.4.%20Exponenciação)
> 		- [1.2.5. Máximo Divisor Comum.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.5.%20Máximo%20Divisor%20Comum)
> 		- [1.2.6. Exemplo: Testando Primalidade.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.6.%20Exemplo%20Testando%20Primalidade)
> 			- [Buscando divisores.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#Buscando%20divisores)
> 			- [O teste de Fermat.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#O%20teste%20de%20Fermat)
> 				- [Métodos probabilísticos.](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#Métodos%20probabilísticos)
