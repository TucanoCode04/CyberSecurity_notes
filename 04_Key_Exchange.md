## Key Exchange
### Trusted third parties
**Key Management:**$\\$
*Problema:*$\\$
n utenti, ognuno con n-1 chiavi.

*Soluzioni:*
- O(n) chiavi per utente, O(n^2) chiavi totali
- Online Trusted Third Party (TTP), ogni utente ha una chiave con il TTP

**Generazione chiavi: Un protocollo giocattolo:**$\\$
A vuole condiivere una chiave con B. Sicurezza contro eavesdropping. 
- Alice richieve una chiave condivisa $k_{AB}$ al TTP
- Il TTP sceglie randomicamente $k_{AB}$ ed invia ad Alice $E(k_{A}, "Alice, Bob" || k_{AB})$ e un ticket $E(k_{B}, "Alice, Bob" || k_{AB})$
- Alice invia a Bob il ticket $E(k_{B}, "Alice, Bob" || k_{AB})$ dato che lo può decriptare solo lui

L'eavesdropper vede solo $E(k_{A}, "Alice, Bob" || k_{AB})$ e $E(k_{B}, "Alice, Bob" || k_{AB})$ e dato che (E, D) è CPA sicuro, non può fare nulla.

### Merkle Puzzles
A e B vogliono condividere una chiave. Sicurezza contro eavesdropping.
Può essere fatto in maniera inefficiente utilizzando crittografia simmetrica.

**Puzzles:** 
Problemi che posso risolvere con "qualche sforzo"

*Esempio:*
- $E(k, m)$ è un cifrario simmetrico con $k \in \{0,1\}^{128}$
- puzzle: $E(P, "messaggio")$ dove $P = 0^{96} || b_1 || b_2 || ... || b_{32}$ (96 bit tutti a zero concatenati a 32 bit)
- per risolvere il puzzle, bisogna trovare P tramite $2^{32}$ tentativi

- Alice prepara $2^{32}$ puzzle: 
    . per $i = 1,... , 2^{32}$, $P_i \in \{0,1\}^{32}$ e $x_i, k_i \in \{0,1\}^{128}$ randomiche (con $x_i \neq x_j$)
    - $puzzle_i = E(0^{96} || P_i, "Puzzle_i" || x_i || k_i)$
- Alice invia i puzzle a Bob
- Bob sceglie un puzzle e lo risolve con bruteforce
- Bob ottiene ($x_i, k_i$), usa $k_i$ come chiave simmetrica e invia $x_i$ ad Alice
- Alice guarda il puzzle con $x_i$ e utilizza $k_i$ come chiave simmetrica

*Tempo:*
- Alice: $2^{32}$ quindi O(n)
- Bob: $2^{32}$ quindi O(n)
- Eavesdropper: $2^{64}$ dovendo risolvere tutti i puzzle quindi O(n^2)

### Diffie-Hellman Protocol
A e B vogliono condividere una chiave. Sicurezza contro eavesdropping. E' possibile farlo con un gap esponenziale(a differenza di prima che era polinomiale).

**Idea:**
- Alice e Bob non condividono nessuna informazione segreta iniziale
- Alice e Bob scambiano messaggi pubblici
- Alice e Bob concordano su una chiave segreta
- Eavesdropper non può ottenere la chiave segreta

*Sicurezza basata sul problema del logaritmo discreto:*
- dati $g, p, g^k \space mod \space p$ trovare k

**Protocollo:**
- Fissato un primo p(600 bit) e un generatore g(tra 2 e p-2)
- Alice sceglie un numero randomico $a \in \{1, ..., p-2\}$ e calcola $A = g^a \space mod \space p$ e lo invia a Bob
- Bob sceglie un numero randomico $b \in \{1, ..., p-2\}$ e calcola $B = g^b \space mod \space p$ e lo invia ad Alice
- Alice fa $(g^b)^a \space mod \space p = g^{ab} \space mod \space p = B^a \space mod \space p$(ultimo passaggio rende chiaro)
- Bob fa $(g^a)^b \space mod \space p = g^{ab} \space mod \space p = A^ \space mod \space p$
- Alice e Bob condividono $g^{ab} \space mod \space p$

*Eavesdropper:*
- vede $p, g, g^a \space mod \space p, g^b \space mod \space p$
- deve trovare $g^{ab} \space mod \space p$, l'algoritmo più efficiente è GNFS che è esponenziale $O(n^{1/3})$

**Insicurezze:**
E' insicuro contro il Man-in-the-middle attack, in generale contro attacchi attivi.
Perchè l'eavesdropper può fare:
- Alice manda $g^a \space mod \space p$ 
- Eavesdropper intercetta e manda $g^{a'} \space mod \space p$ a Bob
- Bob manda $g^b \space mod \space p$
- Eavesdropper intercetta e manda $g^{b'} \space mod \space p$ ad Alice
- Alice calcola $(g^{b'})^a \space mod \space p = g^{ab'} \space mod \space p$
- Bob calcola $(g^{a'})^b \space mod \space p = g^{a'b} \space mod \space p$
