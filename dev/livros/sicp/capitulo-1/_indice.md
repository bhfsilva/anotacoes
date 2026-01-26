---
title: capitulo-1
updated-at: 12/02/2026 10:25
created-at: 01/12/2025 22:41
---

> O conteúdo se baseia no capítulo [1. Building Abstractions with Procedures Functions](https://web.mit.edu/6.001/6.037/sicp.pdf#chapter.1).

> [!example] Tabela de conteúdos
> - [Introdução](#Introdução)
> - [1.1. Elementos da programação](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.%20Elementos%20da%20programação)
> 	- [1.1.1. Expressões](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.1.%20Expressões)
> 	- [1.1.2. Nomeação e ambiente](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.2.%20Nomeação%20e%20ambiente)
> 	- [1.1.3. Avaliando combinações](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.3.%20Avaliando%20combinações)
> 	- [1.1.4. Funções complexas (compostas)](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.4.%20Funções%20complexas%20(compostas))
> 	- [1.1.5. Modelo de substituição para aplicação de funções](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.5.%20Modelo%20de%20substituição%20para%20aplicação%20de%20funções)
> 		- [Ordem aplicativa versus ordem normal](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Ordem%20aplicativa%20versus%20ordem%20normal)
> 	- [1.1.6. Expressões condicionais e predicados](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.6.%20Expressões%20condicionais%20e%20predicados)
> 	- [1.1.7. Exemplo: método de aproximação sucessiva de raiz quadrada](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.7.%20Exemplo%20método%20de%20aproximação%20sucessiva%20de%20raiz%20quadrada)
> 	- [1.1.8. Funções como abstrações de caixa-preta](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#1.1.8.%20Funções%20como%20abstrações%20de%20caixa-preta)
> 		- [Nomes locais](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Nomes%20locais)
> 		- [Definição interna e estrutura de blocos](dev/livros/sicp/capitulo-1/subcapitulo-1-1.md#Definição%20interna%20e%20estrutura%20de%20blocos)
> - [1.2. Funções e os processos que elas geram](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.%20Funções%20e%20os%20processos%20que%20elas%20geram)
> 	- [1.2.1. Recursão e iteração lineares](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.1.%20Recursão%20e%20iteração%20lineares)
> 	- [1.2.2. Recursão em árvore](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.2.%20Recursão%20em%20árvore)
> 	- [1.2.3. Ordens de crescimento](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.3.%20Ordens%20de%20crescimento)
> 	- [1.2.4. Exponenciação](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.4.%20Exponenciação)
> 	- [1.2.5. Máximo Divisor Comum](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.5.%20Máximo%20Divisor%20Comum)
> 	- [1.2.6. Exemplo: Testando Primalidade](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#1.2.6.%20Exemplo%20Testando%20Primalidade)
> 		- [Buscando divisores](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#Buscando%20divisores)
> 		- [O teste de Fermat](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#O%20teste%20de%20Fermat)
> 			- [Métodos probabilísticos](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#Métodos%20probabilísticos)

# Introdução

- O uso do termo "ciência da computação" é vago para se referenciar a área, pois não se trata de uma ciência, e sim um tipo de arquitetura;
	- Isso considerando que ciência seja a análise e explicação de fenômenos naturais.
- E não se trata de computadores, e sim da aplicação dos conceitos arquitetados;
	- Nesse contexto, computadores são as ferramentas utilizadas para essa aplicação desses conceitos.

---

- Existem 2 tipos de conhecimento:
	- **Conhecimento declarativo** (_knowledge-that_) → O conhecimento é descrito por meio de afirmações;
		- Por exemplo: se $\sqrt{X}=Y$, então $Y^2=X$ e $Y\ge0$;
		- As afirmações $\sqrt{X}=Y$, $Y^2=X$ e $Y\ge0$ são conhecimentos declarativos.
	- **Conhecimento imperativo** (_knowledge-how_) → O conhecimento é descrito por meio de instruções;
		- Por exemplo, o **método de aproximação sucessiva de raiz quadrada** são passos para se encontrar o valor aproximado de uma raiz quadrada: ^aprox-suc-method-ref
			1. Faça uma estimativa inicial $G$ para o valor da raiz quadrada de $X$;
				-  Por exemplo, $G=3$ para $X=10$, porque $3^2=9$ e $9\lt10$.
			2. Obtenha uma nova estimativa calculando a média entre a soma de $G$ e o resultado da divisão de $X$ por $G$ (i.e. a fórmula $\dfrac{G+(X/G)}{2}$);
				- Para $G=3$ e $X=10$, a nova estimativa é $3.165$;
				- Onde $3.165^2=10.017$.
			3. Repita o passo 2 até chegar em um resultado aceitável;
				- Para $G=3.165$ e $X=10$, a nova estimativa é $3.162$;
				- Onde $3.162^2=9.9982$ que é um resultado aceitável, porque $9.9982\approx10$;
				- Logo, $\sqrt{10}\approx3.162$.
- O conhecimento imperativo é formado por passos que descrevem procedimentos;
- A mesma coisa acontece em linguagens de programação, que são instruções para a execução de processos.
