## Network Security: Radio and Wireless
### Radio
Le trasmissioni radio sono soggette a interferenze e a problemi di sicurezza informatica.

**TX:** $\newline$
Trasmettitore: colui che trasmette il segnale.

Eccitando un'antenna, il campo elettromagnetico si propaga nello spazio. Gli elettroni in cui è immersa l'antenna vengono eccitati dall'energia a cui li sottoponiamo, e iniziano a vibrare. Queste vibrazioni creano un campo elettromagnetico che si propaga nello spazio.

**Antenna:** $\newline$
Dispositivo che trasmette e riceve segnali.
Ogni antenna deve essere dimensionata correttamente per la trasmissione che vogliamo effettuare(un'antenna grande non implica una trasmissione più potente).

**RX:** $\newline$
Ricevitore: colui che riceve il segnale.

**Ricezione:** $\newline$
L'antenna riceve lo spostamento del campo elettromagnetico e lo trasforma in segnale elettrico da mandare ai circuiti di ricezione che elaborano le informazioni.

**Transceiver:** $\newline$
Dispositivo che può trasmettere e ricevere segnali.

**Sintonizzazione:** $\newline$
Per trasmettere l'antenna deve essere sintonizzata alla frequenza di trasmissione, chiamata *carrier frequency*. per questo in trasmissione le antenne devono essere dimensionate correttamente.

Per ricevere, un'antenna più grande catturerà più informazioni. Il filtraggio delle informazioni che ci interessano si chiama *sintonizzazione*.

La sintonizzazione non è sicurezza, ma è un modo per evitare interferenze.

**Modulazione:** $\newline$
Protocollo di comunicazione(physical layer) che prevede come le informazioni(bit) vengono codificate sull'antenna.

3 classi:
- *Modulazione in Ampiezza(AM)*
- *Modulazione in Frequenza(FM)*
- *Modulazione in Fase(PM)*

**Modulazione OOK:** $\newline$
On-Off Keying: trasmette un bit per volta, la frequenza portante viene accesa(1) o spenta(0).

**Attacchi:** $\newline$
- *Jamming/Denial of Service(DoS):* interferenza con la trasmissione radio(per esempio accendere sempre la portante in OOK). Richiede molta potenza dal Jammer, quindi si possono utilizzare più frequenze portanti(banda larga) per far usare ancora più potenza al Jammer.
    - Difesa: *Frequency Hopping Spread Spectrum(FHSS)*: si selezionano più frequenze portanti e si salta da una all'altra seguendo una sequenza decisa a priori(se il Jamming avviene su una portante si perde solo quell'informazione).
- *Eavesdropping/Sniffing:* sono facilmente realizzabili con un ricevitore alla corretta distanza e se si conoscono modulazione e protocollo di comunicazione.
- *Replay Attack:* si può fare senza conoscere modulazione o protocollo, si cattura una porzione di frequenze e si ritrasmettono così come ricevute. NON è protetto da confidenzialità o integrità.
    - Difesa: *Rolling Code*: il trasmettitore selezione un codice basandosi su un PRNG(Pseudo Random Number Generator) e lo trasmette, il trasmettitore seleziona il codice successivo e lo trasmette. Il ricevitore controlla se il codice ricevuto è consistente basandosi sul suo PRNG, se lo accetta fa avanzare il PRNG. Non risolve completamente il problema, ma in questo modo il Jammer dovrà catturare e ritrasmettere più codici.
    - Difesa: *Challenge-Response*: il trasmettitore invia una richiesta di autenticazione, il ricevitore risponde con un numero casuale(nonce), il trasmettitore cifra il nonce e lo invia, il ricevitore decifra il nonce e valida o meno l'autenticazione.

In generale si utilizza anche una cifratura dei dati, a blocco(AES) o a flusso(Kasumi).

