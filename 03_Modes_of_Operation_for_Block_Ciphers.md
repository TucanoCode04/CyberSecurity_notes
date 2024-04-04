## Modes of Operation(using Block Ciphers)
### Modes of Operation One-Time Key
*Esempio:*
E-mail criptate utilizzando una nuova chiave per ogni messaggio.

**Utilizzando PRPs e PRFs:**
Si vuole costruire costruire una crittografia da un PRP sicuro(ese. AES).
L'avversario vede solo il testo cifrato(One-Time Key) e vuole trovare informazioni sul testo in chiaro(Sicurezza Semantica).

**ECB encryption mode:**
- si divide il messaggio in blocchi indipendenti(ECB), ogni blocco è cifrato separatamente utilizzando la stessa chiave.

Il problema di questo metodo è che se due blocchi sono uguali(b1 = b2), allora il testo cifrato sarà lo stesso(c1 = c2).

*Crittoanalisi di ECB:*
- deterministico: stesso testo in chiaro produce lo stesso testo cifrato(stesso problema del cifrario di Vigenère).
- semanticamente insicuro: se due blocchi sono uguali, allora il testo cifrato sarà lo stesso.

*Esempio di perchè ECB è insicuro:*
- $m_0 = "Hello World"$
- $m_1 = "Hello Hello"$
- I messaggi sono divisi in blocchi. Il primo blocco è uguale per entrambi i messaggi.
- A capisce facilmente se gli è stato inviato $m_0$ o $m_1$ poichè $m_1$ ha due blocchi uguali. Quindi $ADV_{SS}[A, ECB] = 1$.

