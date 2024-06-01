## Public Key Encryption
### Asymmetric Cryptography
Due idee:
- $E(private-key_{Alice}, m) = c, D(public-key_{Alice}, c) = m$
- $E(public-key_{Alice}, m) = c, D(private-key_{Alice}, c) = m$

**Idea generale(Confidentiality):**
- Bob pubblica la sua chiave pubblica e si tiene la sua corrispondente chiave privata segreta
- Alice cifra il messaggio con la chiave pubblica di Bob e l'algoritmo di cifratura
- Bob decifra il messaggio con la sua chiave privata e l'algoritmo di decifratura

*Differenza con la cifratura simmetrica:*
- la chiave pubblica è diversa dalla chiave privata
- un attaccante non può trovare la chiave privata conoscendo la chiave pubblica
- Alice e Bob non devono scambiarsi chiavi segrete
- basta una coppia di chiavi per comunicare con tutti

### Public Key Encryption
**Definizione:** 
Un sistema di cifratura a chiave pubblica è una tripla di algoritmi $(G, E, D)$, dove:
- $G()$ è un algoritmo randomizzato che genera una coppia di chiavi $(p_{k}, s_{k})$
- $E(p_{k}, m)$ è un algoritmo randomizzato che cifra il messaggio $m \in M$ e restituisce il ciphertext $c \in C$
- $D(s_{k}, c)$ è un algoritmo che decifra il ciphertext $c \in C$ e restituisce il messaggio $m \in M or \perp$

*Consistency:* $\forall (p_{k}, s_{k}) outputted$ da $G()$ : $\forall m \in M : D(s_{k}, E(p_{k}, m)) = m$

**Sicurezza:**
Per b = 0, 1, definiamo gli esperimenti EXP(0) e EXP(1) come segue:
- il Challenger genera una coppia di chiavi $(p_{k}, s_{k}) \leftarrow G()$
- il Challenger restituisce $p_{k}$ ad Adversary A
- A produce un messaggio $m_{0}$ e un messaggio $m_{1}$ tali che $|m_{0}| = |m_{1}|$ e li invia al Challenger
- il Challenger sceglie un bit b casualmente e calcola $c = E(p_{k}, m_{b})$ e lo invia ad A
- A produce un bit b'

*Definizione:* 
Un sistema di cifratura a chiave pubblica $\mathcal{E} = (G, E, D)$ è sicuro se $\forall$ efficienti A:
$$Adv_{SS}[A, \mathcal{E}] = |Pr[EXP(0) = 1] - Pr[EXP(1) = 1]| \leq \epsilon$$

**Relazione con la cifratura simmetrica:**
Per la cifratura simmetrica si ha:
- One-Time security $\not\Rightarrow$ Many-Time security(CPA) : non è sufficiente la sicurezza a tempo singolo per garantire la sicurezza a tempo multiplo, poiché l'attaccante può fare query multiple e confrontare i risultati
Per la cifratura asimmetrica si ha:
- One-Time security $\Rightarrow$ Many-Time security(CPA) : la sicurezza a tempo singolo è sufficiente per garantire la sicurezza a tempo multiplo, poiché l'attaccante cripta con la chiave pubblica e non può decifrare
- La cifratura a chiave pubblica DEVE essere randomizzata, altrimenti l'attaccante può fare query multiple e confrontare i risultati

**Sicurezza contro attacchi attivi:**
Se un attaccante può intercettare e modificare i messaggi, allora ha il decriptaggio di messaggi inviati da lui stesso(esempio: email che iniziano con "to: attacker").

*Chosen Ciphertext Security(CCA):*
$\mathcal{E} = (G, E, D)$ criptaggio a chiave pubblica su (M, C), per b = 0, 1 definiamo EXP(b) come segue:
- il Challenger genera una coppia di chiavi $(p_{k}, s_{k}) \leftarrow G()$ e invia $p_{k}$ ad A
- CCA phase 1: A produce un ciphertext $c_{i} \in C$ e lo invia al Challenger
- il Challenger decifra $c_{i}$ e restituisce il messaggio $m_{i} = D(s_{k}, c_{i})$ ad A
- challenge: A produce due messaggi $m_{0}, m_{1}$ tali che $|m_{0}| = |m_{1}|$ e li invia al Challenger
- il Challenger sceglie un bit b casualmente e calcola $c = E(p_{k}, m_{b})$ e lo invia ad A
- CCA phase 2: A invia un ciphertext $c_{i}$, con $c_{i} \neq c$, al Challenger
- il Challenger decifra $c_{i}$ e restituisce il messaggio $m_{i} = D(s_{k}, c_{i})$ ad A
- A produce un bit b'

