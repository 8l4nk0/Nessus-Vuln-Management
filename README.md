# Nessus-Vuln-Management<br>
Vulnerability Management with Nessus<br><br>

VULNERABILITY MANAGEMENT: Essenzialmente, consiste nel cercare e trovare costantemente vulnerabilità all'interno del software, OS, ... per ridurne l'impatto ai minimi.<br><br>


<h2>Requisiti</h2><br>
- Nessus Essentials (vulnerability scanner)<br>
- VM Windows 10<br>
- Virtual Box<br>

Una volta installato Nessus si apre una scheda nel browser per connettersici tramite SSL: http://localhost:8834/WelcomeToNessus-Install/welcome<br>
Ricordatevi le credenziali di Nessus dato che la sessione si chiude automaticamente dopo circa 20-30 minuti.
Installazione ISO di Windows avanzata senza Product Key.<br>
Impostate username e password della VM facilmente ricordabili perché ci serviranno dopo.<br><br>


<h2>Fase 1)</h2><br>
Disattiviamo il firewall sulla VM windows così che sia rintracciabile sulla rete. (Possiamo verificare che non lo è di default effettuando un ping continuo verso la VM, restituirà sempre "richiesta scaduta").<br>
Cerchiamo sulla barra di ricerca di Windows wfs.msc per accedere alle impostazioni del Firewall e disattiviamolo sia nel campo dominio, pubblico e privato.<br>
Creiamo un nuovo scan di nome "Windows 10 Single Host" su Nessus di tipo Network Scan inserendo nel campo Target l'ip della VM, IP_VM.<br>
In questo caso andremo a fare uno scan manuale, ma questo può essere automatizzato nella sezione Schedule.<br>
Lo scan verrà effettuato sulle porte più comuni.<br>

![ping IP_VM -t](https://github.com/user-attachments/assets/65e0cb09-6db5-4501-8e91-442c60bfcb7c)<br>

<h2>Fase 2)</h2><br>
Eseguiamo lo scan e aspettiamo che termini (Se non trovate nulla, provate a vedere se avete lasciato il ping in esecuzione e fermatelo).<br>
Senza aver abilitato la VM per accesso con SMB e senza aver fornito a Nessus le credenziali per accedere alla VM, i risultati di questo scan saranno basilari.<br><br>

<h2>Fase 3)</h2><br>
Sulla barra di ricerca Windows, cerchiamo "services.msc".<br>
Cerchiamo "Registro remoto" per abilitare la funzione di connessione remota sulla VM, selezioniamo Automatico e Applica, infine Avvia.<br><br>

Poi cerchiamo "Impostazioni di Condivisione avanzata" e nella sezione Guest o Pubblico (corrente) abilitiamo "Attiva condivisione file e stampanti".<br><br>

Un'altra cosa, è un pò brutto da fare ma non essendo la VM nel dominio, cerchiamo "impostazioni controllo dell'account utente" e impostiamo il livello più basso di tutti così che la VM
accetti la connessione remota senza interferenze (verrà richiesto il consenso dell'amministratore, cliccate Si).<br><br>

Infine cerchiamo "Editor registro del sistema" e andiamo su HKEY_LOCAL_MACHINE -> SOFTWARE -> Microsoft -> Windows -> CurrentVersion -> Policies -> System.<br>
Qui facciamo click destro per creare una nuova DWORD con nome "LocalAccountTokenFilterPolicy".<br>
Click destro su questa nuova DWORD -> Modifica e impostiamo ad 1 il valore per abilitarla.<br><br>

Riavviamo la VM.<br><br>

<h2>Fase 4)</h2><br>
Su Nessus, clicchiamo la checkbox affianco allo scan che abbiamo creato precedentemente e clicchiamo su More -> Configure per modificarne le impostazioni.<br>
Clicchiamo su Credentials -> Windows e inseriamo le credenziali decise all'inizio durante la preparazione di Windows 10.<br>
Salviamo e eseguiamo lo scan.<br>
Con lo scan con credenziali, troveremo molte più vulnerabilità, alcune delle quali critiche.
Prima di andare a risolverle, installiamo sulla VM una vecchia versione di Firefox così da poterne
riscontrare le vulnerabilità con Nessus.<br><br>


<h2>Fase 5)</h2><br>
Sulla VM, cerchiamo su Internet "download old firefox" e clicchiamo sul sito support.mozilla.org.<br>
Andiamo alla sezione "I still want to downgrade - where can i get the previous version?" e clicchiamo su
"Directory of other versions and languages" e cerchiamo la versione 3.6.12.<br><br>

Link della versione 3.6.12: https://ftp.mozilla.org/pub/firefox/releases/3.6.12/win32/en-US/<br><br>

e scarichiamo il primo dei due eseguibili (quello da 8M).<br>
Clicchiamo due volte sull'eseguibile scaricato e cliccate sempre si per lasciare le impostazioni di default.<br>
In questo modo la nostra VM avrà una vecchissima e probabilmente vulnerabile versione di Firefox.<br>


<h2>Fase 6)</h2><br>
Verifichiamo eseguendo un altro scan con Nessus.<br>
Soltanto con Firefox, il numero delle vulnerabilità critiche è salito vertiginosamente.<br>
Ora passiamo alla fase di Remediation, ossia proviamo a risolverle.<br>
Nessus ci offre una sezione apposita chiamata appunto Remediations dove ci fornisce una soluzione generale per la maggior parte delle vulnerabilità invece che andare ad aprirle una ad una e vedere le possibili soluzioni.<br>
Nel caso di Firefox, come è ovvio che sia, ci basta aggiornarlo all'ultima versione o ancora meglio, disinstallarlo.<br><br>


<h2>Fase 7)</h2><br>
Nella VM cerchiamo appwiz.cpl per disinstallare Firefox.<br>
Nelle impostazioni della VM cerchiamo ed eseguiamo Windows Update fin quando non rimangono più vulnerabilità evidenti. Ogni volta clicchiamo su "cerca nuovi aggiornamenti" e se ci sono, clicchiamoli e riavviamo la VM.<br>
Infine riavviamo la VM per sicurezza.<br>
Eseguiamo nuovamente lo scan con Nessus e confrontiamo con i due precedenti scan.<br>


