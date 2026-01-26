---
updated-at: 12/02/2026 22:54
created-at: 15/12/2025 10:35
---

> O conteúdo se baseia no subcapítulo [1.2. Procedures and the Processes They Generate](https://web.mit.edu/6.001/6.037/sicp.pdf#section.1.2).

> [!example] Tabela de conteúdos
> - [1.2. Funções e os processos que elas geram](#1.2.%20Funções%20e%20os%20processos%20que%20elas%20geram)
> 	- [1.2.1. Recursão e iteração lineares](#1.2.1.%20Recursão%20e%20iteração%20lineares)
> 	- [1.2.2. Recursão em árvore](#1.2.2.%20Recursão%20em%20árvore)
> 	- [1.2.3. Ordens de crescimento](#1.2.3.%20Ordens%20de%20crescimento)
> 	- [1.2.4. Exponenciação](#1.2.4.%20Exponenciação)
> 	- [1.2.5. Máximo Divisor Comum](#1.2.5.%20Máximo%20Divisor%20Comum)
> 	- [1.2.6. Exemplo: Testando Primalidade](#1.2.6.%20Exemplo%20Testando%20Primalidade)
> 		- [Buscando divisores](#Buscando%20divisores)
> 		- [O teste de Fermat](#O%20teste%20de%20Fermat)
> 			- [Métodos probabilísticos](#Métodos%20probabilísticos)

# 1.2. Funções e os processos que elas geram

- Funções são padrões para a evolução local de um processo computacional, especificando como cada parte do processo é realizada a partir da anterior;
- Essa seção exemplificará alguns dos padrões mais comuns em processos gerados por funções;

## 1.2.1. Recursão e iteração lineares

- Considere a função fatorial:

$$n!=n\cdot(n-1)\cdot(n-2)~\dots~3\cdot2\cdot1$$

> [!info]
> Função fatorial é uma função matemática que consiste na multiplicação de um número natural por todos inteiros positivos menores do que ele.

- Podemos computar essa função matemática observando que, para qualquer número inteiro e positivo $n$, $n!$ é igual a $n$ vezes $(n-1)!$:

$$n!=n\cdot[(n-1)\cdot(n-2)~\dots~3\cdot2\cdot1]=n\cdot(n-1)!$$

- Assim, computar $n!$ é o mesmo que computar $(n-1)!$ e multiplicá-lo por $n$;
- Podemos traduzir essa afirmação para uma função Scheme:

```scheme
$> (define (factorial n)
	 (if (= n 1)
		 1
		 (* n (factorial (- n 1)))))
$> (factorial 4)
;? (* 4 (factorial 3))
;? (* 4 (* 3 (factorial 2)))
;? (* 4 (* 3 (* 2 (factorial 1))))
;? (* 4 (* 3 (* 2 1)))
;? (* 4 (* 3 2))
;? (* 4 6)
; 24
```

- Usando o modelo de substituição de avaliação de ordem normal podemos visualizar o processo de se computar $4!$.

---

- Essa não é a única forma de se traduzir $n!$ para Scheme, podemos multiplicar $n$ por um contador que vai de 1 à $n$, e finalizarmos quando o contador for maior que $n$:

```scheme
$> (define (factorial n)
	 (define (iter product counter)
	   (if (> counter n)
		   product
		   (iter (* counter product)
				 (+ counter 1))))
	 (iter 1 1))
$> (factorial 4)
;? (iter 1 1)
;? (iter 1 2)
;? (iter 2 3)
;? (iter 6 4)
;? (iter 24 5)
; 24
```

- Nessa abordagem, o contador e o produto do cálculo são atualizados simultaneamente entre os passos.

---

- Ambas funções computam o valor de $n!$, obtendo o mesmo número de produtos parciais (i.e. operações "incompletas");
- Porém, note como a execução da primeira implementação de `factorial` é expandida e reduzida;
- Essa expansão resulta em uma cadeia de **operações adiadas** (_deferred operations_) que, nesse caso, é a cadeia de multiplicações de $n!$;
	- Um processo formado por essa cadeia de operações é chamado de **processo recursivo** (_recursive process_).
- Executar esse tipo de processo requer que o interpretador gerencie quais operações serão executadas posteriormente;
- Nesse contexto, ao computarmos $n!$, a quantidade de multiplicações adiadas e a quantidade de informação necessária para a gerencia do processo são proporcionais ao valor de $n$.
	- O nome desse tipo de processo é **processo linearmente recursivo** (_linear recursive process_). ^linear-rec-proc-ref

---

- Em contrapartida, a execução da segunda implementação de `factorial` não expande e reduz, isso porque, para cada $n$ passo, gerenciamos o valor das variáveis `product` e `counter`;
- Esse tipo de processo é chamado de **processo iterativo** (_iterative process_), nele, o estado da execução é representado pelos valores fixos de **variáveis de estado** (_state variables_) e de regras que descrevem como essas variáveis devem ser atualizadas a cada passo (i.e. estado);
	- Essas regras devem ter um caso-base que finaliza o processo.
- Nesse contexto, ao computarmos $n!$, a quantidade de multiplicações é proporcional ao valor de $n$.
	- O nome desse tipo de processo é **processo linearmente iterativo** (_linear iterative process_). ^linear-iter-proc-ref

---

- No caso iterativo, as variáveis da função fornecem uma descrição completa do estado do processo em qualquer ponto da execução;
	- Caso interrompessemos a execução, poderíamos recuperar seu estado apenas passando valores para as variáveis.
- Já no caso recursivo, essas informações são gerenciadas pelo interpretador e o estado é a cadeia de operações adiadas.

---

- Funções recursivas e processos recursivos são diferentes, enquanto uma é a capacidade de uma função definir a si mesma, a outra é a forma (formato) que um processo cresce, respectivamente;
- Essa diferenciação pode ser confusa, isso porque a maioria das implementações de linguagens comuns (e.g., C, Java, etc.) são projetadas de uma forma que qualquer função recursiva consome uma quantidade de memória que cresce junto com o número de execuções, mesmo que o processo seja iterativo;
	- Assim, para criar iterações nessas linguagens são usadas instruções como `for`, `do`, `while`, etc.
- Nas implementações Scheme, mesmo que uma função seja recursiva, ela será executada como um processo iterativo em um espaço constante (sem aumentar o uso de memória conforme o número de execuções)[^1];
	- Esse tipo de implementação é chamado de **função de fim recursivo** (_tail-recursive_), nela, processos iterativos podem ser realizados por meio de funções recursivas.

> [!info]
> - Em funções de fim recursivo, a última operação feita deve ser a chamada recursiva da função, sem nenhuma computação pendente a ser realizada;
> - A função `iter` do caso iterativo de `factorial` é _tail-recursive_, isso porque, se o contador não for maior que `n`, `iter` executa a si mesmo;
> - Na avaliação de `(iter (* counter product) (+ counter 1))`, as subexpressões `(* counter product)` e `(+ counter 1)` são avaliadas primeiro, e então `iter` é executada;
> - No caso recursivo de `factorial`, em `(* n (factorial (- n 1)))`, a execução de `factorial` não acontece até que o caso-base seja alcançado.

- Scheme possui essa particularidade por incentivar a escrita de código **funcional**, diferentemente de C, por exemplo, que incentiva uma abordagem **procedural**;
- Onde:
	> **Funcional**
	> - As instruções são passadas descrevendo **o que** se deseja como resultado, organizando o fluxo de execução por meio de recursão e priorizando estado imutável;
	> - Por exemplo:
	>
	> ```scheme
	> (define (factorial n)
	> 	(if (= n 1)
	> 		1
	> 		(* n (factorial (- n 1)))))
	> ```

	> **Procedural**
	> - As instruções são passadas descrevendo **como** se deseja chegar a um resultado, organizando o fluxo de execução por meio de estruturas de controle e variáveis mutáveis:
	> - Por exemplo: 
	> 
	> ```c
	> int factorial(int n) {
	> 	int result = 1;
	> 	for (int i = 1; i <= n; i++) {
	> 		result *= i;
	> 	}
	> 	return result;
	> }
	> ```

## 1.2.2. Recursão em árvore

- Considere a sequência:

$$0,1,1,2,3,5,8,13,21~\cdots$$

- Essa é a sequência de Fibonacci, onde cada número é a soma dos dois anteriores, começando em 0 e 1;
- Os números da sequência de Fibonacci são definidos pela regra:

$$
\begin{equation}
	Fib(n) = \begin{cases}
		   ~0 & \text{se} ~ n = 0, \\
		   ~1 & \text{se} ~ n = 1, \\
		   ~Fib(n-1) + Fib(n-2) & \text{caso contrário}.
	\end{cases}
\end{equation}
$$

- Podemos traduzir essa análise de caso para Scheme:

```scheme
$> (define (fib n)
     (cond ((= n 0) 0)
           ((= n 1) 1)
           (else (+ (fib (- n 1))
                    (fib (- n 2))))))
$> (fib 5)
;? (+ (fib (- 5 1)) (fib (- 5 2)))
;? (+ (fib 4) (fib 3))
;? (+ (+ (fib (- 4 1)) (fib (- 4 2))) (+ (fib (- 3 1)) (fib (- 3 2))))
;? (+ (+ (fib 3) (fib 2)) (+ (fib 2) (fib 1)))
;? (+ (+ (+ (fib (- 3 1)) (fib (- 3 2))) (+ (fib (- 2 1)) (fib (- 2 2)))) (+ (+ (fib (- 2 1)) (fib (- 2 2))) 1))
;? (+ (+ (+ (fib 2) (fib 1)) (+ (fib 1) (fib 0))) (+ (+ (fib 1) (fib 0)) 1))
;? (+ (+ (+ (fib 2) 1) (+ 1 0)) (+ (+ 1 0) 1))
;? (+ (+ (+ (+ (fib (- 2 1)) (fib (- 2 2))) 1) (+ 1 0)) (+ (+ 1 0) 1))
;? (+ (+ (+ (+ (fib 1) (fib 0)) 1) (+ 1 0)) (+ (+ 1 0) 1))
;? (+ (+ (+ (+ 1 0) 1) 1) (+ 1 1))
;? (+ (+ (+ 1 1) 1) 2)
;? (+ (+ 2 1) 2)
;? (+ 3 2)
; 5
```

- Na função `fib`, para computar `(fib 5)`, computamos `(fib 4)` e `(fib 3)`;
- Para computar `(fib 4)`, computamos `(fib 3)` e `(fib 2)`, assim até chegarmos nos casos-base;
- Note que o processo evolui como uma árvore, onde cada execução é dividida "galhos":

```
                                              (fib 5)
                               __________________|__________________
                              /                                     \
                          (fib 4)                                 (fib 3)
                  ___________|___________                       ______|______
                 /                       \                     /             \
             (fib 3)                   (fib 2)             (fib 2)         (fib 1)
           _____|_____                 ___|___             ___|___            |       
          /           \               /       \           /       \          (1)
      (fib 2)       (fib 1)       (fib 1)   (fib 0)   (fib 1)   (fib 0)
     ___|___           |             |         |         |         |
    /       \         (1)           (1)       (0)       (1)       (0)
(fib 1)   (fib 0)
   |         |
  (1)       (0)
```

- A divisão acontece porque a função `fib` chama a si mesmo duas vezes a cada execução;
- Essa implementação foi feita apenas para ilustrar uma recursão em árvore, havendo muitos defeitos;
	- Por exemplo, `(fib 3)`, que representa metade da computação, é executada 2 vezes.
- Assim, o número de passos cresce **exponencialmente** com $n$, porém, o uso de memória cresce **linearmente** com $n$;
- Isso porque precisamos monitorar apenas os nós superiores na árvore em qualquer ponto da computação;
- No geral, o número de passos de um processo de recursão em árvore vai ser proporcional ao número de nós, enquanto o uso de memória vai ser proporcional a profundidade máxima da árvore.

## 1.2.3. Ordens de crescimento

[//]: #(TODO: revisar)

- Os exemplos anteriores ilustram que processos podem consumir diferentes quantidades de recursos computacionais;
- Uma forma de medirmos essa diferença é usando **ordens de crescimento** para visualizar a quantidades de recursos utilizados por um processo conforme a entrada (input) aumenta;
- Fazemos isso considerando $n$ um parâmetro que mede o tamanho do problema, e $R(n)$ a quantidade de recursos que um processo requer para computar um problema;
	- Ao computarmos a aproximação da raiz quadrada de um número, por exemplo, poderíamos considerar $n$ como sendo a quantidade de dígitos de precisão do resultado;
	- Assim, $R(n)$ poderia medir o número de registradores usados, o número de operações feitas, etc.

---

- $R(n)$ tem ordem de crescimento $\Theta(f(n))$ (theta de uma função genérica) quando o crescimento de $R(n)$ é proporcional ao de $f(n)$;
	- $\Theta$ (Theta) representa funções que crescem no mesmo ritmo, ignorando constantes e detalhes pequenos.
- Isso significa que existem constantes $k_1$ e $k_2$ positivas e independentes de $n$ (i.e. não mudam mesmo quando $n$ muda), que, para valores grandes de $n$, fazem com que $R(n)$ sempre fique entre $k_1\cdot f(n)$ e $k_2\cdot f(n)$:

$$k_1f(n)\leq R(n)\leq k_2f(n)$$

- Para [processos linearmente recursivos](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#^linear-rec-proc-ref) o número de passos cresce proporcionalmente à entrada $n$;
	- Assim, os passos e o espaço necessários para esse processo crescem como $\Theta(n)$.
- Para o [processos linearmente iterativos](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#^linear-iter-proc-ref), o número de passos ainda é $\Theta(n)$, porém o espaço é constante $\Theta(1)$;

---

- Ordens de crescimento fornecem uma breve descrição do comportamento de um processo;
	- Por exemplo, processos que requerem $n^2$, ou $1000n^2$, ou $3n^2+10n+17$ passos têm a mesma ordem de crescimento $\Theta(n^2)$.
- Elas também indicam como o comportamento de um processo muda conforme o tamanho do problema muda;
	- Por exemplo:
		- Para um processo $\Theta(n)$ (linear) dobrar o tamanho, a quantidade de recursos usados também dobrará;
		- Para um processo $\Theta(n^2)$ (exponencial), cada incremento no tamanho do problema multiplicará o uso de recursos por um valor constante.

## 1.2.4. Exponenciação

- Considere uma função que computa o exponencial de um número;
- Essa função deve receber a base $b$ e um expoente positivo inteiro $n$ e computar $b^n$;
- Podemos alcançar esse resultado através da definição recursiva:

$$
\begin{aligned}
	& b^n=b\cdot b^{n-1} \\
	& b^0= 1
\end{aligned}
$$

- Que pode ser traduzido para a função:

```scheme
$> (define (expt b n)
	(if (= n 0)
		1
		(* b (expt b (- n 1)))))
```

- Essa definição de `expt` gera um [processo recursivo linear](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#^linear-rec-proc-ref), que requer $\Theta(n)$ passos e $\Theta(n)$ espaço;
- Podemos reformular essa definição para gerar um [processo iterativo linear](dev/livros/sicp/capitulo-1/subcapitulo-1-2.md#^linear-iter-proc-ref):

```scheme
$> (define (expt b n)
	(expt-iter b n 1))
$> (define (expt-iter b counter product)
	(if (= counter 0)
		product
		(expt-iter b
			(- counter 1)
			(* b product))))
```

- Essa definição requer $\Theta(n)$ passos e $\Theta(1)$ espaço;

---

- Podemos computar exponenciais em menos passos usando elevações sucessivas ao quadrado (_successive squaring_), onde, ao invés de calcularmos $b^8$ como:

$$b\cdot(b\cdot(b\cdot(b\cdot(b\cdot(b\cdot(b\cdot b))))))$$

- Fazemos 3 multiplicações:

$$
\begin{aligned}
	& b^2=b\cdot b \\
	& b^4=b^2\cdot b^2 \\
	& b^8=b^4\cdot b^4
\end{aligned}
$$

- Esse método funciona bem para expoentes que são potências de 2 (e.g., 4, 8, 16, etc.);
- Podemos simplificar ainda mais o cálculo se aplicarmos a regra:

$$
\begin{aligned}
	& b^n=(b^{n/2})^2 ~~ \text{para} ~ n ~ \text{par} \\
	& b^n=b\cdot b^{n-1} ~~ \text{para} ~ n ~ \text{ímpar}
\end{aligned}
$$

- Que é traduzido para a função:

```scheme
$> (define (even? n)
	(= (remainder n 2) 0))
$> (define (fast-expt b n)
	(cond ((= n 0) 1)
		((even? n) (square (fast-expt b (/ n 2))))
		(else (* b (fast-expt b (- n 1))))))
```

- A função `even?` testa se um número é par ou ímpar, verificando se o módulo (resto) da divisão de $n$ e 2 é igual a 0;
	- A função primitiva `remainder` retorna o valor do módulo da divisão entre dois números.

---

- O processo gerado pela função `fast-expt` cresce logaritmicamente com o valor de $n$, tanto em espaço quanto em número de passos;
- Note que, ao calcularmos $b^{2n}$ usando `fast-expt` fazemos uma multiplicação a mais do que ao calcularmos $b^n$;
	- Isso acontece porque $2n$ requer que aconteca uma divisão a mais do que $n$;
	- Além de que $n$ ainda pode não ser par, fazendo com que a divisão por 2 não aconteça.
- Assim, o processo gerado tem ordem de crescimento $\Theta(\log_n)$.

## 1.2.5. Máximo Divisor Comum

- O máximo divisor comum (MDC) de dois números inteiros $a$ e $b$ é definido pelo maior número inteiro que divide ambos e não possui resto;
	- Por exemplo, o MDC de 16 e 28 é 4.
- Uma maneira de encontrar o MDC de dois números é fatorá-los e procurar por fatores comuns;
- Há ainda um algoritmo que se baseia na observação de que, se $r$ é o módulo da divisão de $a$ por $b$, então os divisores comuns de $a$ e $b$ são os mesmos de $b$ e $r$;
- Assim, podemos usar a equação $MDC(a,b)=MDC(b,r)$ para reduzir o valor dos pares de inteiros do problema:

$$
\begin{align}
	MDC(206,40)&=MDC(40,6) \\
	&=MDC(6,4) \\
	&=MDC(4,2) \\
	&=MDC(2,0) \\
	&=2
\end{align}
$$

- Reduzimos $MDC(206,40)$ para $MDC(2,0)$ que é 2;
- Começando com quaisquer números inteiros positivos e reduzindo repetidas vezes sempre produzirá um par onde o primeiro número é o MDC e o segundo número é 0;
- Esse método para calcular o MDC é chamado de **algoritmo de Euclides**;
- Traduzido para uma função:

```scheme
$> (define (mdc a b)
	(if (= b 0)
		a
		(mdc b (remainder a b))))
```

- A função `mdc` gera um processo iterativo, onde o número de passos cresce junto com o logaritmo dos números presentes na operação;
	- A quantidade de passos cresce dessa forma porque sempre calculamos o módulo dos números presentes.
- Assim, sua ordem de crescimento é $\Theta(\log_n)$.

## 1.2.6. Exemplo: Testando Primalidade

- Essa seção descreve dois métodos para checar se um número inteiro $n$ é primo, um com ordem de crescimento $\Theta(\sqrt{n})$, e um algoritmo probabilístico (i.e. baseado em probabilidade) com ordem de crescimento $\Theta(\log_n)$;
- Números primos são números naturais maiores que 1, e divisíveis apenas por 1 e por eles mesmos, possuindo exatamente dois divisores.

### Buscando divisores

- Uma das formas de testar se um número é primo é encontrando seus divisores;
- A função a seguir encontra o menor divisor (maior que 1) de um dado número $n$ de forma bem direta, sucessivamente testando números para verificar se são divisores de $n$, começando por 2:

```scheme
$> (define (smallest-divisor n) (find-divisor n 2))
$> (define (find-divisor n divisor)
	(cond ((> (square divisor) n) n)
		  ((divides? divisor n) divisor)
		  (else (find-divisor n (+ divisor 1)))))
$> (define (divides? a b) (= (remainder b a) 0))
```

- Agora, podemos testar se o número é primo caso ele, e somente ele, seja seu próprio menor divisor:

```scheme
$> (define (prime? n) (= n (smallest-divisor n)))
```

- O caso-base de `find-divisor` é o fato de que, se $n$ não for primo, ele terá um divisor $d$ menor ou igual a $\sqrt{n}$;
	- A condição `(> (square divisor) n)` de `find-divisor` se traduz para $d^2>n$, que é o mesmo que $d>\sqrt{n}$.
- Isso significa que apenas precisamos comparar divisores entre 1 e $\sqrt{n}$ ;
- Assim, esse processo tem ordem de crescimento $\Theta(\sqrt{n})$.

### O teste de Fermat

[//]: #(TODO: revisar)

- O método probabilístico com ordem de crescimento $\Theta(\log_n)$ é baseado em um resultado das teorias do números conhecido como **o pequeno teorema de Fermat**, que diz:

> Se $n$ for um número primo e $a$ for qualquer número positivo inteiro menor que $n$, então $a$ elevado a potência de $n$ é congruente (igual) a $a$ módulo de $n$";

- Se $n$ não for primo, então números $a<n$ (números inteiros positivos menores que $n$) não vão satisfazer a relação;
- Assim, a descrição do algoritmo será:
	- Dado um número $n$, escolha um número aleatório $a<n$ e compute o módulo de $a^n$ por $n$;
	- Se o resultado for diferente de $a$, então $n$ não é primo; Se o resultado for igual a $a$, então existem chances de $n$ ser primo;
	- Escolha outro número aleatório $a$ e aplique o mesmo metódo, se ele também satisfazer a equação então existem ainda mais chances de $n$ ser primo;
	- Quanto mais valores de $a$ satisfazerem a equação, mais chances de $n$ tem de ser um número primo.

---

- Para implementarmos o teste de Fermat precisamos de uma função que computa o exponencial de um número pelo módulo de outro:

```scheme
$> (define (expmod base exp m)
	(cond ((= exp 0) 1)
		  ((even? exp)
			  (remainder
				  (square (expmod base (/ exp 2) m))
				  m))
		  (else
			  (remainder
				  (* base (expmod base (- exp 1) m))
				  m))))
```

- A função `expmod` usa sucessivas elevações ao quadrado, assim seu número de passos cresce logaritmicamente com o expoente;
- O teste de Fermat é feito escolhendo um número aleatório entre 1 e $n-1$, e checando se o módulo de $n$ por $a$ elevado a potência de $n$ é igual a $a$;
- Para obtermos um número aleatório entre 1 e $n-1$ usaremos a função primitiva `random` que retorna um número positivo, inteiro e menor do que o input;
	- Assim, para retornarmos um número aleatório entre 1 e $n-1$, executamos `random` passando $n-1$ e somando 1 ao resultado para evitar que 0 seja retornado.

```scheme
$> (define (fermat n)
	(define try a) (= (expmod a n n) a)
	(try (+ 1 (random (- n 1)))))
$> (define (fast-prime? n times)
	(cond ((= times 0) true)
		  ((fermat n) (fast-prime? n (- times 1)))
		  (else false)))
```

#### Métodos probabilísticos

- O teste de Fermat é diferente da maioria dos algoritmos comuns;
- Enquanto nos outros é garantido que a resposta esteja correta, nele a resposta obtida provalvelmente está correta;
- Caso falhe no teste, $n$ com certeza não é um número primo, mas, mesmo que passe, não é garantido que $n$ seja primo;
- Se realizarmos o teste vezes suficientes e $n$ sempre passar, então a probabilidade de erro é muito baixa;
- Porém, existem números que passam o teste de Fermat mesmo sem serem primos;
	- Isso é, números $n$ que não são primos e ainda têm a propriedade de que $a^n$ é congruente a $a$ módulo $n$ para todos os inteiros $a<n$.
- Esses números são extremamente raros então, na prática, o teste de Fermat é confiável;
	- Números que "enganam" o teste de Fermat são chamados de **números de Carmichael**, havendo 255 números de Carmichael abaixo de 100.000.000.
- Algoritmos onde a quantidade de testes diminui a chance de erro são chamados **algoritmos probabilísticos**.

[^1]: SHINN, Alex; COWAN, John; GLECKLER, Arthur A. [r7rs — Revised 7 Report on the Algorithmic Language Scheme. p. 11](https://standards.scheme.org/official/r7rs.pdf#page=11). Acesso em 26/01/2025.