In pratica: l'attaccante invia un ciphertext $c_{i}$ e il Challenger lo decifra, così ha il decriptaggio di messaggi inviati da lui stesso da utilizzare nel challenge. Nella challenge invia due messaggi $m_{0}, m_{1}$ e il Challenger cifra uno dei due messaggi e lo invia all'attaccante. L'attaccante deve indovinare quale dei due messaggi è stato cifrato. Successivamente l'attaccante invia un ciphertext $c_{i}$ diverso da quello cifrato dal Challenger e il Challenger lo decifra. Questo simula un attacco in tempo reale dove l'attaccante può intercettare e modificare i messaggi basandosi sui messaggi che ha già decriptato.

*Definizione:*
Un sistema di cifratura a chiave pubblica $\mathcal{E} = (G, E, D)$ è sicuro contro attacchi attivi se $\forall$ efficienti A:
$$Adv_{CCA}[A, \mathcal{E}] = |Pr[EXP(0) = 1] - Pr[EXP(1) = 1]| \leq \epsilon$$

*Esempio:*
- il Challenger genera una coppia di chiavi $(p_{k}, s_{k}) \leftarrow G()$ e invia $p_{k}$ ad A
- challenge: A produce due messaggi {$to: Alice, 0$}, {$to: Alice, 1$} e li invia al Challenger
- il Challenger sceglie un bit b casualmente e calcola $c = E(p_{k}, m_{b})$ e lo invia ad A
- CCA phase 2: A invia un ciphertext c' = {$to: David, b$} $\neq c$ al Challenger
- il Challenger decifra $c'$ e restituisce il messaggio $m' = D(s_{k}, c')$ ad A
- A produce un bit b'

**Attacchi attivi: simmetrica vs asimmetrica**
- Simmetrica: l'attaccante non può creare ciphertext, quindi è sicura contro CCA
- Asimmetrica: l'attaccante può creare ciphertext, quindi dobbiamo implementare un sistema di sicurezza CCA

### Trapdoor Functions(TDF)
**Definizione:**
Una funzione trapdoor $X \rightarrow Y$ è una tripla di algoritmi efficienti $(G, F, F^{-1})$, dove:
- $G()$ è un algoritmo randomizzato che genera una coppia di chiavi $(p_{k}, s_{k})$
- $F(p_{k}, .)$ è un algoritmo deterministico che definisce una funzione $X \rightarrow Y$
- $F^{-1}(s_{k}, .)$ definisce la funzione $Y \rightarrow X$ inversa di $F(p_{k}, .)$

*Precisamente:*
$\forall (p_{k}, s_{k})$ outputted da $G()$ : $\forall x \in X : F^{-1}(s_{k}, F(p_{k}, x)) = x$

**Sicurezza:**
(G, F, F^{-1}) è sicuro se $F(p_{k}, .)$ è una one-way function: può essere calcolata facilmente, ma non può essere invertita senza la chiave segreta $s_{k}$
- il Challenger genera una coppia di chiavi $(p_{k}, s_{k}) \leftarrow G()$ e una $x \in X$
- il Challenger calcola $y = F(p_{k}, x)$ e lo invia ad A insieme a $p_{k}$
- A produce un $x'$

*Definizione:*
(G, F, F^{-1}) è un TDF sicuro se $\forall$ efficienti A:
$$Adv_{OW}[A, F] = |Pr[ x = x'] \leq \epsilon$$

**Hash Functions:**
- input: stringa di lunghezza arbitraria
- output: stringa di lunghezza fissa

*One-way Hash Algorithm:*
Un algortimo one-way di hash $H(.)$ prende in input un documento e lo condensa in un hash di lunghezza fissa, più piccolo del documento(per esempio 256 bit). 
H(.):
- input: m - una stringa di lunghezza arbitraria
- output: H(m) - una stringa binaria di L bit(L è fissa per un one-way hash)
- esempi: "MD5" ha L = 128, "SHA-1" ha L = 160

*Proprietà di una one-way hash function:*
- facile da calcolare
- difficile da invertire, difficile trovare h tale che H(m) = h
- difficile trovare due messaggi m, m' tali che H(m) = H(m')
- un piccolo cambiamento nel documento produce un così grande cambiamento che i due hash paiono non avere nulla in comune

**Crittografia a chiave pubblica con TDFs:**
- ($G, F, F^{-1}$) un TDF sicuro $X \rightarrow Y$
- ($E_{S}, D_{S}$) una cifratura simmetrica autenticata definita su (K, M, C)
- ($H : X \rightarrow K$) una one-way hash function

Dobbiamo costruire un sistema di cifratura a chiave pubblica (G, E, D), dove G() genera una coppia di chiavi come la G() del TDF

$E(p_{k}, m):$
- $x \leftarrow X$, $y \leftarrow F(p_{k}, x)$
- $k \leftarrow H(x)$, $c \leftarrow E_{S}(k, m)$
- output: $y, c$

$D(s_{k}, (y, c)):$
- $x \leftarrow F^{-1}(s_{k}, y)$
- $k \leftarrow H(x)$, $m \leftarrow D_{S}(k, c)$
- output: $m$

Visto meglio:
- header: $y = F(p_{k}, x)$
- body: $c = E_{S}(H(x), m)$

*Sicurezza:*
Se ($G, F, F^{-1}$) è un TDF sicuro, ($E_{S}, D_{S}$) è una cifratura simmetrica autenticata sicura e $H: X \rightarrow K$ è un oracolo randomico, allora il sistema di cifratura a chiave pubblica (G, E, D) è sicuro CCA.

*Esempio di Utilizzo Errato di un TDF:*
Non bisogna applicare direttamente F al testo in chiaro, dato che è deterministico e quindi non sarebbe semanticamente sicuro. 
$E(p_{k}, m):$
- output: $F(p_{k}, m)$

$D(s_{k}, c):$
- output: $F^{-1}(s_{k}, c)$

### RSA Trapdoor Permutation
**Key Generation:**
La funzione G() genera una coppia di chiavi $(p_{k}, s_{k})$ come segue:
- scegli due numeri primi grandi $p, q$
- calcola $N = pq$
- sceglie *e*( tale che $gcd(e, \phi(N) = 1)$) e *d* tali che $ed \equiv 1 \mod \phi(N)$, dove $\phi(N) = (p-1)(q-1)$
- $p_{k} = (N, e)$, $s_{k} = (N, d)$

**Encryption:**
- $F(p_{k}, x) : \mathbb{Z}_{N}^{*} \rightarrow \mathbb{Z}_{N}^{*}$
- RSA(x) = $x^{e} \mod N$

**Decryption:**
- $F^{-1}(s_{k}, y) = y^{d} \mod N$
- $y^{d} \equiv RSA(x)^{d} \equiv x^{ed} \equiv x^{k\phi(N) + 1} \equiv (x^{\phi(N)})^{k}x \equiv x \mod N$

**Esempio:**
Bob genera una coppia di chiavi $(p_{k}, s_{k})$:
- sceglie $p = 5, q = 11, N = 55, \phi(N) = 40$
- sceglie $e = 3 : gcd(3, 40) = 1$
- calcola $d = 27 : 3*27 \mod 40 \equiv 1$
- $p_{k} = (55, 3), s_{k} = (55, 27)$

Alice cifra un messaggio m = 13:
- trova la chiave pubblica di Bob $p_{k} = (55, 3)$
- calcola $c = m^{e} \mod N = 13^{3} \mod 55 = 52$
- invia il ciphertext c = 52 a Bob

Bob decifra il messaggio:
- riceve il ciphertext c = 52
- calcola $m = c^{d} \mod N = 52^{27} \mod 55 = 13$

**RSA assumption:**
L'RSA è una one-way permutation, cioè è facile calcolare RSA(x) ma è difficile calcolare x dato RSA(x) senza la chiave privata.
$$\forall \text{ efficiente algoritmo A}: |Pr[A(N, e, y) = y^{1/e}] \leq \epsilon$$
Dove A è un algoritmo che prende in input N, e, y e restituisce x tale che $y = x^{e} \mod N$, $y \in \mathbb{Z}_{N}^{*}$

**Review RSA ISO Standard:**
- ($E_{S}, D_{S}$) è una cifratura simmetrica autenticata
- $H: \mathbb{Z}_{N} \rightarrow K$ è una one-way hash function, dove K è lo spazio delle chiavi di $E_{S}, D_{S}$
- G() genera i parametri RSA: $pk = (N, e), sk = (N, d)$
- $E(p_{k}, m):$
    - $x \leftarrow \mathbb{Z}_{N}$ scelto casualmente
    - $y \leftarrow RSA(x) = x^{e} \mod N$, $k \leftarrow H(x)$ 
    - output: $y, E_{S}(k, m)$
- $D(s_{k}, (y, c)):$
    - output: $D_{S}(H(RSA^{-1}(y)), c) = m$

**Semplice Attacco:**
Supponiamo che la chiave di sessione da criptare sia di 64 bit: $k \in \{0,...,2^{64}\}$
L'attaccante vede il ciphertext $c = RSA(k) = k^{e} \mod N$
Se $k = k_1*k_2$, dove $k_1, k_2 < 2^{34}$ con probabilità $\approx 20\%$, allora $c/k_1^{e} = k_2^{e} \mod N$
*Meet-in-the-middle attack:*
- costruisce una tabella di $2^{34}$ valori: $c/1^{e}, c/2^{e}, ..., c/2^{34e}$ (O($2^{34}$))
- per $k_2 = 0, 1, ..., 2^{34}$ calcola $k_2^{e} \mod N$ e cerca il valore in tabella (O($2^{34}$)) 
- se trova un match, allora $k = k_1*k_2$

Il tempo totale è $\approx 2^{40} << 2^{64}$, quindi l'attacco è fattibile.

**RSA è una one-way permutation?**
Per invertire RSA senza la chiave privata, l'attaccante deve trovare $x$ tale che $c = x^{e} \mod N$.
*Quant'è difficile trovare la e-th root di c in* $\mathbb{Z}_{N}, \frac{c^{\frac{1}{e}}}{\sqrt[e]{c}}$*?*
Il miglior algoritmo conosciuto per trovare l'e-th root di c in $\mathbb{Z}_{N}$ è composta da due passaggi:
- fattorizzare N in $p, q$, difficile se $N = pq$ è grande
- calcolare la e-th root di c modulo $p, q$, facile

**Come non migliorare RSA:**
Usare una chiave segreta piccola(d $\approx 2^{128}$) per velocizzare la decifrazione($c^{d} \mod N$ = m).

- Wiener: se d < N^{0.25}, allora RSA è facilmente invertibile da (N, e)

**Attacco di Wiener:**
(//TODO: vedere esempio)

### RSA in Practice
**RSA con e piccolo:**
Se utilizzo *e* piccolo rendo la cifratura più veloce.
- $e = 3 (gcd(3, \phi(N)) = 1)$ è il valore più piccolo possibile
- $e = 2^{16} + 1 = 65537$ è un valore comune, il criptaggio deve fare solo 17 moltiplcazioni 
Spesso è accompagnato da una decifratura lenta.

**Key length:**
La sicurezza di un sistema a chiave pubblica dovrebbe essere comparabile a quella di un sistema a chiave simmetrica.
- 256 bit (AES) $\approx$ 15360 bit (RSA)

**Attacchi di implementazione:**
- timing attack: il tempo per eseguire $c^{d} \mod N$ dipende da d
- power attack: il consumo di potenza per eseguire $c^{d} \mod N$ dipende da d
- fault attack: un errore durante l'esecuzione di $c^{d} \mod N$ può rivelare d

*Esempio di fault attack:*
Di solito si divide il decriptaggio di un messaggio $x = c^{d} \mod N$ in due parti:
- $x_p = c^{d} \mod p$
- $x_q = c^{d} \mod q$
Supponimao che avvenga un errore durante il calcolo di $x_q$, quindi:
- l'output è $x' = c^{d} \mod p$, quindi $x'^{e} = c$ in $\mathbb{Z}_{p}$
- l'output è $x' \neq c^{d} \mod q$, quindi $x'^{e} \neq c$ in $\mathbb{Z}_{q}$
- $gcd(x'^{e} - c, N) = p$

**RSA Key Generation Trouble:**
Con poca entropia, la stessa p può essere generata più volte, che partendo da due dispositivi diversi con chiavi ($N_1, N_2$) posso calcolare $gcd(N_1, N_2) = p$.

### Digital Signatures
(//TODO: vedere slide)
**Esempio:**
Bob genera una coppia di chiavi $(p_{k}, s_{k})$:
- sceglie $p = 5, q = 11, N = 55, \phi(N) = 40$
- sceglie $e = 3 : gcd(3, 40) = 1$
- calcola $d = 27 : 3*27 \mod 40 \equiv 1$
- $p_{k} = (55, 3), s_{k} = (55, 27)$

Bob ha un messaggio m = 19 e vuole firmarlo:
- calcola la sua firma digitale $s = m^{d} \mod N = 19^{27} \mod 55 = 24$ utilizzando la sua chiave privata
- appende la firma al messaggio $(m, s) = (19, 24)$

Cathy, un verificatore, riceve la coppia $(m, s) = (19, 24)$:
- trova la chiave pubblica di Bob $p_{k} = (55, 3)$
- calcola $t = s^{e} \mod N = 24^{3} \mod 55 = 19$
- verifica che $t = m$
- conferma che $(m, s)$ è stato firmato da Bob

*Se il documento è lungo?*
La firma digitale viene calcolata su un hash del documento invece che sul documento stesso.

*Perché la firma digitale?*
- unforgeable: ci vuole la chiave privata per firmare
- un-deniable: non si può negare di aver firmato
- verificabile universalmente
- differisce tra due messaggi diversi

### Asymmetric Cryptosystems
**ElGamal Cryptosystem:**
Lo schema di crittografia è basatp sul protocollo Diffie-Hellman:
- Bob: $G()$ genera una coppia di chiavi $(p_{k}, s_{k})$ come segue:
    - sceglie un numero primo grande p e un generatore g di $\mathbb{Z}_{p}^{*}$
    - sceglie un numero casuale $a \in [0, p-2]$
    - calcola $A = g^{a} \mod p$
    - $p_{k} = (p, g, A)$, $s_{k} = a$
- Alice: $E(p_{k}, m): dove (0 <= m < n)$
    - trova la chiave pubblica di Bob $p_{k} = (p, g, A)$
    - sceglie un numero casuale $b \in [0, p-2]$
    - calcola $B = g^{b} \mod p$
    - calcola $c = m * A^{b} \mod p$
    - invia $B, c$ a Bob
- Bob: $D(s_{k}, (B, c)):$
    - riceve $B, c$ da Alice
    - usa la sua chiave privata $a$ per calcolare $m$:
        - $x = p-1-a$
        - $m = c * B^{x} \mod p$
E' un sistema di crittografia randomizzato.
Efficienza: il ciphertext è 2 volte più lungo del plaintext.
Sicurezza: la sicurezza dipende dalla difficoltà del problema del logaritmo discreto.

**Rabin Cryptosystem:**
- Bob: $G()$ genera una coppia di chiavi $(p_{k}, s_{k})$ come segue:
    - sceglie due numeri primi grandi $p, q$ tali che $p (mod 4) = q (mod 4) = 3$
    - calcola $N = pq$
    - $p_{k} = N$, $s_{k} = (p, q)$
- Alice: $E(p_{k}, m):$
    - trova la chiave pubblica di Bob $p_{k} = N$
    - calcola $c = m^{2} \mod N$
- Bob: $D(s_{k}, c):$
    - riceve $c$ da Alice
    - utilizza la sua chiave privata $(p, q)$ per calcolare $m$:
        - $m_p = c^{(p+1)/4} \mod p$
        - $m_q = c^{(q+1)/4} \mod q$
        - trova $y_p, y_q$ tali che $y_p + p*y_q = 1$ (algoritmo di Euclide esteso)
        - $r = (y_p*p*m_q + y_q*q*m_p) \mod N$
        - $s = (y_p*p*m_q - y_q*q*m_p) \mod N$
        - uno tra $r, -r, s, -s$ è $m$
Efficienza: criptaggio più veloce di RSA.
Sicurezza: il problema su cui si basa la sicurezza è difficile quanto la fattorizzazione di N.
    
    