*Metodo di conta deterministica:*
- $PRF F : K x {0, 1}^n -> {0, 1}^n$
- $E_{DETCTR}(k, m) = m[0] xor F(k, 0) || m[1] xor F(k, 1) || ... || m[l] xor F(k, l) = c[0] || c[1] || ... || c[l]$(F fa lo xor con il contatore).
- $D_{DETCTR}(k, c) = c[0] xor F(k, 0) || c[1] xor F(k, 1) || ... || c[l] xor F(k, l) = m[0] || m[1] || ... || m[l]$(F fa lo xor con il contatore, quindi non c'è bisogno di invertire F).

*Sicurezza:*
Teorema: $\forall L > 0$, se F è una PRF semanticamente sicura su (K, X, X) allora $DETCTR$ è semanticamente sicuro su (K, X^L, X^L).
In particolare per ogni attaccante A di DETCTR efficiente, esiste un attaccante di F efficiente B tale che $Adv_{SS}[A, DETCTR] = 2*Adv_{PRF}[B, F]$. $Adv_{PRF}[B, F]$ è trascurabile essenod F una PRF sicura, quindi $Adv_{SS}[A, DETCTR]$ è trascurabile(si vuole ridurre la sicurezza di DETCTR alla sicurezza di F).

### Modes of Operation Many-Time Key
*Esempio:*
File system criptato: si vuole utilizzare la stessa chiave AES per più file.
IPsec: stessa AES per più pacchetti.

**Sicurezza Semantica per Many-Time Key:**
L'avversario, essendo la chiave utilizzata più volte, vede più testi cifrati con la stessa chiave.
L'avversario può fare attacchi di tipo CPA(Chosen Plaintext Attack) ossia può ottenere il testo cifrato di un testo in chiaro scelto da lui e vuole rompere la sicurezza semantica del cifrario.

- Q = (E, D) cifrario su (K, X, C).
- per b = 0, 1, EXP(b) è definito come:
  - A sceglie due messaggi $m_{i, 0}, m_{i, 1}$, dove i è il numero di query fatte da A.
  - Challenger sceglie b uniformemente e cripta $c_i = E(k, m_{i, b})$.
  - A riceve $c_i$ e deve indovinare b.
- CPA mi dice che se l'avversario vuole può ottenere $c = E(k, m)$ facendo una query $m_{j, 0} = m_{j, 1} = m$.

Q è sicuro se $\forall A : Adv_{SS}[A, Q] = |Pr[EXP(0) = 1] - Pr[EXP(1) = 1]| \leq \epsilon$ dove $\epsilon$ è trascurabile.
Quindi l'avversario non deve saper distinguere quali dei due messaggi è stato cifrato.

*Cinfraggi insicuri sotto CPA:*
Supponiamo che E(k, m) dà come output sempre lo stesso testo cifrato c per lo stesso testo in chiaro m e la stessa chiave k.
Se l'avversario fa una query con $m_{j, 0} = m_{j, 1} = m_0$, e successivamente fa una query con $m_{j, 0} = m_0, m_{j, 1} = m_1$, allora l'avversario può capire se il testo cifrato è $E(k, m_0)$ o $E(k, m_1)$.
Per risolvere questo se la chiave segreta dev'essere utilizzata più volte, per lo stesso testo in chiaro m, il testo cifrato dev'essere diverso.

*Soluzione 1: Randomized Encryption:*
- $E(k, m) è un algoritmo randomizzato$. Ossia se si cifra lo stesso testo in chiaro con la stessa chiave, il testo cifrato è diverso.
- Il testo cifrato è più lungo del testo in chiaro: CT-size = PT-size + #random bits.

*Soluzione 2: Nonce-based Encryption:*
- $E(k, m, n) = c$ dove n è un nonce(valore che cambia da messaggio a messaggio).
- c, n sono inviati insieme al destinatario.
- la coppia (k, n) è usata una sola volta, n non ha bisogno di essere segreto e neanche casuale.

Due metodi:
- nonce è un contatore(se il criptaggio mantiene lo stato da un messaggio all'altro).
- nonce è randomico(simile al metodo di Randomized Encryption) $n \leftarrow \mathcal{N}$(con $\mathcal{N}$ abbastanza grande da non ripetersi).

*Prova di sicurezza:*
I stistemi devono essere sicuri quando i nonce sono scelti dall'avversario.
Tutti i nonce $n_1, n_2, ..., n_q$ devono essere distinti.
Nonce-based Q è semanticamente sicuro se $\forall A : Adv_{SS}[A, Q]  = |Pr[EXP(0) = 1] - Pr[EXP(1) = 1]| \leq \epsilon$ dove $\epsilon$ è trascurabile.
L'avversario manda due messaggi $m_{i, 0}, m_{i, 1}$ e il nonce $n_i$ e il challenger sceglie b uniformemente e cripta $c_i = E(k, m_{i, b}, n_i)$.

### Many-Time Key Mode of Operation: Cipher Block Chaining(CBC)
**Costruzione 1: CBC con IV(Initialization Vector):**
- PRP E : K x ${0, 1}^n$ -> ${0, 1}^n$.
- $E_{CBC}(k, m) :$ scelgo $IV \in {0, 1}^n$ a caso, poi faccio $c_0 = IV xor E(k, m_0)$ e $c_i = c_{i-1} xor E(k, m_i)$. Il testo cifrato è $c[i]$ dipende da $c[i-1]$ e sarà composto da $IV, c_0, c_1, ..., c_l$.
- D : K x ${0, 1}^n$ -> ${0, 1}^n$ è l'inverso di E.
- $D_{CBC}(k, c) :$ $m_0 = IV xor D(k, c_0) e m_i = c_{i-1} xor D(k, c_i)$.

*Sicurezza:*
Teorema: $\forall L > 0$, se E è una PRP semanticamente sicura su (K, X) allora CBC è semanticamente sicuro su (K, X^L, X^{L+1}).
In particolare per ogni attaccante A efficiente e query q attaccante di CBC, esiste un attaccante di E efficiente B(che è un attaccante di PRP) tale che $Adv_{CPA}[A, CBC] <= 2*Adv_{PRP}[B, E] + \frac{2q^2L^2}{|X|}$.
CBC è sicuro se $q^2L^2 << |X|$.
CBC è sicuro se E è sicuro.

*Esempio:*
- q: numero di messaggi criptati con la stessa chiave.
- L: lunghezza dei messaggi.
- supponiamo di avere $Adv_{CPA}[A, CBC] = 1/2^{32} \rightarrow \frac{q^2L^2}{|X|} < 1/2^{32}$.
- AES ha $|X| = 2^{128} \rightarrow qL < 2^{48}$ quindi dopo 2^48 messaggi devo cambiare la chiave.
- 3DES ha $|X| = 2^{64} \rightarrow qL < 2^{16}$ quindi dopo 2^16 messaggi devo cambiare la chiave.

*Attacco:*
Se l'avversario può predirre IV allora può fare un attacco CPA.
- mettiamo che dato $c = E_{CBC}(k, m)$, l'avversario può predirre IV per il prossimo messaggio.
- A inizializza un $c_0 = [IV, E(k, 0 xor IV)]$.
- A manda $m_0 = IV xor IV*$ e $m_1 \neq m_0$, dove $IV*$ è il valore predetto.
- A riceve $c = [IV*, E(k, IV)] oppure [IV*, E(k, m_1 xor IV*)]$. Se $c = [IV*, E(k, IV)]$, quindi uguale a $c_0$, allora sa che $b = 0$, altrimenti $b = 1$.
In pratica l'avversario riconosce il messaggio, poichè se il challenger utilizza IV*(predetto dall'avversario) allora il testo cifrato sarà uguale a $c_0 = [IV, E(k, 0 xor IV)] = [IV*, E(k, IV)]$.

**Costruzione 2: Nonce-based CBC:**
- la chiave è data da una coppia (k, k_1) dove k è la chiave e k_1 è la chiave per crittare il nonce.
- la coppia (k, n) è usata una sola volta.
- $E_{CBC}(k, m, n) :$ $IV = E(k_1, n)$, $c_0 = IV xor E(k, m_0)$ e $c_i = c_{i-1} xor E(k, m_i)$. Il testo cifrato è $c[i]$ dipende da $c[i-1]$ e sarà composto da $n(incluso solo se il destinatario non lo conosce), c_0, c_1, ..., c_l$.
- $D_{CBC}(k, c) :$ $IV = E(k_1, n)$, $m_0 = IV xor D(k, c_0) e m_i = c_{i-1} xor D(k, c_i)$.

*Esempio:*
Crypto API utilizza un IV scelto dal mittente, quindi se non è random verrà crittato prima di usarlo, altrimenti il sistema è vulnerabile ad attacchi CPA.

### Many-Time Key Mode of Operation: Counter Mode(CTR)
**Costruzione 1: Randomized CTR:**
- PRP F : K x ${0, 1}^n$ -> ${0, 1}^n.
- $E_{CTR}(k, m) :$ scelgo $IV \in {0, 1}^n$ a caso, poi faccio $c_0 = m_0 xor F(k, IV)$ e $c_i = m_i xor F(k, IV + i)$. Il testo cifrato è $c[i]$ dipende da $IV$ e sarà composto da $IV, c_0, c_1, ..., c_l$. IV è scelto randomicamente per ogni messaggio. E' parallelizzabile(ogni blocco può essere cifrato indipendentemente).
- $D_{CTR}(k, c) :$ $m_0 = c_0 xor F(k, IV)$ e $m_i = c_i xor F(k, IV + i)$. Non c'è bisogno di invertire F.

**Costruzione 2: Nonce-based CTR:**
- PRF F : K x ${0, 1}^n$ -> ${0, 1}^n.
- $E_{CTR}(k, m, n) :$ scelgo $IV$ come $n0$(quindi il nonce seguito da uno 0) e faccio $c_0 = m_0 xor F(k, IV)$ e $c_i = m_i xor F(k, IV + i)$. Il testo cifrato è $c[i]$ dipende da $IV$ e sarà composto da $n0, c_0, c_1, ..., c_l$. Quindi $IV = n0, IV + i = ni$. Il nonce rimane lo stesso per tutto il messaggio, ma cambia per ogni messaggio.
- per assicurarsi che $F(k, x)$ non si utilizzi più di una volta, la coppia (nonce, contatore) ha il contatore che parte da 0 e incrementa di 1 per ogni blocco mentre il nonce rimane lo stesso per tutto il messaggio.