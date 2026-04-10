# **Design Decision Log: Architettura "Lean L3/L4" (IEC 62443 SL-2)**

**Progetto:** Standardizzazione Sicurezza Tecnopack

**Obiettivo:** Ottenimento conformità SL-2 tramite architettura essenziale, minimizzazione dei costi hardware e assenza di licenze UTM ricorrenti.

## **QUESITO 1: Firewall L3/L4 Stateful vs Next-Gen UTM**

**Domanda:** Se utilizzo un "semplice" firewall L3/L4 Stateful (senza abbonamenti Antivirus, IPS, Deep Packet Inspection UTM), raggiungo comunque lo scopo della IEC 62443 SL-2?

**Decisione Architetturale:** SÌ. L'architettura è conforme e approvata.

**Giustificazione Normativa:**

Il requisito centrale per il perimetro è l'**SR 5.2 (Zone Boundary Protection)**. La norma SL-2 richiede esplicitamente che il firewall neghi tutto il traffico di default ("Deny-by-default") e permetta solo le connessioni esplicitamente dichiarate per porte e IP sorgente/destinazione. Non vi è alcun obbligo normativo SL-2 di ispezionare il payload (DPI/UTM), specialmente se le reti sottostanti sono architetturalmente isolate.

**Vantaggi:** Costi hardware ridotti di un ordine di grandezza, latenza prossima allo zero (wire-speed), nessun costo di licenza annuale, massima affidabilità (MTBF elevato).

## **QUESITO 2: Gestione Accesso Esterno senza Sistemi NAC Complessi**

**Domanda:** Vorrei usare il firewall L3/L4 per autenticare chi si collega alla porta esterna di manutenzione. Un firewall semplice ha questa funzionalità senza dover comprare costosi switch con 802.1X nativo?

**Decisione Architetturale:** SÌ, tramite tecnica di "Captive Portal su VLAN Segregata".

**Giustificazione Tecnica:**

Invece di usare un costoso protocollo NAC a livello di porta dello switch, si configura lo switch unmanaged/managed di base in modo che la porta RJ45 esterna finisca in una VLAN dedicata (es. VLAN 300 \- External). Questa VLAN termina *esclusivamente* sul Firewall L3/L4.

* Il manutentore inserisce il cavo.  
* Il PC riceve un IP, ma non vede nessun PLC (routing bloccato dal Firewall).  
* Qualsiasi traffico HTTP/HTTPS del PC viene reindirizzato dal Firewall a una sua pagina web interna (Captive Portal).  
* Il manutentore inserisce username e password (locali sul firewall).  
* Il firewall sblocca temporaneamente il routing L3 verso l'IP specifico del PLC per cui quell'utente è autorizzato.  
  Questo soddisfa in pieno l'**SR 1.1 (Human User Identification)** e **SR 2.1 (Authorization Enforcement)** a costo zero.

## **QUESITO 3: Eliminazione Porta RJ45 Fisica a favore di Access Point Wi-Fi**

**Domanda:** Se io tolgo fisicamente la porta RJ45 dal quadro e ci metto un Access Point Wi-Fi con autenticazione Captive Portal sul firewall, sono a norma?

**Decisione Architetturale:** ALTAMENTE RACCOMANDATO. È una soluzione superiore rispetto alla porta fisica.

**Giustificazione Normativa e Pratica:**

* **Sicurezza Fisica (SR 7.8):** Eliminare la presa esterna RJ45 rimuove il rischio di "cavi dimenticati" o attacchi fisici di passaggio (es. un dipendente che attacca un cavo per curiosità).  
* **Accesso Condizionato (SR 1.13):** Il Wi-Fi agisce come rete untrusted. Il manutentore locale si collega al Wi-Fi (es. WPA2/3 con password cambiata periodicamente) e finisce nella VLAN "External". Da qui, interviene il Captive Portal del firewall L3/L4 per l'autenticazione ad personam.  
* **Disabilitazione Programmata:** L'alimentazione dell'Access Point (o la rete Wi-Fi stessa) può essere spenta nativamente dal PLC o da un interruttore nel quadro quando non ci sono manutenzioni in corso. Quando l'AP è spento, la macchina è in **totale Air-Gap**.  
* **Ergonomia:** Il manutentore può lavorare con il tablet/PC camminando attorno alla flowpack, senza vincoli fisici di cablaggio.

## **QUESITO 4: Responsabilità Asset Owner (Cliente)**

**Domanda:** Se il cliente usa la sua chiave per aprire il cabinet e si attacca direttamente allo switch unmanaged bypassando il firewall, di chi è la responsabilità?

**Decisione Architetturale:** La responsabilità ricade al 100% sull'Asset Owner.

**Giustificazione Normativa:**

La IEC 62443 stabilisce il concetto di *Shared Responsibility*. Il fornitore (Tecnopack) garantisce la sicurezza *logica* alle interfacce previste (Firewall, Wi-Fi, HMI). Il quadro elettrico chiuso a chiave costituisce il **Physical Security Boundary**. Il superamento deliberato di tale perimetro fisico da parte del cliente annulla i controlli compensativi. Questa clausola sarà inserita nel "Cybersecurity User Manual" fornito con la documentazione della macchina, manlevando Tecnopack.

## **QUESITO 5: Protezione Accesso Programmazione (PLC/HMI) e Gestione Log**

**Domanda:** Sulla rete di supervisione (HMI, PLC, ecc.), devo proteggere l'accesso in programmazione al PLC e all'HMI con utente e password? E devo tenere traccia dei log delle modifiche?

