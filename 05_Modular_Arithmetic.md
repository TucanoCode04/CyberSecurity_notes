## Introduction Number Theory
**Notation:**
- $N$: numero intero positivo
- $p$: numero primo
- $\mathbb{Z}_{N} = \{0, 1, ..., N-1\}$: insieme degli interi positivi con operazioni di somma e prodotto modulo N
- $\mathbb{Z}_{N}^{*} = \{x \in \mathbb{Z}_{N} | gcd(x, N) = 1\}$: insieme degli elementi invertibili in $\mathbb{Z}_{N}$
    - Esempio: $\mathbb{Z}_{12}^{*} = \{1, 5, 7, 11\}$ ( se fai tutti i calcoli in questo caso ognuno è l'inverso di se stesso($1*1 = 1, 5*5 = 25 mod 12 = 1, 7*7 = 49 mod 12 = 1, 11*11 = 121 mod 12 = 1$))
               $per p primo, \mathbb{Z}_{p}^{*} = \{1, 2, ..., p-1\} = \mathbb{Z}_{p} - \{0\}$

**Modular Arithmetic:**
- 9 + 8 = 5, in $\mathbb{Z}_{12}$
- 5 * 7 = 11, in $\mathbb{Z}_{12}$
- 5 - 7 = 10, in $\mathbb{Z}_{12}$

**Greatest Common Divisor:**
*Definizione:* $gcd(a, b)$ è il più grande intero positivo che divide sia a che b.
- $gcd(12, 18) = 6$
- $gcd(12, 17) = 1$(sono coprimi)

*Fatti:*
Per ogni $x, y \in \mathbb{Z}$, esistono $s, t \in \mathbb{Z}$ tali che $gcd(x, y) = s*x + t*y$, dove $s, t$ possono essere calcolati con l'algoritmo di Euclide.
- $gcd(12, 18) = 6 = 12*(-1) + 18*1$

**Modular Inversion:**
*Definizione:* l'inverso di x in $\mathbb{Z}_{N}$ è un intero y(scritto anche $x^{-1}$) tale che $x*y \equiv 1 (mod N)$.
- se prendiamo N dispari, l'inverso di 2 in $\mathbb{Z}_{N}$ è $\frac{N+1}{2}$, perchè $2*\frac{N+1}{2} = N+1 \equiv 1 (mod N)$

*Lemma:* l'inverso di x in $\mathbb{Z}_{N}$ esiste se e solo se $gcd(x, N) = 1$.

**Modular Linear Equations:**
Per risolvere $ax + b \equiv 0$ in $\mathbb{Z}_{N}$:
*Soluzione:* $x \equiv -b*a^{-1} (mod N)$, dove $a^{-1}$ è trovato con l'algoritmo di Euclide. La soluzione si trova in tempo $O(log^2 N)$.

**Fermat's Theorem:**
*Teorema:* dato p primo , $\forall x \in \mathbb{Z}_{p}^{*} : x^{p-1} \equiv 1 in \mathbb{Z}_{p}$.
*Esempio:* $p = 5, 3^{5-1} = 3^4 = 81 \equiv 1 in \mathbb{Z}_{5}$   
*Applicazione: Calcolo dell'inverso:* $x*x^{p-2} \equiv 1 \rightarrow x^{-1} = x^{p-2}$
*Applicazione: Generazione di numeri primi casuali:* 
- vogliamo generare un numero primo p di 1024 bit($p \approx 2^{1024}$)
- scegliamo un numero casuale $p \in [2^{1024}, 2^{1025}-1]$
- controlliamo se $2^{p-1} \equiv 1 in \mathbb{Z}_{p}$, se si allora p è primo altirmenti scegliamo un altro p.
- la probabilità che un numero composto passi il test è $Pr[p non primo] < 2^{-60}$

**Struttura di $\mathbb{Z}_{p}^{*}$:**
*Teorema di Eulero:* $\mathbb{Z}_{p}^{*}$ è un gruppo ciclico, cioè $\exists g \in \mathbb{Z}_{p}^{*} : \{g^0(1), g^1, g^2, ..., g^{p-2}\} = \mathbb{Z}_{p}^{*}$.
- $g$ è detto generatore di $\mathbb{Z}_{p}^{*}$.
*Esempio:* $p = 7, \mathbb{Z}_{7}^{*} = \{1, 2, 3, 4, 5, 6\}$
- $3$ è un generatore di $\mathbb{Z}_{7}^{*}$ perchè $\{3^0(1), 3^1(3), 3^2(2), 3^3(6), 3^4(4), 3^5(5)\} = \mathbb{Z}_{7}^{*}$
- $2$ non è un generatore di $\mathbb{Z}_{7}^{*}$ perchè $\{2^0(1), 2^1(2), 2^2(4), 2^3(1), 2^4(2), 2^5(4)\} \neq \mathbb{Z}_{7}^{*}$

**Ordine:**
Per $g \in \mathbb{Z}_{p}^{*}$ l'insieme $\{g^0(1), g^1, g^2, ..., g^{p-2}\}$ è detto gruppo ciclico generato da g, denotato come $\langle g \rangle$.
*Definizione:* l'ordine di $g \in \mathbb{Z}_{p}^{*}$ è la grandezza di $\langle g \rangle$, denotato come $ord_{p}(g) = |\langle g \rangle| = (smallest a > 0 : g^a \equiv 1 in \mathbb{Z}_{p})$.
- $ord_{7}(3) = 6$, $ord_{7}(2) = 3$

*Teorema di Lagrange:* $\forall g \in \mathbb{Z}_{p}^{*} : ord_{p}(g) \text{ divide } p-1$.
- $ord_{7}(3) = 6 : 6 | 7-1 = 6$, $ord_{7}(2) = 3 : 3 | 7-1 = 6$

**Generalizzazione del Teorema di Fermat, Eulero:**

*Definizione:* per un intero positivo N definiamo $\phi(N) = |(\mathbb{Z}_{N}^{*})|$.

*Esempio:* 
- $\phi(12) = |\{1, 5, 7, 11\}| = 4$, $\phi(p) = p-1$
- per $N = p*q$ con p, q primi, $\phi(N) = N - p - q + 1 = (p-1)(q-1)$

*Teorema di Eulero:* $\forall x \in \mathbb{Z}_{N}^{*} : x^{\phi(N)} \equiv 1 in \mathbb{Z}_{N}$.
- $5^{\phi(12)} = 5^4 = 625 \equiv 1 in \mathbb{Z}_{12}$
- è la generalizzazione del teorema di Fermat, se N è primo allora $\phi(N) = N-1$ e diventa il teorema di Fermat.

**Modular e'th roots:**
*Definizione:* per $x \in \mathbb{Z}_{p} : x^e \equiv c in \mathbb{Z}_{p}$, dove $c \in \mathbb{Z}_{p}$ è detto e-th root di c.
- $7^{\frac{1}{3}} \equiv 6 in \mathbb{Z}_{11}$, perchè $6^3 = 216 \equiv 7 in \mathbb{Z}_{11}$

**Calcolo dell'e'th root:**
Quando esiste $c^{1/e}$ in $\mathbb{Z}_{p}$?
- caso base: se $gcd(e, p-1) = 1$ allora $\forall c \in \mathbb{Z}_{p}^{*} : c^{1/e} \in \mathbb{Z}_{p}$ esiste ed è facile da calcolare.
- caso e=2: se p è primo dispari allora $gcd(2, p-1) \neq 1$
    - la funzione $x \rightarrow x^2$ è una funzione 2 a 1 in $\mathbb{Z}_{p}^{*}$: x e -x hanno lo stesso quadrato. Esempio: $x^2 = 1 in \mathbb{Z}_{11} : 1^2 = 1, 10^2 = 1$
*Definizione:* x in $\mathbb{Z}_{p}$ è il quadrato residuo se ha una radice quadrata in $\mathbb{Z}_{p}$. 
Se p è un numero primo dispari, allora $\frac{p-1}{2} + 1$ numeri in $\mathbb{Z}_{p}$ sono quadrati residui.

**Teorema di Eulero:**
*Teorema:* $x \in \mathbb{Z}_{p}^{*} è un quadrato residuo se e solo se x^{\frac{p-1}{2}} \equiv 1 in \mathbb{Z}_{p}$ se p è un numero primo dispari($x^{\frac{p-1}{2}}$ è detto simbolo di Legendre di x su p).
- $in \mathbb{Z}_{11} : 1^5 = 1, 3^5 = 1, 4^5 = 1, 5^5 = 1, 9^5 = 1$
*Nota:* con $x \neq 0$ ho che $x^{\frac{p-1}{2}} \equiv x^{(p-1)^{1/2}} \equiv 1^{1/2} \in \{1, -1\} \in \mathbb{Z}_{p}$

**Equazioni quadratiche:**
*Lemma:* se $c \in \mathbb{Z}_{p}^{*}$ è un quadrato residuo, allora $\sqrt{c} = c^{\frac{p+1}{4}} in \mathbb{Z}_{p}$.
$$a*x^2 + b*x + c = 0 in \mathbb{Z}_{p}$$
*Soluzione:* $x = \frac{-b \pm \sqrt{b^2 - 4*a*c}}{2*a}$, dove $(2a)^{-1}$ è calcolato con l'algoritmo di Euclide.

**Computare e'th root modulo N:**
Se N è composto e $e > 1$, quando esiste $c^{1/e}$ in $\mathbb{Z}_{N}$?


