### Sicurezza dei sistemi e permessi (Access Control), Linux
#### Access Control
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

# DA COMPLETARE