## Sicurezza dei sistemi e permessi (Access Control), Linux
### Access Control
Da autenticazione a autorizzazione, il controllo degli accessi è un processo che determina chi può accedere a cosa.

**Access Control:**
- previene a utenti non autorizzati di accedere a risorse.
- previene a utenti autorizzati di accedere a risorse in modo non autorizzato.
- permette a utenti autorizzati di accedere a risorse.

E' un insieme di regole(policies) e meccanismi che applicano le regole. Che tipi di accesso sono permessi, sotto che circostanze, e chi può accedere a cosa.

Elementi base:
- *Soggetti:* entità che possono accedere ad oggetti.
- *Oggetti:* risorse ad accesso controllato.
- *Permessi:* regole che definiscono cosa un soggetto può fare su un oggetto.

Tipi di Policy:
- *Discretionary Access Control(DAC):* Discrezionario perchè il proprietario dell'oggetto decide chi può accedere all'oggetto. Accesso in base all'identità del soggetto e alla relazione tra soggetto e oggetto.
- *Mandatory Access Control(MAC):* Obbligatorio perchè il sistema decide *security labels* e *clearance* per soggetti e oggetti. Accesso compara i *security labels* e *clearance* del soggetto.
- *Role Based Access Control(RBAC):* Ruoli assegnati ai soggetti, accesso in base al ruolo.

Principi fondamentali per le policy:
- Open Design: il design del sistema è pubblico.
- Economy of Mechanism: il design è semplice.
- Fail-Safe Defaults: accesso negato se non specificato.
- Complete Mediation: controllo di accesso su ogni tentativo di accesso.
- Least Privilege: accesso minimo necessario. Applicato attraverso un *reference monitor*(software che implementa le policy) che implementa il principio di *complete mediation*.

### Sicurezza dei sistemi
- possibilità di avere enti certificati nel sistema.
- sistemi a singolo o multi-utente
- possibilità di divisione die privilegi

Attacchi:
- *Privilege Escalation:* possibilità di un utente di ottenere più privilegi(installare software, modificare file di sistema, ecc) di quelli che dovrebbe avere. L'Arbitrary Code Execution è un prima passo per l'escalation, in quanto permette di eseguire codice arbitrario, attraverso l'installazione di software malevolo o lo sfruttamento di vulnerabilità(per esempio UNIX tutti è un file, quindi se ho controllo su un file ho controllo sul sistema).

**Access Control List(ACL):**
Il sistema mantiente una lista di permessi soggetto-oggetto.

*Esempio:*
Unix/Linux dichiara 3 soggetti: proprietario del file, gruppo del file, altri.
I soggetti sono identificati tramite un file(/etc/passwd) che contiene informazioni sugli utenti, analogamente per i gruppi(/etc/group).

- *DAC:* in Unix/Linux è possibile assegnare permessi di lettura, scrittura ed esecuzione a proprietario, gruppo e altri.

**Capabilities:**
Le capabilities sono dei token che rappresentano un oggetto e i permessi che un soggetto ha su quell'oggetto.

*Esempio:*
- cookie web: rappresentano la sessione di un utente.
- file aperti: rappresentano i file aperti da un processo.

A seguito di una SYS call, il kernel controlla le capabilities del processo e decide se permettere o meno l'accesso.

Unix utilizza un sistema misto di ACL(file non aperti) e capabilities(file aperti).

- *MAC:* per evitare che un utente possa modificare i permessi di un file. Mandatory Access Control.

**Modelli di scambio di informazioni:** $\newline$
*Bell-LaPadula Model:* $\newline$
E' un modello di scambio di informazioni tra soggetti e oggetti a diversi livelli di sicurezza. Il modello è basato su 3 regole:
- ogni soggetto e oggetto ha un livello di sicurezza.
- un soggetto può leggere un oggetto solo se il suo livello di sicurezza è maggiore o uguale a quello dell'oggetto.
- un soggetto può scrivere su un oggetto solo se il suo livello di sicurezza è minore o uguale a quello dell'oggetto.

WURD: Write Up, Read Down.

*Biba Model:* $\newline$
E' un modello di scambio di informazioni tra soggetti e oggetti duale al Bell-LaPadula. Il modello è basato su 3 regole:
- ogni soggetto e oggetto ha un livello di sicurezza.
- un soggetto può scrivere su un oggetto solo se il suo livello di sicurezza è maggiore o uguale a quello dell'oggetto.
- un soggetto può leggere un oggetto solo se il suo livello di sicurezza è minore o uguale a quello dell'oggetto.

WDRU: Write Down, Read Up.

### Fylesystem
Il sistema per lo scambio dei dati locali nel sistema operativo è il filesystem. 
Ad esempio su Linux, il filesystem è una struttura ad albero con radice in /.

*Permessi:* $\newline$
Il sistema *DAC* analizza prima UID e GID del processo e poi i permessi del file. Questo può portare a problemi nel momento in cui appartiene ad un gruppo che ha permessi di scrittura su un file, ma tu non hai i permessi di scrittura sul file.

*Superuser:* $\newline$
Il superuser è un utente con permessi speciali che può fare tutto a partire dalla root(SYSTEM in Windows). 

*chown/chgrp:* $\newline$
Per cambiare il proprietario di un file o il gruppo di un file.

*chmod:* $\newline$
Per cambiare i permessi di un file. I permessi sono rappresentati da 3 cifre, in base 8.
- bit 0: execute(per le cartelle è il permesso di entrare nella cartella)
- bit 1: write(per le cartelle è il permesso di creare file nella cartella)
- bit 2: read(per le cartelle è il permesso di vedere i file nella cartella)

777: tutti i permessi
711(nelle cartelle): security through obscurity, permette di entrare nella cartella ma non di vedere i file.

*Gruppi:* $\newline$
Un utente può appartenere a più gruppi, questi permettono di dividere i permessi tra gli utenti.

*Permessi speciali:* $\newline$
Sono salvati nei bit significativi dei metadati del file.
- setuid: permette di eseguire un file con i permessi del proprietario del file(per le cartelle viene ignorato).
- setgid: permette di eseguire un file con i permessi del gruppo del file(per le cartelle permette di creare file solo al gruppo del file).
- sticky bit: permette di creare file solo al proprietario del file, ma è ignorato e deprecato(per le cartelle permette di eliminare solo i file che si possiedono).

setuid e setgid sono pericolosi, in quanto permettono di eseguire codice con permessi speciali. Quindi per esempio i permessi di root sono stati spezzati in più permessi, per evitare che un utente possa fare tutto.

*sudo:* $\newline$
Controlla la password dell'utente e permette di eseguire comandi come superuser(utilizza un framework PAM).



