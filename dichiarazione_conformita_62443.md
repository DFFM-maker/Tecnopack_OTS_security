# **Dichiarazione di Conformità Cybersecurity (IEC 62443\)**

**Costruttore:** Tecno Pack S.p.A.

**Oggetto:** Infrastruttura di Controllo e Rete (SUC \- System Under Consideration)

**Standard di Riferimento:** IEC 62443-3-3 / IEC 62443-4-2

**Security Level Target:** SL-2 (Security Level 2\)

## **1\. ATTESTAZIONE DI CONFORMITÀ**

Tecno Pack S.p.A. dichiara che la macchina/linea identificata in calce è stata progettata e realizzata seguendo il **Tecnopack OT Security Blueprint**. L'architettura logica e hardware è conforme ai requisiti del **Security Level 2 (SL-2)** definiti dallo standard internazionale **IEC 62443**, garantendo la protezione contro violazioni intenzionali condotte con mezzi semplici e risorse limitate.

## **2\. REQUISITI FONDAMENTALI (FR) IMPLEMENTATI**

In conformità alla norma IEC 62443-3-3, sono stati implementati i seguenti controlli:

* **FR 1 \- Identificazione e Autenticazione (IAC):** L'accesso remoto è protetto da cifratura end-to-end e autenticazione a più fattori (MFA) tramite integrazione Azure AD. L'accesso locale di manutenzione è vincolato a un portale di autenticazione (Captive Portal).  
* **FR 2 \- Controllo dell'Uso (UC):** Implementazione di privilegi basati sui ruoli (RBAC). Le sessioni di manutenzione sono soggette a timeout automatico (30 min) per prevenire accessi non presidiati.  
* **FR 3 \- Integrità del Sistema (SI):** Protezione dei file di progetto PLC/HMI tramite crittografia e password. Funzioni di Audit Trail attive per la tracciabilità delle modifiche ai parametri critici.  
* **FR 5 \- Restrizione dei Flussi (RDF):** Segmentazione della rete in Zone e Condotti (Firewall Stateful). Politica di traffico "Deny-by-Default" e occultamento della topologia interna (NAT/Topology Hiding).  
* **FR 6 \- Risposta agli Eventi (TRE):** Sistema di logging centralizzato (Syslog) predisposto per il monitoraggio continuo delle anomalie di sicurezza.  
* **FR 7 \- Disponibilità delle Risorse (RA):** Protezione fisica delle porte di rete (Port Lock/MAC Filtering) e procedure di backup validate per il Disaster Recovery.

## **3\. MODELLO DI RESPONSABILITÀ CONDIVISA (SHARED RESPONSIBILITY)**

La sicurezza della macchina è un processo congiunto. Tecno Pack garantisce la sicurezza "By Design" del sistema, mentre l'**Asset Owner (Cliente)** è responsabile della gestione operativa:

1. **Sicurezza Fisica:** Il quadro elettrico e i pensili HMI costituiscono il perimetro di sicurezza fisica. L'accesso non autorizzato all'interno dei quadri (elusione delle chiavi di chiusura) invalida i controlli compensativi di conformità.  
2. **Gestione Credenziali:** Il cliente è responsabile della custodia e della segretezza delle password fornite per l'accesso ai sistemi.  
3. **Aggiornamenti:** La conformità nel tempo è garantita dalla sottoscrizione dei piani di manutenzione e patching (CRA Compliance) definiti nel contratto di fornitura.

## **4\. IDENTIFICAZIONE MACCHINA**

* **Modello:** \[Inserire Modello\]  
* **Matricola:** \[Inserire Matricola\]  
* **Data di Collaudo:** \[Inserire Data\]  
* **Profilo Hardware:** \[ \] MikroTik Lean | \[ \] Siemens Ready | \[ \] Fortinet Ready

**Approvazione Tecnica (Tecno Pack OT Security Dept.)** *Firma e Timbro*