**Decisione Architetturale:** SÌ. Implementazione di "Project Protection" sui dispositivi e attivazione dell'Audit Trail.

**Giustificazione Normativa:**

* **Autenticazione (SR 1.1):** L'SL-2 vieta categoricamente gli "accessi anonimi" per attività critiche. Qualsiasi connessione di programmazione (es. tramite TIA Portal, Studio 5000, o accesso SSH/Web all'HMI) deve richiedere una credenziale. Non è necessario un server Active Directory per l'SL-2; le password locali impostate nei progetti PLC/HMI sono sufficienti, purché non siano quelle di default ("admin/admin").  
* **Tracciabilità Log (SR 2.8 \- Auditable Events):** È obbligatorio generare e conservare log per gli eventi di sicurezza. Non serve loggare ogni singolo pulsante premuto dall'operatore, ma l'HMI deve avere attivata la funzione di **Audit Trail** per registrare: Login riusciti/falliti, cambi utente, modifiche a parametri critici di ricetta/safety, e avvii/fermi macchina non previsti.

## **QUESITO 6: Sicurezza Fisica dei Dispositivi di Campo su Switch Unmanaged**

**Domanda:** Sulla macchina utilizziamo **switch unmanaged** per contenere i costi, quindi non possiamo usare il MAC Filtering logico. Cosa succede normativamente se un malintenzionato stacca il cavo di rete di una bilancia o di un metal detector per attaccarci un proprio PC infetto?

**Decisione Architetturale:** Mitigazione tramite controlli compensativi Fisici, IP e Applicativi.

**Giustificazione Normativa:**

In assenza di Port Security a livello di switch (layer 2), la conformità IEC 62443 SL-2 si raggiunge applicando una combinazione di controlli compensativi:

* **Disabilitazione DHCP (Livello IP):** Sulla rete di macchina non deve esistere alcun server DHCP attivo. Dispositivi, PLC e bilance hanno IP statici rigidi. L'attaccante che inserisce il PC non ottiene automaticamente i parametri di rete, alzando il livello di competenza richiesto per l'attacco (SL-2).  
* **Watchdog di Rete e Safety (Livello Applicativo):** Il PLC scambia ciclicamente pacchetti (keep-alive/heartbeat) con i dispositivi di campo. Se il cavo viene staccato, il PLC rileva il timeout entro pochi millisecondi e manda la macchina in stato di blocco/allarme. Un attaccante non ha il tempo fisico di collegarsi e fare IP-Spoofing senza causare un fermo macchina (che avvisa gli operatori).  
* **Contenimento (Firewall):** Anche se l'attaccante riesce a indovinare l'IP e a collegarsi allo switch unmanaged, si trova confinato in una "Cell/Area Zone" cieca. Non può propagare l'attacco al server aziendale o alle altre macchine perché il Firewall L3 a monte lo blocca.  
* **Sicurezza Fisica (SR 7.8):** I cavi e i dispositivi non devono avere porte RJ45 esposte liberamente. I cablaggi devono essere fascettati, inseriti in canaline chiuse, o richiedere lo smontaggio di carter/sportelli. Anche in questo caso si ricade nella *Responsabilità dell'Asset Owner* per la violazione del perimetro fisico.

## **QUESITO 7: Perimetro Fisico su Quadri Mobili / Pensili HMI**

**Domanda:** Se un operatore/malintenzionato svita o apre il quadro mobile (il pensile/pulsantiera) dove è alloggiato l'HMI per collegarsi alla porta RJ45 dietro lo schermo, ricado sempre nel concetto di "Responsabilità dell'Asset Owner" visto per l'armadio principale?

**Decisione Architetturale:** SÌ. Il quadro pensile è un'estensione del Physical Security Boundary.

**Giustificazione Normativa:**

Ai fini della IEC 62443, qualsiasi involucro che racchiuda l'hardware di rete e che richieda l'utilizzo di un utensile (cacciavite, chiave a brugola, chiave a spillo) per essere aperto, costituisce una barriera fisica (Physical Boundary).

Se il cliente smonta il carter del braccio pensile o il retro del monitor HMI eludendo l'isolamento meccanico fornito da Tecnopack, commette una violazione procedurale di competenza dell'Asset Owner, esentando Tecnopack dalla responsabilità dell'eventuale infezione di rete derivante. (Clausola da ribadire nel manuale utente).

## **QUESITO 8: Adozione di Apparati MikroTik (RouterOS) in Ambiente OT**

**Domanda:** Possiamo utilizzare switch managed e firewall/router di brand non nativamente "industriali" come **MikroTik** (che offrono alimentazione DC nativa 10-57V, montaggio DIN opzionale e software avanzato senza costi di licenza) per raggiungere i requisiti IEC 62443 SL-2 al posto di brand Enterprise costosi (es. Fortinet)?

**Decisione Architetturale:** APPROVATO, con vincolo di standardizzazione tramite "Infrastructure as Code" (IaC).

**Giustificazione Normativa e Tecnica:**

La IEC 62443 è neutrale rispetto al vendor. Valuta l'effettiva presenza di controlli logici, non il marchio stampato sul dispositivo. L'uso di apparati basati su RouterOS/SwOS (MikroTik) soddisfa pienamente i requisiti SL-2:

* **Network Segmentation (SR 5.1):** Pieno supporto a 802.1Q (VLAN Tagging).  
* **Boundary Protection (SR 5.2):** RouterOS include uno Stateful Firewall (basato su iptables) estremamente potente per il "Deny-by-default".  
* **Port Security & Access (SR 1.1 / 2.1):** Supporto nativo integrato per Captive Portal, Hotspot, MAC Filtering e standard 802.1X, con costi zero per le licenze.  
* **Alimentazione:** Compatibilità nativa con i 24V DC standard dei quadri automazione.

