## Stream Ciphers
**Cifraggio simmetrico:**
- E: K x M -> C
- D: K x C -> M
$\forall m \in M, \forall k \in K : D(k, E(k, m)) = m$
E è spesso randomizzata, D è deterministica.

**One-time pad:**
K = M = C = {0, 1}$^n$
E(k, m) = k xor m
D(k, c) = k xor c
k è usata una volta sola ed è randomica.

D(k, E(k, m)) = k xor (k xor m) = m
Veloce, ma richiede una chiave lunga quanto il messaggio e non può essere riutilizzata.

**Information theoretic security(Shannon):**
Un cifrario (E, D) su (K, M, C) è perfettamente sicuro se
$\forall m_0, m_1 \in M, (len(m_0) = len(m_1)) : \forall c \in C, Pr[E(k, m_0) = c] = Pr[E(k, m_1) = c]$ dove k è scelto uniformemente da K.
Questo vuol dire che dato c non si può distinguere tra m_0 e m_1, con k scelta a caso.
Questo permette di difendersi dagli attacchi di tipo ciphertext-only.

OTP è perfettamente sicuro, poichè abbiamo che:
$Pr[E(k, m_0) = c] = \frac{numero di k : E(k, m_0) = c}{|K|} = \frac{1}{|K|}$ 
Dove se il numero di k che mappano m_0 in c è costante allora il cifrario è sicuro. 
Poichè 1 chiave mappa m in c.

Il problema del teorema della perfect secrecy è che richiede |K| >= |M|, quindi non è pratico.

### Pseudo-random generators e Stream Ciphers
**Pseudo-random generators:**
Uno stream cipher serve per rimpiazzare l'OTP, per rendere la chiave più corta. Però non rispetta il teorema della perfect secrecy.
PRG è una funzione $G : \{0, 1\}^s -> \{0, 1\}^n$ dove n >> s.

**Attacco 1:**
Il two-time pad è insicuro in quanto l'attaccante può fare:
- c_1 = m_1 xor PRG(k)
- c_2 = m_2 xor PRG(k)
- c_1 xor c_2 = m_1 xor m_2
Questo è successo nel Venona Project.

**Esempi:**
- MS-PPTP: utilizzo della stessa chiave per più messaggi, sia in trasmissione che in ricezione.
- 802.11b WEP: utilizzo di IV(Initialization Vector) di 24 bit, che permette di avere solo 16 milioni di chiavi possibili. Quindi trovando 2 messaggi con stesso IV si ottiene la chiave. Si risolve utilizzando una chiave diversa per ogni messaggio.

**Attacco 2(no integrity):**
L'OTP è malleabile, se l'attaccante può modificare il ciphertext, può modificare il plaintext.
- c = m xor k(Alice)
- c' = c xor p(Eve)
- k xor c' = m xor p(Bob)

**Esempi:**
- cambiare la risposta da 1 a 0: c' = c xor 1
- cambiare la risposta da Alice a Maria: c' = c xor Alice xor Maria

### Real-world Stream Ciphers
**RC4:**
Inizia con un array *s* di 256 byte, inizializzato con 0, poi riempito con 0-255 in ordine. Si fa un mixing di *s* con la chiave *k*(KSA algorithm) e si fa uno stream di byte pseudo-random: ad ogni passo(tanti quanto la lunghezza del plaintext) vengono utilizzati 2 byte di *s*(PRG) per generare un byte di output(che viene poi scambiato con un byte del plaintext).  
E' stato dimostrato che RC4 ha un bias nell'output iniziale: $Pr[2^{nd} byte = 0] = \frac{2}{256} => RC4-drop[n]$.(Mantin e Shamir).
E' inoltre debole ad attacchi sulla chiave(WEP) e la $Pr(0,0) = 1/256^2 + 1/256^3$(Fluhrer e McGrew).

**CSS(Content Scrambling System):**
E' un cifrario stream utilizzato nei DVD, è stato rotto.
Utilizza un LFSR(Linear Feedback Shift Register) che genera la chiave da una funzione di un seed di 40 bits. 

**eStream:**
Viene utilizzato un *nonce*(numero ripetuto una volta sola) in aggiunta alla chiave, in questo modo la chiave può essere riutilizzata.
$PRG: {0, 1}^s x R -> {0, 1}^n$ dove R è il nonce.
$E(k, m, r) = PRG(k, r) xor m$

**Salsa20:**
${0,1}^{128 or 256} x {0,1}^{64} -> {0,1}^n$ dove max n = $2^{73}$.
$Salsa20(k, r) = H(k, r, 0) || H(k, r, 1) || ...$ dove H è una funzione di hash invertibile applicata 10 volte.