**RFID:** $\newline$
Radio Frequency Identification: tecnologia che permette di identificare un oggetto tramite un tag RFID, con un codice univoco. I portali in cui si richiede l'autenticazione effettueranno una ricerca nel database per verificare l'identità del tag(per esempio badge dell'Unibo con tecnologia EM410X).

- *Cloning:* non si può cambiare il codice del tag, ma si può clonare il tag stesso.
- *Spoofing:* ci si può spacciare per un tag tramite un transceiver RFID.

### IEEE 802.11
E' uno standard per la trasmissione di dati wireless(WLAN), conosciuto come *Wi-Fi*.

**Layer Fisico:** $\newline$
E' diviso in più sotto-standard in base alla frequenza di trasmissione. Questo comporta differenze nei livelli fisici e nelle modulazioni.

**Accesso al canale:** $\newline$
Protocollo di accesso al canale tra i dispositivi per evitare collisioni: *Carrier Sense Multiple Access with Collision Avoidance(CSMA/CA)*.

**Terminale nascosto:** $\newline$
Il punto d'accesso ha visibilità di tutta la rete, ma i singoli nodi no. Se due nodi trasmettono contemporaneamente, il punto d'accesso non può rilevare la collisione.

**IFS:** $\newline$
Inter Frame Space: intervallo di tempo tra due frame, per evitare collisioni.
Prevede quindi spazi di silenzio tra i frame.

Se qualcuno non rispetta l'IFS, parla solo lui e non permette a nessun altro di parlare.

**Attacchi:** $\newline$
- *Denial of Service:* se due terminali non rispettano l'IFS, nessun altro può parlare.
    - Difesa: *SSID Hiding*: nascondere l'identità della rete, che viene trasmessa in chiaro agli host che richiedono l'accesso(security through obscurity).
    - *MAC Whitelisting*: a livello 2 i terminali IEEE 802.11 hanno un MAC Address, si può fare una lista di MAC Address autorizzati, il problema è che il MAC Address è facilmente spoofabile.
    
**Cifratura:** $\newline$
Autenticazione e confidenzialità sono cruciali in reti di questo tipo, poichè non c'è bisogno di un Man in the Middle per catturare i dati. In una rete open le informazioni vengono inviate nell'etere, dove tutti le possono catturare, per questo si utilizzano protocolli di cifratura.

Tuttavia certi tipi di messaggi, come l'associazione o la disassociazione, devono essere inviati in chiaro per permettere la comunicazione tra i dispositivi.

- *WEP:* Wired Equivalent Privacy, autenticazione e confidenzialità. 
    - *Shared Key:* Si utilizza un meccanismo di challenge-response per l'autenticazione, il problema è che è vulnerabile ad attacchi KP(Known Plaintext) che permettono di ricavare la chiave.
    - *Open System:* Il richiedente di connessione è già a conoscenza della chiave, altrimenti non potrebbe decifrare i pacchetti provenienti dal punto d'accesso.

Dopo 30000 pacchetti WEP la possibilità di collisione è inevitabile, catturando quindi pacchetti con lo stesso IV(Initialization Vector) si possono fare attacchi statistici per ricavare la chiave.

- *WPA:* Wi-Fi Protected Access, è uno standard più sicuro di WEP, si basa su diversi modi di utilizzo per il canale:
    - *PSK:* Pre-Shared Key, per reti domestiche, la chiave è condivisa tra tutti i dispositivi.
    - *Enterprise:* per reti con molti dispositivi, si utilizza un server di autenticazione RADIUS.
    - *WPS:* Wi-Fi Protected Setup, sistema di autenticazione semplificato.
- e diversi metodi di cifratura:
    - *TKIP:* Temporal Key Integrity Protocol, chiave temporanea per ogni pacchetto, compatibile con WEP, deprecato.
    - *CCMP:* Counter Mode with Cipher Block Chaining Message Authentication Code Protocol, più sicuro di TKIP, utilizza AES.

- *WPA-PSK-TKIP:* è basato su RC4 per retrocompatibilità con WEP, gestisce le chiavi in modo da complicare attacchi KP. Deprecato.
- *WPA-PSK-CCMP:* utilizza AES per la cifratura, uno dei metodi più sicuri.
    - Attacchi a PSK: soffrono i problemi legati all'autenticazione, non alla cifratura, quindi si possono fare attacchi di forza bruta o di dizionario.

- *WPS:* non necessità di password, si può accedere alla rete attraverso un PIN o un pulsante sul router.
    - Attacchi: *Rogue AP:* il settaggio del nome(SSID) della rete è fatto in chiaro, quindi si può fare un attacco di spoofing del nome e invitando i client ad accedere impossibilitati poichè non conoscono la password.
    - *PIN Brute Force:* il PIN è a 7 cifre, quindi ci sono $10^7$ combinazioni possibili. Però il controllo del PIN è fatto in due parti, quindi si può fare un attacco a 4 cifre e uno a 3 cifre, riducendo le combinazioni a $10^4 + 10^3$.
    - *Pixiedust:* attacco che sfrutta la generazione del PIN, che è basato su un seed casuale. Se si riesce a catturare il seed si può calcolare il PIN.

- *WPA-Enterprise:* utilizza un server di autenticazione RADIUS(Remote Authentication Dial-In User Service) che mantiene le credenziali degli utenti(tipo un database per le reti wireless).
    - Attacchi: il primo passaggio della rete è in chiaro, quindi si può fare Rogue AP, venendo esso fatto con challenge-response il Rogue AP può fare un bruteforce o un dizionario per ottenere la chiave.

Viene abilitato di deafult un protocollo che disabilita l'uso di challenge-response inviando la password in chiaro(GTC).

*WPA2* utilizzava nella fase di autenticazione della rete un nonce, che poteva venire riutilizzato per velocizzare l'autenticazione, rendendo vulnerabile la rete ad attacchi di replay. Adesso si usa *WPA3*.