**Controlli Compensativi Obbligatori (Mitigazione del Rischio Operativo):**

Poiché RouterOS è un sistema aperto e complesso, il rischio di "misconfiguration" umana (errore di programmazione delle regole) è alto e malvisto dagli auditor. Per mitigare questo rischio, Tecnopack impone che:

* Nessun dispositivo MikroTik venga configurato manualmente via GUI (WinBox) in produzione.  
* La configurazione debba avvenire *esclusivamente* tramite caricamento automatico di uno script/template standard validato dall'ufficio tecnico o iniettato via API dalla Dashboard Tecnopack.  
* Siano disabilitati di default tutti i servizi di management insicuri del dispositivo (es. Telnet, FTP, WebFig in HTTP, MAC-Telnet) lasciando attivo solo l'accesso crittografato (SSH/HTTPS).

## **QUESITO 9: Curva di Apprendimento e Deployment Massivo (MikroTik)**

**Domanda:** Considerata la nota complessità della riga di comando e dell'interfaccia di MikroTik (RouterOS), come garantiamo che i tecnici di assemblaggio o i trasfertisti non commettano errori di configurazione che invalidino la compliance IEC 62443 sul campo?

**Decisione Architetturale:** Automazione del deployment tramite Scripting Centralizzato (Zero-Touch Provisioning).

**Giustificazione Operativa e Normativa:**

L'approccio manuale "punta e clicca" sui firewall genera variazioni e vulnerabilità (violazione SR 3.3 \- Security Functionality Verification). Per superare la curva di apprendimento del prodotto, Tecnopack adotta la seguente procedura:

* **Ingegnerizzazione Centralizzata:** La progettazione delle regole di Firewall e VLAN (il file .rsc master) viene effettuata e validata una singola volta dall'Ufficio Tecnico / IT OT.  
* **Generazione Automatica:** La *Dashboard Tecnopack* prende in carico il file .rsc master. Inserendo semplicemente l'IP del PLC di destinazione, la Dashboard compila in tempo reale lo script personalizzato per la specifica macchina.  
* **Deployment a Bordo Macchina:** Il tecnico o il collaudatore esegue un semplice "Copia e Incolla" nel terminale (WinBox) del dispositivo MikroTik "vergine".  
  Questo metodo azzera la necessità di formazione specialistica per i trasfertisti su RouterOS, garantendo un'implementazione identica, auditabile e "Secure by Design" per ogni singola linea prodotta.

## **QUESITO 10: Sostituzione Switch Unmanaged e Gestione Alimentazione 24V**

**Domanda:** Considerando l'elevato costo degli switch industriali managed e dei dispositivi con funzionalità PoE (Power over Ethernet) spesso non necessarie per l'automazione di base, possiamo utilizzare switch MikroTik base (SwOS) senza PoE per ottenere la conformità SL-2? E come gestiamo l'alimentazione nei quadri?

**Decisione Architetturale:** SÌ. Adozione di switch MikroTik SwOS (es. serie CSS) non-PoE, alimentati a 24V DC.

**Giustificazione Normativa e Pratica:**

* **Razionalizzazione dei Costi (Niente PoE):** Poiché le telecamere, i PLC e gli HMI delle macchine sono alimentati separatamente dalle morsettiere di quadro, l'uso di switch con funzionalità "PoE-Out" rappresenta uno spreco economico e termico. I modelli MikroTik base (come il **CSS106-5G-1S** a 5 porte o il **CSS610-8G** a 8 porte) non forniscono PoE Out, abbattendo radicalmente il costo del dispositivo (sotto i 50-100€) pur garantendo tutte le feature L2 avanzate.  
* **Alimentazione Nativa Industriale (24V DC):** Tutti i dispositivi MikroTik selezionati supportano un range in ingresso amplissimo (tipicamente da 11V a 57V DC). Questo li rende nativamente compatibili con l'alimentatore a 24V DC standard sempre presente nei quadri di automazione.  
* **Cablaggio Diretto:** L'assenza di morsettiere integrate sullo chassis MikroTik (che usa il classico plug DC a barilotto) viene risolta cablando lo switch con un semplice "Adattatore DC a Morsettiera a Vite". Questo permette ai cablatori di portare i cavi della 24V e dello 0V dal power supply direttamente al plug dello switch, garantendo un'installazione industriale stabile e pulita.  
* **Vantaggi di Sicurezza (SL-2):** A parità di costo con uno switch unmanaged, l'introduzione dello SwOS garantisce il supporto nativo a **Port Lock/MAC Filtering** (SR 7.8), **VLAN 802.1Q** (SR 5.1) e **Storm Control** (SR 7.1), soddisfacendo appieno i requisiti del Security Level 2\.

## **QUESITO 11: Topologia di Linea (SD-Branch Equivalente) con MikroTik**

**Domanda:** Nelle linee complesse (es. 1 Macchina Master \+ N Flowpack), come replichiamo l'architettura centralizzata (SD-Branch) per l'inventario e la configurazione, sostituendo i protocolli proprietari (es. FortiLink) con hardware MikroTik?

**Decisione Architetturale:** Adozione di topologia "Hub and Spoke" basata su Trunk 802.1Q standard e gestione via API.

**Giustificazione Normativa e Tecnica:**

