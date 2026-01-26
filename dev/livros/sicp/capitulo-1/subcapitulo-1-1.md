---
updated-at: 12/02/2026 22:15
created-at: 01/12/2025 22:42
---

> O conteúdo se baseia no subcapítulo [1.1. The Elements of Programming](https://web.mit.edu/6.001/6.037/sicp.pdf#section.1.1).

> [!example] Tabela de conteúdos
> - [1.1. Elementos da programação](#1.1.%20Elementos%20da%20programação)
> 	- [1.1.1. Expressões](#1.1.1.%20Expressões)
> 	- [1.1.2. Nomeação e ambiente](#1.1.2.%20Nomeação%20e%20ambiente)
> 	- [1.1.3. Avaliando combinações](#1.1.3.%20Avaliando%20combinações)
> 	- [1.1.4. Funções complexas (compostas)](#1.1.4.%20Funções%20complexas%20(compostas))
> 	- [1.1.5. Modelo de substituição para aplicação de funções](#1.1.5.%20Modelo%20de%20substituição%20para%20aplicação%20de%20funções)
> 		- [Ordem aplicativa versus ordem normal](#Ordem%20aplicativa%20versus%20ordem%20normal)
> 	- [1.1.6. Expressões condicionais e predicados](#1.1.6.%20Expressões%20condicionais%20e%20predicados)
> 	- [1.1.7. Exemplo: método de aproximação sucessiva de raiz quadrada](#1.1.7.%20Exemplo%20método%20de%20aproximação%20sucessiva%20de%20raiz%20quadrada)
> 	- [1.1.8. Funções como abstrações de caixa-preta](#1.1.8.%20Funções%20como%20abstrações%20de%20caixa-preta)
> 		- [Nomes locais](#Nomes%20locais)
> 		- [Definição interna e estrutura de blocos](#Definição%20interna%20e%20estrutura%20de%20blocos)

# 1.1. Elementos da programação

- Uma linguagem de programação deve conter mais do que apenas instruções para um computador executar tarefas, ela também deve prover estruturas para se organizar ideias sobre um procedimento;
	- Ao aprender uma linguagem, devemos procurar por recursos que permitam a criação dessas estruturas, fazendo com que instruções simples possam ser combinadas para se tornarem instruções complexas.
- Em programação, manipulamos dois tipos de elementos:
	- **Dados** → Qualquer pedaço de informação (e.g., números, letras, palavras, etc.);
	- **Funções** (procedimentos) → Descrição de regras para manipulação de dados.
- Assim, uma linguagem deve ter mecanismos como:
	- **Expressões primitivas** → Elementos gerenciáveis mais básicas da linguagem;
	- **Recursos de combinação** → Combinações de elementos simples em elementos compostos;
	- **Recursos de abstração** → Nomeação e manipulação de elementos compostos.
- Linguagens deve conter funcionalidades para representar elementos primitivos, e combinar e abstrair dados e funções.

## 1.1.1. Expressões

- Ao inserirmos um valor em um terminal REPL ele é avaliado e, se for uma expressão válida, seu resultado é retornado;
	- Avaliar, nesse contexto, se refere à analisar e executar (ou "expandir") o valor de expressões.
- Por exemplo:

```scheme
$> 2
; 2
```

- `2` é uma expressão primitiva que representa o número 2 na base 10;
- Já `+`, por exemplo, é uma expressão que representa uma função (procedimento — _procedure_) primitiva:

```scheme
$> +
; #<procedure #2 +>
```

- A expressão nos retorna uma identificação interna da função primitiva `+`.

---

- Ao combinarmos expressões que representam funções primitivas e expressões que representam números, formamos **expressões complexas** (compostas):

```scheme
$> (+ 5 3)
; 8
$> (- 100 382)
; -282
$> (* 5 99)
; 495
$> (/ 10 5)
; 2
$> (+ 2.7 10)
; 12.7
```

- Em uma expressão composta, a expressão mais à esquerda é o **operador**, e as outras, os **operandos**;
	- Uma das vantagens dessa abordagem é a de que funções podem receber inúmeros argumentos.

> [!info]
> - A convenção de posicionar o operador à esquerda dos operandos é chamada de **notação prefixa** (ou notação polonesa);
> - Mesmo sendo diferente da **notação infixa** (que posiciona os operadores entre os operandos), elas representam a mesma coisa;
> - Assim, `1 + 2 * 3` é o mesmo que `+ 1 * 2 3`.

- Assim, o retorno das expressões complexas representam a execução da função primitiva sob as expressões que representam números;
- Uma lista de expressões entre parênteses (`()`) que representa a execução de uma função é chamada **combinação**;
	- Por exemplo, `(+ 1 2 3 4)` é uma expressão composta e uma combinação;
	- Obtemos o valor dela ao aplicarmos a função/operador `+` sob os argumentos/operandos `1`, `2`, `3` e `4`;
	- Simplificando, combinações representam "chamadas de função".
- Uma combinação pode conter outras combinações:

```scheme
$> (+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))
;? (+ (* 3 (+ 8 8)) (+ (- 10 7) 6))
;? (+ (* 3 16) (+ 3 6))
;? (+ 48 9)
; 57
```

> [!info]
> - A formatação (_pretty-printing_) de combinações aninhadas segue o padrão:
> ```scheme
> (+ (* 3
> 	  (+ (* 2 4)
> 	     (+ 3 5)))
>    (+ (- 10 7)
>       6))
> ```
> - Onde cada operador e operando segue uma estrutura hierárquica.

## 1.1.2. Nomeação e ambiente

- No Scheme, para nomearmos elementos usamos a instrução (_keyword_) `define`:

```scheme
$> (define var 3)
$> var
; 3
$> (* 5 var)
; 15
$> (define double-var (* 2 var))
$> double-var
; 6
```

- A instrução `define` é um dos **recursos de abstração** presentes na linguagem Scheme;
- A funcionalidade de nomearmos valores significa que o interpretador possui uma "memória" que gerencia esses pares;
- Essa memória é chamada de **ambiente** (precisamente **ambiente global** — _global environment_).

## 1.1.3. Avaliando combinações

- Ao avaliar combinações, o próprio interpretador segue um procedimento (_procedure_ ou função), que é:
	1. Avaliar as subcombinações (ou subexpressões) presentes na combinação;
	2. Aplicar a função sob os argumentos (que podem ser expressões primitivas ou o valor de outras subexpressões).
- Note que o processo de avaliação é naturalmente recursivo, isso porque, para avaliarmos uma combinação, devemos primeiro avaliar seus argumentos;
	- Como os argumentos de uma combinação pode ser outras combinações, o processo de avaliação acontece recursivamente.
- Por exemplo:

```scheme
$> (* (+ 2 (* 4 6)) (+ 3 5 7))
;? (* (+ 2 24) (+ 3 5 7))
;? (* 26 15)
; 390
```

- No exemplo, 4 combinações diferentes foram avaliadas;
	- Podemos verificar quantas avaliações serão feitas pela quantidade de "abre-parênteses" `(` presentes na combinação.
- Para visualizarmos isso, podemos representar a combinação em um formato de árvore:

```
               (390)
             ____|____
   _________/    |    \_________
  /              |              \
(*)             (26)            (15)
               __|__         ____|____
              /  |  \       /  |   |  \
            (*) (4) (6)   (+) (3) (5) (7)
```

- Note que os resultados das combinações são acumulados de baixo para cima, até chegarem ao valor da raiz.
	- Esse processo é chamado de **acumulação em árvore** (_tree accumulation_) ou **agregação em árvore**.

---

- O processo de avaliação de combinações sempre resulta na avaliação de expressões primitivas, que podem ser **números**, **operadores padrão** (_built-in operators_) ou **nomes personalizados**;
- Processamos essas expressões partindo do ponto que:
	1. Expressões primitivas cujos nomes são números possuem os valores numéricos correspondentes na base 10;
		- Por exemplo, a expressão primitiva `2` representa o número `2` na base 10.
	2. Expressões primitivas cujos nomes são operadores padrão possuem o valor de sequências de instrução de máquina que executam a operação correspondente;
		- Por exemplo, a expressão primitiva `+` representa uma instrução de máquina que executa a operação de soma.
	3. Expressões primitivas cujos nomes são personalizados possuem o valor de objetos do ambiente com o mesmo nome, sendo associados a qualquer valor.
		- Por exemplo, a expressão primitiva `test` representa o objeto `test` que pode representar qualquer valor ou função do ambiente global.
- Podemos considerar o ponto 2 como sendo um caso especial do ponto 3, isso porque operadores padrão são objetos do ambiente global que estão associados a sequências de instrução de máquina;
	- Assim, um dos propósitos do ambiente global é associar valores (ou "dar significado") a nomes (ou símbolos) em expressões.

> [!info]
> - Regras para avaliação de combinações não são aplicadas sob operações de definição;
> - Isso porque a avaliação de `(define x 1)`, por exemplo, não aplica `define` sob os argumentos, ela os relaciona;
> - Exceções à regra de avaliação são chamadas **formas especiais** (_special forms_).

## 1.1.4. Funções complexas (compostas)

- Identificamos no Scheme que:
	- Números e operações aritméticas são expressões primitivas e funções primitivas, respectivamente;
	- Aninhar combinações permite a combinação de operações;
	- Associar nomes com valores permite uma abstração básica.

---

- Funções complexas (funções compostas — _compound procedure_) permitem que expressões complexas sejam nomeadas e referenciadas;
- Por exemplo, obtemos o valor do quadrado de um dado número ao multiplicarmos ele por si mesmo:

```scheme
$> (define (square x) (* x x))
```

- Criamos a função complexa `square`, que pode ser lida como:
	- `define` → Para obter;
	- `square` → O quadrado;
	-  `x` → De um dado número;
	- `* x x` → Multiplicamos ele por si mesmo.
- Note como a definição de uma função complexa pode ser lida usando a linguagem natural.

---

- A forma (sintaxe) mais simples de se definir uma função é:

```
(define ({nome} {parâmetros}) {corpo})
```
 
- Onde:
	- `{nome}` → São os símbolos que nomearão a definição (i.e. `{corpo}`) da função no ambiente;
	- `{parâmetros}` → São os nomes que serão usados na definição da função para identificar os argumentos passados;
	- `{corpo}` → É uma expressão que armazena o valor da aplicação da função quando os parâmetros forem substituídos pelos argumentos.

> [!info]
> - A definição da função (`{nome}` e `{parâmetros}`) são agrupados por parênteses (`()`), assim como a própria chamada da função.

- Podemos executar nossa função `square`:

```scheme
$> (square 3)
; 9
$> (square (+ 2 5))
; 49
$> (square (square 3))
; 81
```

- Podemos ainda usá-la para definir outras funções, por exemplo $X^2+Y^2$ pode ser expressa como:

```scheme
$> (define (sum-of-squares x y)
	(+ (square x) (square y)))
$> (sum-of-squares 3 4)
; 25
$> (define (f a)
	(sum-of-squares (+ a 1) (* a 2)))
$> (f 5)
; 136
```

## 1.1.5. Modelo de substituição para aplicação de funções

- Não há diferença entre a avaliação de combinações, seja o operador uma função primitiva ou uma função complexa;
	- O interpretador primeiro avalia as subcombinações e aplica a função sob os operandos (argumentos).
- O mecanismo para aplicar funções primitivas sob argumentos é padrão do interpretador;
	- Porque funções primitivas já são mapeadas para instruções internas.
- Para funções complexas o processo é diferente, isso porque é necessário retornar o corpo da função (expandir) e avaliá-lo, substituindo cada parâmetro pelo argumento correspondente;
- Por exemplo:

```scheme
$> (f 5)
;? (sum-of-squares (+ a 1) (* a 2))
;? (sum-of-squares (+ 5 1) (* 5 2))
;? (sum-of-squares 6 10)
;? (+ (square x) (square y))
;? (+ (square 6) (square 10))
;? (+ (* x x) (* x x))
;? (+ (* 6 6) (* 10 10))
;? (+ 36 100)
; 136
```

- Esse processo é chamado de **modelo de substituição** (_substitution model_) para aplicação de funções;
- Podemos considera-lo como sendo o procedimento para determinar o "significado" de uma aplicação de função.

### Ordem aplicativa versus ordem normal

- A avaliação por aplicação (i.e. avaliar os operandos de uma expressão e usar o resultado) não é a única forma de se realizar avaliações;
- Uma forma alternativa seria a de avaliar os operandos apenas quando seus valores forem necessários;
- Dessa forma, substituiríamos os parâmetros por expressões, até chegarmos em uma expressão que possua apenas funções primitivas e, então, realizaríamos as avaliações;
- Por exemplo:

```scheme
$> (f 5)
;? (sum-of-squares (+ 5 1) (* 5 2))
;? (+ (square (+ 5 1)) (square (* 5 2)))
;? (+ (* (+ 5 1) (+ 5 1)) (* (* 5 2) (* 5 2)))
;? (+ (* 6 6) (* 10 10))
;? (+ 36 100)
; 136
```

- Esse processo de "expandir tudo e reduzir" é chamado de **avaliação de ordem normal** (_normal-order evaluation_);
- Ao contrário de "avaliar os argumentos e aplicar" usado pelo interpretador, que é chamado de de **avaliação de ordem aplicativa** (_applicative-order evaluation_).

## 1.1.6. Expressões condicionais e predicados

- Até o momento não temos como atestar valores ou realizarmos diferentes operações dependendo de alguma verificação;
- Por exemplo, ainda não conseguimos criar uma função que computa o valor absoluto de um número verificando se ele é positivo, negativo ou zero:

$$
\begin{equation}
	|X| = \begin{cases}
		   ~~~ X & \text{se} & X > 0, \\
		   ~~~~ 0 & \text{se} & X = 0, \\
		   -X & \text{se} & X < 0.
	\end{cases}
\end{equation}
$$

- Obter diferentes soluções com base em condições é chamado de **análise de caso** (_case analysis_);
- Em Scheme, usamos a forma especial `cond` (i.e. _conditional_) para implementá-las:

```scheme
$> (define (abs x)
	 (cond ((> x 0) x)
		   ((= x 0) 0)
		   ((< x 0) (- x))))
```

- A forma (estutura) de uma expressão condicional é:

```
(cond ({p1} {e1}) ({p2} {e2}) ... ({pn} {en}))
```

- Que consiste na _keyword_ `cond` seguida de **cláusulas** (_clauses_) que são pares de expressões entre parênteses (e.g., `({p} {e})`);
- O primeiro parâmetro `{p}` de cada cláusula é um **predicado** (_predicate_), que são expressões cujo valores são avaliados para as constantes `#t` (`true`) ou `#f` (`false`).
	- Caso o  valor de um predicado seja `#f`, o interpretador considera-o `false`;
	- Qualquer outro valor é considerado `true`.

---

- Na avaliação de uma expressão condicional, o predicado `{p1}` é avaliado primeiro, caso seu valor seja `false`, o predicado `{p2}` é avaliado, e assim por diante, até que um predicado com valor `true` seja avaliado;
- O valor da expressão condicional é a **expressão consequente** (_consequent expression_) `{e}` do predicado com valor `true`;
- Caso nenhum predicado possua valor `true`, o valor da expressão condicional é `undefined`;

> [!info]
> - Predicados são funções e expressões que retornam um valor booleano (`true` ou `false`).

- Nossa função `abs` usa os **predicados primitivos** `>`, `<` e `=`, que recebem 2 números como argumentos e verificam se o primeiro número é, respectivamente, maior que, menor que ou igual ao segundo número, retornando `true` ou `false`.
	- E o operador menos (`-`) que, quando usado com um único operando, converte seu valor para negativo.

---

- Podemos reescrever a função `abs` da seguinte forma:

```scheme
$> (define (abs x) 
	 (cond ((< x 0) (- x))
		   (else x)))
```

- Onde `else` é uma forma especial que pode ser usada para substituir um predicado na última cláusula de uma `cond`;
- Ela faz com que o valor da expressão condicional seja o valor da expressão consequente `{e}` equivalente se todos predicados anteriores forem `false`;
	- Podemos considerá-la como sendo um "predicado que sempre é `true`";
	- Assim, qualquer predicado que sempre seja `true` pode substituí-la (e.g., `(= 1 1)`).
- Podemos ainda reescrever a função `abs` de outra forma:

```scheme
$> (define (abs x)
	 (if (< x 0)
		 (- x)
		 x))
```

- Onde `if` é uma forma especial que pode ser usada para realizar verificações que possuem exatamente dois casos;
- A estrutura de uma expressão `if` é:

```
(if {predicado} {consequencia} {alternativa})
```

- Na avaliação de uma expressão `if`, o predicado é avaliado primeiro, caso seja `true`, o interpretador avalia a `{consequencia}` e faz com que expressão condicional tenha seu valor, senão, avalia a `{alternativa}` e faz com que a expressão condicional tenha seu valor.

---

- Além dos predicados primitivos `>`, `<` e `=`, existem ainda **operadores lógicos** que permitem a criação de predicados complexos, como:

> `(and {e1} ... {en})`
> - O interpretador avalia todas expressões `{e}`, uma de cada vez, da esquerda pra direita;
> - Se alguma for `false`, o valor da expressão é `false`, e as outras expressões não são avaliadas;
> - Se todas forem `true`, o valor da expressão é o mesmo da última `{e}`.
> ---
> `(or {e1} ... {en})`
> - O interpretador avalia todas expressões `{e}`, uma de cada vez, da esquerda pra direita;
> - Se alguma for `true`, o valor da expressão é o mesmo da `{e}`, e as outras expressões não são avaliadas;
> - Se todas forem `false`, o valor da expressão é `false`.
> ---
> `(not {e})`
> - O valor da expressão é `true` quando `{e}` for `false`, e `false` quando `{e}` for `true`.

- Note que `and` e `or` são formas especiais porque nem todas subexpressões são avaliadas;
- Com operadores lógicos podemos, por exemplo, verificar se um número está entre 5 e 10:

```scheme
$> (define x 6)
$> (and (> x 5) (< x 10))
; #t
```

- Ou criar uma função que verifica se um número é maior ou igual ($\ge$) a outro:

```scheme
$> (define (>= x y)
	 (or (> x y) (= x y)))
$> (>= 2 1)
; #t
$> (>= 0 1)
; #f
```

- Podemos reescrever a função `>=` para:

```scheme
$> (define (>= x y) (not (< x y)))
```

## 1.1.7. Exemplo: método de aproximação sucessiva de raiz quadrada

- Funções de computador são como funções matemáticas, porém, funções de computador devem chegar a um resultado;
- Por exemplo, ao computar raízes quadradas, podemos definir:

$$\sqrt{X}=Y, ~ \text{então} ~ Y\ge0 ~ \text{e} ~ Y^2=X.$$

- Essa definição é cabível para funções matemáticas, isso porque podemos utilizá-la para validar se um número é a raiz quadrada de outro;
- Em contrapartida, essa definição não é cabível para funções de computador, porque não descreve nada sobre como encontrar a raiz quadrada de um número;
- Por exemplo:

```scheme
$> (define (sqrt x)
	 (y (and (>= y 0)
			 (= (square y) x))))
$> (sqrt 2)
; *** ERROR IN sqrt, console@93:4 -- Unbound variable: y
```

- Não computa o valor da raiz quadrada de um número;
- Essa diferença entre funções matemática e de computadores é um reflexo da diferença entre conhecimentos declarativo (o que é) e imperativo (como fazer);
	- Isso porque, funções de computador precisam ser uma descrição de como se chegar em um dado valor, diferentemente de funções matemáticas, que são descrições de propriedades.

---

- Para criarmos uma função de computador que computa o valor da raiz quadrada de um número podemos usar o [método de aproximação sucessiva](dev/livros/sicp/capitulo-1/_indice.md#^aprox-suc-method-ref):

```scheme
; /sqrt.scm
(define (average x y)
	 (/ (+ x y) 2))

(define (improve guess x)
	 (average guess (/ x guess)))

(define (abs x)
	 (if (< x 0)
		 (- x)
		 x))

(define (good-enough? guess x)
	 (< (abs (- (square guess) x)) 0.001))

(define (sqrt-iter guess x)
	 (if (good-enough? guess x)
		 guess
		(sqrt-iter (improve guess x) x)))

(define (sqrt x) (sqrt-iter 1.0 x))
```

- Note como a função `sqrt-iter` é executada iterativamente (em looping), mesmo não executando nenhuma instrução de looping;
- Essas iterações acontecem pois `sqrt-iter` é uma função **recursiva**, ou seja, ela se "auto-define" (a definição da função inclui uma chamada dela mesma).

## 1.1.8. Funções como abstrações de caixa-preta

- A função `sqrt` é um exemplo de um processo definido por um conjunto de funções:

```
            (sqrt)
               |
          (sqrt-iter)
           ____|____
          /         \
   (good-enough)   (improve)
     ____|____         |
    /         \    (average)
(square)    (abs)
```

- Encontrar o valor de uma raiz quadrada, naturalmente, se divide em subproblemas;
- Em `sqrt`, cada um desses subproblemas é resolvido em uma função;
	- Assim, `sqrt` pode ser visualizado como um conjunto de funções que refletem os passos para se encontrar uma raiz quadrada.

---

- Essa separação não deve acontecer apenas para dividir o programa em partes, mas para que cada uma das funções execute uma tarefa e se torne um módulo à ser utilizado por outras funções;
- Por exemplo, na função `good-enough?` usamos as funções `abs` e `square`:

```scheme
$> (define (good-enough? guess x)
	 (< (abs (- (square guess) x)) 0.001))
```

- Nesse contexto, `abs` e `square` são modulares, ou seja, podem ser substituídas por quaisquer outras funções que tenham o mesmo propósito (i.e. obter o valor absoluto e o quadrado de um número, respectivamente);
- Esse nível de modularidade faz com que essas funções sejam **abstrações procedurais** (_procedural abstraction_) para as funções que a executam (i.e. abstrações do procedimento);
	- Assim, é irrelevante **como** a função chega em resultado, o que importa é **o que** foi retornado.
- Por exemplo, poderíamos reescrever  função `square` de outra forma para obter o quadrado de um número:

```scheme
$> (define (square x) (* x x))
$> (square 4)
; 16
$> (define (square x) (exp (* 2 (log x))))
$> (square 4)
; 15.999999999999998
```

- Mesmo com procedimentos diferentes, ambas funções computam o valor do quadrado de um número.

> [!info]
> - A operação `(exp (* 2 (log x)))` pode ser traduzida para $\exp(2\cdot\log(x))$ ou $e^{2\cdot\log(x)}$.
> ---
> - Exponencial é a função matemática utilizada para se obter o valor de uma base $b$ elevada a um expoente $x$;
> 	- Por exemplo: $b^x=y$;
> 	- Para $b=4$ e $x=3$:
> 		- $4^3=y\rightarrow4\cdot4\cdot4=y\rightarrow y=64$.
> - A função `exp` do Scheme computa o número irracional de Euler ($e=2.7182$) elevado a um dado expoente[^1].
> ---
> - Logaritmo é a operação inversa da exponenciação;
> - Ela é utilizada para encontrar o expoente $x$ que a base $b$ deve ser elevada para resultar em $y$;
> - Assim, se $b^x=y$, então $\log_b(y)=x$, onde $b$ é a base, $x$ é o expoente e $y$ é o resultado da exponenciação;
> - Quando apenas 1 argumento é passado para a função `log` do Scheme o interpretador considera-o um **logaritmo natural** ($\log=\ln$)[^2].
> 	- Logaritmos naturais possuem base igual ao número irracional de Euler.
> ---
> - Exponenciais e logaritmos de mesma base cancelam umas as outras (assim como multiplicação e divisão, soma e subtração, etc.);
> - Isso acontece porque, um calcula o resultado da exponenciação, e outro o valor do expoente, respectivamente;
> - Então:
> 	- Para um exponencial com mesma base que um logaritmo, procuramos pelo resultado $x$ da exponenciação, logo, $b^{\log_b(x)}=x$;
> 	- Para um logaritmo com mesma base que um exponencial, procuramos pelo valor do expoente $x$ da base $b$, logo, $\log_b(b^x)=x$.
> - Exemplificando, com $b=2$ e $x=3$ temos, para ambos os casos:
> $$
> \begin{gather}
> 	b^{\log_b(x)}=\log_b(b^x) \\
> 	2^{\log_2(3)}=\log_2(2^3) \\
> 	2^{1.584}=\log_2(8) \\
> 	2.997\approx3
> \end{gather}
> $$
> - Por isso, $e^{\ln(x)}=x$.
> ---
> - Assim, para `(exp (* 2 (log x)))` com $x=4$:
> $$
> \begin{gather}
> 	=e^{2\cdot\ln(4)} \\
> 	=(e^{\ln(4)})^2 \\
> 	=(e^{\log_e(4)})^2 \\
> 	=4^2 \\
> 	=16
> \end{gather}
> $$
> - Em $e^{2\cdot\ln(4)}=(e^{\ln(4)})^2$, aplicamos a propriedade de potências que diz que $x^{y\cdot z}=(x^y)^z$;
> - Essa afirmação é verdade porque, com $x=2$, $y=2$ e $z=3$:
> 	- $(2^2)^3=(2\cdot2)^3=(2\cdot2)\cdot(2\cdot2)\cdot(2\cdot2)=64$;
> 	- $2^{2\cdot3}=2^6=64$.

### Nomes locais

- O nome dos parâmetros de uma função não deve impactar quem a executa, assim, as funções abaixo devem ser idênticas:

```scheme
$> (define (square y) (* y y))
$> (define (square x) (* x x))
```

- Por exemplo, em:

```scheme
$> (define (good-enough? guess x)
	 (< (abs (- (square guess) x)) 0.001))
```

- O parâmetro `x` aparece em ambas funções, porém, a execução de `square` não afeta o valor de `x` usado por `good-enough?`;
- Se parâmetros não fossem limitados ao corpo das funções que os definem algumas funções dependeriam de outras para serem executadas;
	- Por exemplo: `good-enough?` dependeria exclusivamente da função `square` que recebe um parâmetro com nome `x`.
- Isso faria com que a abstração procedural (i.e. abstração de caixa-preta) fosse perdida.

---

- Parâmetros de funções são chamados de **variáveis vinculadas** (_bound variable_), isso porque parâmetros são variáveis vinculadas a uma função;
	- Variáveis globais (não vinculadas) são **livres** (_free_).
- O escopo ("limite") de  variáveis vinculadas é o corpo da função que as definem;
- Em `good-enough?`, `guess` e `x` são variáveis vinculadas, já `<`, `-`, `abs` e `square` são livres;
- Os nomes das variáveis vinculadas são arbitrários, contanto que sejam diferentes dos nomes das variáveis livres.

### Definição interna e estrutura de blocos

- Em __sqrt.scm__, a única função que realmente importa para o usuário é `sqrt`, com outras funções sendo auxiliares;
- Isso faz com que nomes de funções sejam exclusivas à `sqrt`, assim, não podemos definir uma função `good-enough?` com uma implementação diferente da usada por `sqrt`;
- Para contornarmos isso podemos definir funções internas:

```scheme
; /sqrt.scm
(define (sqrt x)
  (define (good-enough? guess x)
    (< (abs (- square guess) x)) 0.001)
  (define (improve guess x)
	(average guess (/ x guess)))
  (define (sqrt-iter guess x)
	(if (good-enough? guess x)
		guess
		(sqrt-iter (improve guess x) x)))
  (sqrt-iter 1.0 x))
```

- Essa estratégia é chamada de **estrutura de blocos** (_block structure_), ela permite que funções sejam limitadas ao escopo de outras funções;
- Agora, como `good-enough?`, `improve` e `sqrt-iter` estão no mesmo escopo de `x`, podemos usá-la como uma variável livre ao invés de passá-la como argumento para todas funções auxiliares:

```scheme
; /sqrt.scm
(define (sqrt x)
  (define (good-enough? guess)
    (< (abs (- square guess) x)) 0.001)
  (define (improve guess)
	(average guess (/ x guess)))
  (define (sqrt-iter guess)
	(if (good-enough? guess)
		guess
		(sqrt-iter (improve guess) x)))
  (sqrt-iter 1.0 x))
```

- Essa estratégia é chamada **escopo léxico** (_lexical scoping_) e ajuda na construção e organização de programas muito grandes;
- A definição de funções internas deve vir primeiro no corpo de uma função.

[^1]: SPERBER, Michael et al. [r6rs — Revised 6 Report on the Algorithmic Language Scheme. p. 45](https://standards.scheme.org/official/r6rs.pdf#page=45). Acesso em 10/12/2025.
[^2]: SHINN, Alex; COWAN, John; GLECKLER, Arthur A. [r7rs — Revised 7 Report on the Algorithmic Language Scheme. p. 38](https://standards.scheme.org/official/r7rs.pdf#page=38). Acesso em 10/12/2025.