### Quand'è sicuro un PRG?
Un PRG dev'essere sicuro e non prevedibile, quindi deve essere indistinguibile da una funzione random.
Un PRG è predicibile se:
$\exists A(algoritmo efficiente) \land \exists 1<=i<=n-1 : Pr[A(G(k)|_{1...i}) = G(k)|_{i+1}] > \frac{1}{2} + \epsilon$ dove k è scelto uniformemente da K ed epsilon è trascurabile($\epsilon = 1/2^{30}$).
Un PRG è sicuro se non è predicibile:
$\forall i$ non esiste A che predice G(k)|_{i+1} per un $\epsilon$ trascurabile.

Si definisce anche sicuro un algoritmo dove:
$k \leftarrow K, output G(k)$ è indistinguibile da $r \leftarrow {0,1}^n, output r$ dove per esempio K è ${0,1}^10$ ed il generatore casuale ha n = 1000.
Da cui *s* dev'essere abbastanza grande.

**Test Statistico:**
Dato un algoritmo $A : {0,1}^n -> {0,1}$, si definisce il test statistico di A su $G : K -> {0,1}^n$ come:
$Adv_{PRG}[A, G] = |Pr[A(G(k)) = 1] - Pr[A(r) = 1]|$ dove k è scelto uniformemente da K e r è scelto randomicamente da {0,1}^n.
- Se $Adv_{PRG}[A, G] \sim 0$ allora A non può distinguere G da una funzione random.
- Se $Adv_{PRG}[A, G]$ è non trascurabile allora A può distinguere G da una funzione random.
- Se $Adv_{PRG}[A, G] \sim 1$ allora A può distinguere G da una funzione random.

**Esempio:**
$G: K -> {0,1}^n$ soddisfa $msb(G(k)) = 1$ per 2/3 dei k, il test statistico è $if msb(x) = 1 then 1 else 0$.
$Pr[A(G(k)) = 1] = 2/3, Pr[A(r) = 1] = 1/2 => Adv_{PRG}[A, G] = 1/6$. Quindi non è sicuro.

Da cui una nuova definizione di sicurezza è:
Un PRG è sicuro se $\forall A : Adv_{PRG}[A, G] \leq \epsilon$ dove $\epsilon$ è trascurabile.

Dimostriamo che se un PRG è predicibile allora è non sicuro:
$Pr[A(G(k)) = 1] = \frac{1}{2} + \epsilon, Pr[A(r) = 1] = \frac{1}{2} => Adv_{PRG}[A, G] = \epsilon$.

**Teorema:**
Se $\forall i \in {1,2,...,n-1} : G(k)|_{1...i}$ è non predicibile allora G è sicuro.
Più in generale, $\forall A (test statistic), P1 = Pr[A(X) = 1] - Pr[A(Y) = 1] \leq \epsilon$ dove X e Y sono due distribuzioni su {0,1}^n, allora X e Y sono indistinguibili.

### Sicurezza Semantica
Se ho un attaccante che può fare solo attacchi CT(Chiper Text), le possibili misure di sicurezza sono:
- l'attaccare non può recuperare la chiave.
- l'attaccante non può recuperare il plaintext.
Da cui Shannon dice che CT non deve rivelare informazioni sul PT.

**Sicurezza Semantica(one-time key):**
Per un cifrario Q = (E, D) e un attaccante A, si definisce il vantaggio di A su Q come:
$Adv_{SS}[A, Q] = |Pr[EXP(0) = 1] - Pr[EXP(1) = 1]|$ dove EXP è un esperimento che simula l'attacco di A su Q.
A sceglie due messaggi m_0 e m_1, il challenger cripta i messaggi E(k, m_b) = c, A riceve c e deve indovinare b. Se A indovina b allora EXP = 1 altrimenti 0.
Q è sicuro se $\forall A : Adv_{SS}[A, Q] \leq \epsilon$ dove $\epsilon$ è trascurabile.

**Esempio:**
OTP è sicuro, poichè $Adv_{SS}[A, OTP] = |Pr[A(k xor m_0) = 1] - Pr[A(k xor m_1) = 1]| = 0$ poichè k è scelto randomicamente.

**Teorema:**
G è un PRG sicuro da test statistici allora il cifrario stream Q derivato da G è sicuro da attacchi che possono fare solo attacchi CT.
$\forall A(attaccante di sicurezza semantica), \exists B(attaccante al PRG) : Adv_{SS}[A, Q] \leq 2*Adv_{PRG}[B, G]$