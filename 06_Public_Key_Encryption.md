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