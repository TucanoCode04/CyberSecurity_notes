## Crittografia
**Crittografia:** Scienza che si occupa, con l'aiuto della matematica, di oscurare il significato di un messaggio in modo che solo chi è autorizzato possa leggerlo.
**Crittanalisi:** Scienza che si occupa di trovare il modo di decifrare un messaggio senza conoscere la chiave.

**Steganografia:** Nasconde l'esistenza del messaggio.
**Crittografia:** Nasconde il significato del messaggio.

**Goals:**
- privacy
- autenticità
- integrità
- non ripudio (non negabilità di essere l'autore del messaggio)

**Principio di Kerckhoffs:**
La sicurezza del protocollo deve dipendere dalla chiave e non dall'algoritmo che può essere pubblico.
- No security through obscurity

**Modelli di minaccia:**
- Conoscenza del cifrario:
    - Principio di Kerckhoffs
    - l'attaccante conosce l'algoritmo di cifratura
- Interazione con i messaggi ed il protocollo:
    - passivamente: osserva e cerca di decriptare il messaggio(confidenzialità)
    - attivamente: osserva, modifica, inietta o cancella messaggi (integrità, autenticità, confidenzialità)
- Interazione con l'algoritmo:
    - ciphertext-only: l'attaccante conosce solo il testo cifrato
    - chosen-plaintext: l'attaccante può scegliere un numero di messaggi e ottenere i corrispondenti testi cifrati
    - chosen-ciphertext: l'attaccante può scegliere un numero di testi cifrati e ottenere i corrispondenti messaggi in chiaro
- Risorse:
    - illimitate
    - limitate (calcolare, spazio di memoria, tempo)

**Criptaggio simmetrico(confidenzialità):**
Alice ha un messaggio *m* ed un cifrario *E*. Alice cifra il messaggio con la chiave *k* e ottiene il testo cifrato *c=E(k,m)*. 
Bob riceve il testo cifrato *c* e col suo cifrario *D* e la chiave *k* decifra il messaggio *m=D(k,c)*.

- *chiave ad uso singolo*: la chiave è usata una sola volta(e-mail criptate)
- *chiave ad uso multipli*: la chiave è usata più volte(file criptati)

**Criptaggio asimmetrico:**
Alice ha un messaggio *m* e la chiave pubblica di Bob *Pb*. Alice cifra il messaggio con la chiave pubblica di Bob.
Bob riceve il testo cifrato e lo decifra con la sua chiave privata.

**Utilizzi:**
- stabilire una chiave segreta
- comunicazione sicura
- firma digitale
- comunicazione anonima
- denaro digitale anonimo
- ricerche private(E[query] -> E[result])
- prove di conoscenza(conosco la password -> conosci la password?)

**Cifrario di Cesare:**
Spostamento di *k*(chiave) posizioni dell'alfabeto.
E(3, "ciao") -> "flcr"
D(3, "flcr") -> "ciao"
*Criptaggio:* E(k, m) = (m+k) mod 26
*Decriptaggio:* D(k, c) = (c-k) mod 26

Facile da decifrare con un attacco a forza bruta, poichè l'alfabeto è di 26 lettere.

**Cifraggio di sostituzione monoalfabetica:**
Lo spazio delle chiavi + $\sigma = {A, B, C, ..., Z}$ è di $26!$.
Data una permutazione $\pi$ di $\sigma$, si ha:
E($\pi$, "ciao") -> "xqcn"
D($\pi$, "xqcn") -> "ciao"
*Criptaggio:* E($\pi$, m) = $\pi(m)$
*Decriptaggio:* D($\pi$, c) = $\pi^{-1}(c)$

Questo cifrario si rompe utilizzando la frequenza delle lettere(Frequency analysis attack) e dei gruppi di lettere, dato che preserva le caratteristiche del linguaggio.

**Cifrario di Vigenère:**
Si utilizza una chiave *k* di lunghezza *n*.
*Criptaggio:* E(k, m) = $m_i + k_i$ mod 26
*Decriptaggio:* D(k, c) = $c_i - k_i$ mod 26
**Esempio:**
k = "cryptocrypto"
m = "whataniceday"
c = "yyyitbktcstm"
E(k_1, m_1) = "w" + "c" mod 26 = "y" (22 + 2 mod 26)
E(k_2, m_2) = "h" + "r" mod 26 = "y" (7 + 17 mod 26)
E(k_3, m_3) = "a" + "y" mod 26 = "y" (0 + 24 mod 26)
E(k_4, m_4) = "t" + "p" mod 26 = "i" (19 + 15 mod 26)
etc.

Una lettera corrisponde a più lettere. Per rompere il cifrario si può indovinare la lunghezza della chiave, dividere il testo cifrato e usare l'analisi di frequenza su ogni spostamento del cifrario.

**Rotor Machines:**
Se il cifrario di Vigenère può essere rotto trovando la lunghezza della chiave basta fare più round di sostituzione, mappando le lettere più volte.
Una macchina è composta da più cilindri(rotori) che mappano le lettere più volte utilizzando cavi elettrici/meccanici.