L'architettura di linea viene standardizzata svincolandosi dal lock-in dei vendor IT, utilizzando protocolli aperti di Livello 2:

* **Il Nodo Master (Cervello):** Nel quadro principale viene installato un singolo router MikroTik (es. RB5009 o hEX S) operante su RouterOS. Questo dispositivo svolge la funzione di gateway per la rete del cliente, Stateful Firewall di linea, server DHCP per le reti esterne e gestore del Captive Portal.  
* **I Nodi di Macchina (Muscoli):** Nei quadri delle singole Flowpack vengono installati switch MikroTik gestiti tramite SwOS (es. CSS610-8G).  
* **Il Backbone (Trunking Standard):** Il collegamento tra il quadro Master e i quadri macchina avviene tramite cavi Ethernet configurati in modalità **Trunk 802.1Q**. Sul tronco viaggiano contemporaneamente ma isolate a livello logico (SR 5.1):  
  * La VLAN della Supervisione (es. VLAN 200).  
  * La VLAN di Accesso Esterno/Manutenzione (es. VLAN 300).  
* **Network Inventory e Orchestrazione:** In assenza di un controller proprietario (es. FortiManager), l'orchestratore diventa la *Dashboard Tecnopack*. Tramite interrogazioni API dirette al router Master (via REST/RouterOS API), la Dashboard recupera la tabella ARP e i Lease DHCP, generando dinamicamente un inventario degli asset (SR 7.8) aggiornato in tempo reale.

## **QUESITO 12: Integrazione Wi-Fi sicura per Programmazione e Manutenzione (VLAN \+ AP)**

**Domanda:** Considerando la topologia di linea descritta nel Quesito 11, qual è il metodo normativamente più sicuro ed economicamente vantaggioso per fornire accesso locale di manutenzione/programmazione ai tecnici sulle singole macchine? Come regoliamo l'ampiezza dell'accesso?

**Decisione Architetturale:** Adozione di Access Point industriali a bordo macchina, segregati su VLAN di Manutenzione e protetti da Captive Portal centralizzato con policy di accesso "Subnet-based" o basate su Ruolo.

**Giustificazione Normativa e Tecnica:**

L'esposizione di prese RJ45 esterne sui quadri rappresenta un rischio fisico intrinseco. La soluzione ottimale prevede l'eliminazione dei connettori fisici esterni in favore di una copertura wireless mirata:

* **Hardware Dedicato:** Installazione all'interno del quadro (o antenna a pannello) di un Access Point compatto a 24V (es. MikroTik mAP o cAP).  
* **Segregazione Rigida L2:** L'Access Point diffonde un SSID nascosto o palese (es. "TP-Maint"), ma la porta dello switch SwOS a cui è fisicamente collegato è forzata in modalità Access (Untagged) sulla **VLAN di Manutenzione (es. VLAN 300\)**.  
* **Zero Trust Locale (Captive Portal):** Quando il manutentore si collega al Wi-Fi, riceve un IP dalla VLAN 300 isolata. Qualsiasi tentativo di pingare o raggiungere il PLC viene bloccato nativamente. L'unico traffico consentito è l'apertura di un browser web.  
* **Accesso Basato sui Ruoli (RBAC \- Routing Dinamico):** Solo dopo aver inserito credenziali valide nel Captive Portal, il Master crea dinamicamente una regola di routing Layer 3 temporanea. In base al ruolo dell'utente, questa regola sblocca l'accesso **all'intera VLAN Supervisione (es. 172.16.224.0/24)** per permettere una diagnostica di linea completa (es. interazione tra Flowpack e Quadro Master), o a singoli IP per utenti base (SR 1.1, SR 2.1). Il firewall continua a bloccare in modo assoluto l'accesso verso la rete aziendale IT del cliente e verso Internet, garantendo il confinamento dell'intervento all'interno della *Cell/Area Zone*.  
* **Vantaggio Operativo:** Il tecnico può programmare muovendosi liberamente attorno alla macchina avendo visibilità sull'intera topologia OT della linea. Quando l'intervento è concluso, il rilascio della sessione (o lo spegnimento dell'AP) ripristina un "Air-Gap" fisico e logico totale.

## **QUESITO 13: Asset Inventory Passivo e API (Evoluzione rispetto a Nmap)**

**Domanda:** Sostituendo gli switch unmanaged con apparati MikroTik (RouterOS/SwOS), la *Dashboard Tecnopack* come può evolvere il suo sistema di rilevamento asset, superando i limiti e i rischi legati alle attuali scansioni attive di rete tramite nmap?

**Decisione Architetturale:** Transizione da "Active Scanning" (Nmap) a "Passive Infrastructure Polling" (REST API).

**Giustificazione Operativa e Normativa:**

L'utilizzo di sonde attive come nmap in un ambiente OT è intrinsecamente rischioso (rischio di saturazione stack TCP/IP di vecchi PLC) e fornisce una visibilità parziale limitata ai dispositivi che rispondono al protocollo ICMP. L'adozione di un'infrastruttura di rete intelligente (MikroTik) permette alla Dashboard di recuperare dati critici in tempo reale interrogando nativamente il firewall e gli switch:

* **Identificazione Hardware Esatta (LLDP/CDP):** Interrogando gli switch di macchina, la Dashboard estrae i pacchetti LLDP. Questo permette di scoprire la marca, il modello e persino il nome host di PLC/HMI (es. "Omron NJ501") in modo completamente passivo, colmando il divario di visibilità lasciato da Nmap.  
* **Mappatura L2 (Port-to-MAC Binding):** Le API dello switch restituiscono l'esatta mappatura delle porte fisiche (es. MAC Address della bilancia connesso alla porta 4). Eventuali spostamenti fisici di cavi vengono tracciati immediatamente, facilitando il rispetto del requisito IEC 62443 **SR 7.8 (Asset Inventory)**.  
* **Mappatura Flussi L3/L4 (Conduit Validation):** Interrogando la tabella *Connection Tracking* del firewall RouterOS, la Dashboard estrae l'elenco in tempo reale di "chi parla con chi". Oltre a vedere un semplice IP, la Dashboard evidenzia le connessioni attive (es. 172.16.x.11 \-\> 172.16.x.31 su porta TCP 4840), validando empiricamente il traffico consentito nei *Conduit* (SR 5.2).  
* **Sicurezza Operativa:** Le chiamate REST API dirette all'hardware di rete non generano traffico estraneo sulla rete OT delle macchine, preservando il determinismo (zero jitter o packet loss per i protocolli Safety) essenziale per la certificazione.

## **QUESITO 14: Coesistenza con Secomea Prime e SiteManager (Teleassistenza)**

**Domanda:** Con l'adozione di MikroTik come Stateful Firewall e switch L2 per la sicurezza locale, come integriamo i gateway hardware **Secomea SiteManager** per mantenere il nostro standard di teleassistenza basato su **Secomea Prime** e Azure AD (Entra ID)?

**Decisione Architetturale:** Modello "Defense-in-Depth" (Separation of Duties). Secomea gestisce l'Identità/VPN, MikroTik gestisce l'Enforcement di Rete (Routing/Firewall).

**Giustificazione Operativa e Normativa:**

La rimozione di Fortinet a favore di MikroTik non impatta, ma anzi valorizza, l'uso di Secomea. L'architettura IEC 62443 SL-2 si ottiene applicando rigorosamente la separazione dei compiti:

* **Isolamento in Uscita (Outbound Firewalling):** Di default, il router MikroTik (Master) blocca *tutto* il traffico in uscita verso Internet generato dalla fabbrica (PLC, HMI, dispositivi) ottemperando alla *Boundary Protection* (SR 5.2). L'unica deroga viene creata per il MAC Address/IP statico del **Secomea SiteManager**, a cui il MikroTik permette di uscire esclusivamente sulle porte necessarie (es. TCP 11444, HTTPS 443\) per raggiungere i server Secomea GateManager in cloud. Nessun altro dispositivo può uscire su Internet.  
* **Integrazione "Zero Trust" (FR 1):** Il MikroTik Master delega totalmente l'autenticazione remota a Secomea. Quando un trasfertista si collega da casa, non interroga il MikroTik, ma passa dal portale Secomea Prime dove subisce l'autenticazione forte (MFA tramite Azure AD). Questo scarica il MikroTik da logiche complesse di VPN e garantisce conformità alla NIS2.  
* **Monitoraggio e Confinamento del Traffico Entrante (Inbound):** Quando il tunnel Secomea è attivo, il SiteManager "inietta" il traffico del trasfertista all'interno della *VLAN Supervisione (es. VLAN 200\)*. Poiché il traffico passa fisicamente attraverso gli switch MikroTik SwOS, la *Dashboard Tecnopack* rileva l'attività e il MikroTik Master può loggare quali IP il trasfertista sta interrogando localmente, fornendo un "Audit Trail" completo (SR 2.8).  
* **Controllo Fisico dell'Asset Owner:** Il SiteManager Secomea è dotato di input digitali. Collegando un interruttore a chiave (hardware) a bordo quadro all'ingresso digitale del Secomea, il cliente finale (Asset Owner) può tagliare fisicamente la connessione VPN disabilitando la teleassistenza quando non desiderata, mantenendo comunque attivo il firewall MikroTik per la protezione locale.

## **QUESITO 15: Posizionamento Fisico e Logico del Collegamento Esterno (WAN Cliente e Secomea)**

**Domanda:** A livello di cablaggio fisico, in una topologia governata da un MikroTik Master (nel quadro principale), come gestiamo il collegamento verso la rete IT del cliente e dove devono essere collegate la porta **UPLINK** (WAN) e la porta **DEV** (LAN) del Secomea SiteManager per garantire l'isolamento (Boundary Protection) ed evitare bypass del firewall?

**Decisione Architetturale:** MikroTik come "Guardiano della Soglia". Porta WAN dedicata al cliente. UPLINK Secomea su porta controllata dal MikroTik, DEV su porta "Access" in VLAN Supervisione.

**Giustificazione Normativa e Tecnica:**

Per soddisfare il requisito **SR 5.2 (Zone Boundary Protection)** della IEC 62443, la topologia di cablaggio deve forzare *tutto* il traffico entrante/uscente (incluso il traffico di teleassistenza) a passare attraverso lo Stateful Firewall:

* **Il Cavo verso il Cliente (Porta UPLINK del MikroTik):** Una porta specifica del MikroTik Master (es. ether1) viene designata come unica interfaccia fisica "Esterna/WAN". Questa è l'unica porta che l'IT del cliente finale dovrà collegare alla propria rete di stabilimento. Il firewall su questa porta è configurato in rigoroso **"Deny-by-Default"**. Il traffico IT non può entrare e il traffico macchina non può uscire, salvo esplicite eccezioni concordate (i cosiddetti *Conduit*, es. la porta TCP 4840 per l'OPC-UA verso il MES del cliente).  
* **Gestione della Porta UPLINK del Secomea (WAN):** Per permettere al Secomea di raggiungere i server in cloud per la VPN, la sua porta UPLINK **non viene collegata direttamente** alla rete del cliente, ma a una porta dedicata del MikroTik Master (es. ether2). Il MikroTik crea una regola "Outbound" specifica che consente al solo MAC Address/IP di quella porta Secomea di uscire tramite la porta ether1 (verso Internet), bloccando qualsiasi altro tentativo di connessione. Questo garantisce che un eventuale compromissione del Secomea non possa propagarsi verso il cliente.  
* **Gestione della Porta DEV del Secomea (LAN):** Questa porta inietta il traffico del manutentore remoto (che ha superato l'autenticazione MFA su Secomea Prime) verso le macchine. Va cablata fisicamente su una porta del MikroTik Master configurata in modalità **Access (Untagged)** e assegnata in via esclusiva alla **VLAN Supervisione (es. VLAN 200\)**.  
  * *Il vantaggio logico:* Il Secomea riceve un IP della subnet di Supervisione (es. 172.16.224.254). Essendo il traffico forzato a entrare in una VLAN gestita dal MikroTik Master, il firewall **intercetta, analizza e traccia** (tramite la sua tabella di *Connection Tracking*) ogni singolo pacchetto che il manutentore remoto invia ai PLC. Questo elimina i "punti ciechi" sulla rete e garantisce la conformità piena al requisito **SR 2.8 (Auditable Events)**.

## **QUESITO 16: Integrazione SCADA/MES del Cliente e Gestione Protocolli (Conduit IT/OT)**

**Domanda:** Come regolamentiamo e proteggiamo l'accesso dalla rete IT del cliente (SCADA, MES, ERP) verso i nostri PLC di macchina? Quali protocolli di comunicazione sono ammessi o sconsigliati per mantenere la compliance IEC 62443 SL-2 sul confine di zona?

**Decisione Architetturale:** Adozione prioritaria di OPC UA e traffico TCP instradabile. Divieto di Modbus TCP e limitazione di protocolli L2 (Profinet) sul confine IT/OT.

**Giustificazione Normativa e Tecnica:**

Il collegamento tra l'infrastruttura del cliente e la *Cell/Area Zone* di Tecnopack costituisce il *Conduit* principale (SR 5.2). Per garantire che questo flusso dati non diventi un vettore d'attacco, si applicano le seguenti regole implementate sul Firewall MikroTik Master:

1. **Deny-by-Default e Whitelisting (Micro-segmentazione L3/L4):** La porta WAN del MikroTik connessa al cliente blocca tutto il traffico in ingresso. L'apertura viene effettuata creando regole firewall univoche che specificano:  
   * **Source IP:** Esclusivamente l'IP statico del Server SCADA/MES del cliente.  
   * **Destination IP:** Esclusivamente l'IP del PLC Master Tecnopack designato allo scambio dati.  
   * **Destination Port:** Esclusivamente la porta TCP del protocollo autorizzato (es. TCP 4840).  
     In questo modo, anche se la rete IT del cliente viene infettata da un ransomware che scansiona la rete, i pacchetti verranno scartati dal firewall.  
2. **Protocolli Ammessi (OPC UA / EtherNet/IP):**  
   * L'architettura supporta e raccomanda caldamente l'utilizzo di **OPC UA (TCP 4840\)**. Essendo un protocollo Layer 3 nativo, attraversa i firewall in modo pulito e supporta nativamente crittografia e autenticazione tramite certificati, ottemperando ai requisiti **SR 1.1 (Autenticazione)** e **SR 4.1 (Riservatezza)**.  
   * È tollerato l'uso di **EtherNet/IP (TCP 44818\)** per l'Explicit Messaging (lettura dati da SCADA), in quanto instradabile e filtrabile tramite regole firewall L3/L4 rigide.  
3. **Protocolli Sconsigliati/Vietati (Modbus TCP / Profinet):**  
   * **Modbus TCP (TCP 502):** Fortemente sconsigliato per le comunicazioni esterne. Essendo un protocollo "in chiaro" e privo di qualsiasi meccanismo di autenticazione (chiunque conosca l'IP può scrivere nei registri fermando la macchina), il suo uso degrada la compliance. Se il cliente lo impone, l'unica mitigazione SL-2 accettabile è un blocco IP/MAC ferreo sul MikroTik Master.  
   * **Profinet:** Sconsigliato per il *Conduit* esterno verso i server IT. Profinet (nelle varianti RT) fa largo uso di comunicazioni Layer 2 (MAC-based) non instradabili attraverso i normali firewall IP. L'architettura Tecnopack prevede che protocolli deterministici Layer 2 vivano *esclusivamente* confinati all'interno della *Cell/Area Zone*, isolati dietro il firewall.

## **QUESITO 17: Regole NAT vs Routing e il "Problema" dell'OPC UA**

**Domanda:** Per consentire l'accesso del MES del cliente (SCADA) al PLC via OPC UA, è preferibile esporre l'IP reale interno del PLC (Routing puro) o utilizzare un IP Alias/NAT del cliente (es. 192.168.250.225:4840 \-\> 172.16.224.11:4840)? E quali problematiche tecniche genera il NAT con il protocollo OPC UA?

**Decisione Architetturale:** Adozione esclusiva del Destination NAT (Port Forwarding / IP Alias) per standardizzazione OEM. Gestione delle anomalie OPC UA lato SCADA (Bypass Endpoint).

**Giustificazione Operativa e Tecnica:**

Per un costruttore di macchine di serie (OEM) come Tecnopack, mantenere l'uniformità della rete interna è imperativo. L'architettura prescrive quanto segue:

1. **Il Vantaggio del NAT (Port Forwarding):** Non chiediamo mai all'IT del cliente di inserire "rotte statiche" (Static Routes) nei loro router verso le nostre sottoreti 172.16.x.x. Assegniamo invece alla porta WAN del firewall MikroTik Master un IP della rete del cliente (IP Alias, es. 192.168.250.225). Il firewall tramite *Destination NAT (DST-NAT)* instrada i pacchetti TCP 4840 dall'IP Alias direttamente al PLC. Questa tecnica non solo evita conflitti IP, ma rafforza il concetto di *Zone Boundary* nascondendo la topologia interna (Topology Hiding).  
2. **La Problematica dell'Endpoint OPC UA:** A differenza del Modbus, l'OPC UA è un protocollo "self-describing". Quando lo SCADA del cliente interroga l'Alias 192.168.250.225:4840, il PLC risponde trasmettendo al cliente l'URL del proprio *Endpoint* contenente il suo vero IP interno (es. opc.tcp://172.16.224.11:4840). Lo SCADA standard, ricevendo un IP diverso da quello interrogato, rifiuterà la connessione e, se i certificati crittografici X.509 sono vincolati all'IP reale, genererà un errore di sicurezza.  
3. **Risoluzione Concordata (Guidelines per il Cliente):** Per ovviare al comportamento dell'OPC UA in ambienti NATtati senza degradare la sicurezza, l'ufficio tecnico Tecnopack fornirà indicazioni chiare agli integratori SCADA del cliente:  
   * **Configurazione Client SCADA:** È tassativo istruire il software SCADA (es. Ignition, Kepware, WinCC) ad attivare l'impostazione nativa **"Ignore Endpoint URL"** (o "Use Connection IP"). Questo forza lo SCADA a mantenere il tunnel sicuro sull'IP Alias concordato (192.168.250.225), bypassando l'IP interno restituito dal PLC.  
   * **Gestione Certificati (SAN):** In fase di setup della macchina (se l'autenticazione tramite certificati è richiesta), Tecnopack genererà il certificato OPC UA del PLC inserendo l'IP Alias del cliente o il nome DNS all'interno del campo *Subject Alternative Name (SAN)*, prevenendo errori di mismatch.

## **QUESITO 18: Sincronizzazione Temporale (NTP) e validità dei Log**

**Domanda:** Per garantire che i file di log (Audit Trail) del PLC, dell'HMI e del Firewall abbiano valenza in fase di analisi post-incidente, come deve essere gestita la sincronizzazione dell'orologio (Timestamp) su una macchina isolata dalla rete aziendale?

**Decisione Architetturale:** Il MikroTik Master opera come Server NTP (Network Time Protocol) locale autorevole per la Cell/Area Zone.

**Giustificazione Normativa e Tecnica:**

Il requisito IEC 62443 **SR 2.9 (Audit Storage capacity) e SR 6.1 (Audit Log accessibility)** presuppone implicitamente che i log generati da diversi asset siano temporalmente allineati, altrimenti la correlazione degli eventi diventa impossibile e invalida l'assessment.

1. Il router MikroTik Master (essendo collegato alla WAN del cliente o a Internet via Secomea) si sincronizza con un Server NTP esterno sicuro (fornito dal cliente o server pubblici).  
2. Il MikroTik viene configurato per operare in modalità "NTP Server" per la subnet interna (172.16.x.x).  
3. Tutti gli asset interni (PLC, HMI, switch, telecamere) devono essere tassativamente configurati per puntare all'IP interno del MikroTik (es. 172.16.224.1) come loro unica sorgente del tempo, garantendo un'accuratezza al millisecondo trasversale su tutta la linea.

## **QUESITO 19: Integrazione Log con i sistemi del Cliente (Syslog/SIEM)**

**Domanda:** Se il reparto IT del cliente finale (Asset Owner) esige l'invio dei log di sicurezza della nostra macchina al loro server di monitoraggio centrale (es. Splunk, QRadar, SIEM) per la conformità NIS2, come soddisfiamo la richiesta senza compromettere la sicurezza della cella?

**Decisione Architetturale:** Configurazione di Log Forwarding unidirezionale (Syslog) tramite MikroTik Master.

**Giustificazione Normativa e Tecnica:**

La convergenza IT/OT richiede trasparenza sugli eventi di confine (Zone Boundary). La IEC 62443 incoraggia l'invio di log di sicurezza verso sistemi centralizzati (SR 6.1).

RouterOS supporta nativamente l'azione di Remote Logging. L'Ufficio Tecnico Tecnopack configurerà il MikroTik Master affinché intercetti eventi specifici (es. tentativi di accesso al firewall, blocchi/Drop di pacchetti provenienti dalla rete cliente, login al Captive Portal locale) e li invii in formato Syslog (UDP 514 o TCP crittografato) direttamente all'IP del server SIEM del cliente, attraverso una singola regola firewall Outbound mirata. In questo modo il cliente monitora la sicurezza perimetrale della macchina senza dover installare software agent invasivi sui PLC.

## **QUESITO 20: Disaster Recovery dell'Infrastruttura di Rete (Rimpiazzo Hardware)**

**Domanda:** In caso di guasto hardware fatale (es. sovratensione) al router MikroTik Master o a uno switch SwOS, come si garantisce un ripristino rapido (Disaster Recovery) senza richiedere la presenza in loco di un ingegnere di rete, rispettando i requisiti di disponibilità?

**Decisione Architetturale:** Standardizzazione del Backup "Text-Based" e ripristino via Dashboard/USB.

**Giustificazione Normativa e Tecnica:**

Il requisito IEC 62443 **SR 7.3 (Control System Backup)** si applica anche agli asset di rete, non solo ai PLC. Data la complessità del sistema operativo RouterOS, la Business Continuity è garantita da procedure rigide:

1. Al momento del collaudo finale della macchina (FAT), la configurazione del MikroTik viene esportata come script leggibile (file .rsc) e lo SwOS come file di backup.  
2. Questi file vengono archiviati nel database in cloud di Tecnopack (associati alla matricola macchina) e forniti fisicamente su un supporto di memoria (USB Flash Drive) consegnato insieme agli schemi elettrici nel quadro macchina.  
3. Qualsiasi elettricista o manutentore del cliente finale può sostituire l'hardware rotto con un dispositivo MikroTik vergine commerciale, collegarsi tramite WinBox e incollare il contenuto del file di testo nel terminale. L'operazione ripristina Firewall, VLAN, Captive Portal e regole NAT in meno di 2 minuti senza errori di battitura.

## **QUESITO 21: Hardening del Firewall (Chi controlla il controllore?)**

**Domanda:** Se il firewall MikroTik Master protegge i PLC, come proteggiamo il firewall stesso da tentativi di accesso o hacking provenienti dalla rete IT del cliente, assicurandoci che nessuno manipoli le regole di sicurezza?

**Decisione Architetturale:** Chiusura assoluta dei servizi di management e restrizione dell'accesso (Hardening).

**Giustificazione Normativa e Tecnica:**

La IEC 62443 richiede che gli apparati di rete siano soggetti a procedure di Hardening severe (SR 3.3). Un router lasciato con le impostazioni di fabbrica è un vettore d'attacco primario.

La configurazione standard del MikroTik Master Tecnopack prevede obbligatoriamente:

1. **Drop Management da WAN:** Una regola firewall assoluta (sulla catena Input) che blocca qualsiasi tentativo di accesso alle interfacce di gestione del router (WinBox, SSH, WebFig) proveniente dalla porta ether1 (la porta collegata alla rete del cliente). Il firewall è un muro invalicabile dal lato IT.  
2. **Disabilitazione Servizi Insecuri:** Spegnimento definitivo dei servizi in chiaro come Telnet, FTP, WWW (HTTP) e MAC-Telnet.  
3. **Accesso Consentito Solo da VLAN di Manutenzione:** L'accesso al management del router è permesso *esclusivamente* a chi è fisicamente connesso all'Access Point interno della macchina (VLAN 300\) e ha superato il Captive Portal, oppure tramite il tunnel VPN sicuro fornito da Secomea Prime.

## **QUESITO 22: Centralizzazione Log (Fleet SIEM) \- Obbligo Normativo o Servizio Premium?**

**Domanda:** Alla luce delle normative CRA e NIS2, Tecnopack è *obbligata per legge* a estrarre e centralizzare i log di tutte le macchine vendute nel mondo all'interno di un proprio cloud (Fleet SIEM)? E qual è la strategia architetturale ed economica in merito a strumenti Open-Source come Wazuh o Loki?

**Decisione Architetturale:** La centralizzazione sul cloud Tecnopack NON è un obbligo di legge, ma è implementata tecnologicamente come **Servizio Premium Opzionale (Security-as-a-Service)** a tutela dell'OEM e dell'Asset Owner.

**Giustificazione Operativa e Normativa:**

È fondamentale distinguere tra obbligo legale e strategia di business:

* **Cosa è obbligatorio:** La IEC 62443 richiede che la macchina sia in grado di produrre log e resi disponibili all'Asset Owner (es. inoltrati al SIEM del cliente finale, come descritto al Quesito 19). Il CRA obbliga a gestire le vulnerabilità, non a spiare le reti dei clienti H24.  
* **Il Vincolo di Privacy:** Molti clienti industriali (Pharma, Difesa) vietano esplicitamente l'esfiltrazione di dati di log verso datacenter esterni, esigendo sistemi "Air-Gapped". Rendere la connessione cloud "obbligatoria" violerebbe i loro capitolati di sicurezza.

**La Strategia "Fleet SIEM" Tecnopack (Perché farlo come Servizio Premium):**

Anche se non obbligatorio, creare un cluster centrale Open-Source (basato su **Wazuh**, **Elastic/OpenSearch** o **Grafana Loki**) offre a Tecnopack un vantaggio competitivo e legale inestimabile:

1. **Plausibile Negabilità (Scarico Responsabilità):** Se una linea si ferma per un attacco cyber proveniente dalla rete IT del cliente, Tecnopack può utilizzare i log archiviati sul proprio SIEM per dimostrare inconfutabilmente che la colpa è dell'infrastruttura del cliente e non di un difetto della macchina, tutelandosi da cause legali milionarie.  
2. **Nuovo Modello di Business (MDR):** Tecnopack si evolve da puro fornitore hardware a fornitore di "Managed Detection and Response". I clienti che non hanno un reparto IT strutturato possono pagare un canone ricorrente affinché Tecnopack monitori le anomalie del loro firewall MikroTik di macchina.  
3. **Flusso Dati Sicuro:** Solo previo esplicito consenso contrattuale, il router MikroTik Master invierà i log in formato Syslog tramite tunnel VPN cifrato (Secomea o Wireguard) verso il cluster cloud Tecnopack, garantendo la confidenzialità dei dati (SR 4.1) anche su reti pubbliche.