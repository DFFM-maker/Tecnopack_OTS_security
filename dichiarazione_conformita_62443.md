# Dichiarazione di Conformità Cybersecurity (IEC 62443)

**Costruttore:** Tecno Pack S.p.A.  
**Oggetto:** Infrastruttura di Controllo e Rete (SUC)  
**Security Level Target:** SL-2 (Security Level 2)

## 1. ATTESTAZIONE DI CONFORMITÀ
Si dichiara che la macchina/linea è progettata secondo il **Tecnopack OT Security Blueprint**, soddisfacendo i requisiti **IEC 62443-3-3** e **4-2** per il livello SL-2.

## 2. REQUISITI IMPLEMENTATI
* **FR 1/2:** MFA remota (Secomea/Azure AD) e Captive Portal locale con timeout 30m.
* **FR 3:** Protezione sorgenti PLC/HMI e Audit Trail attivo.
* **FR 4 - Riservatezza dei Dati (DC):** Utilizzo di protocolli crittografici (TLS) per le comunicazioni esterne via Secomea VPN. Le comunicazioni interne alla Cell/Area Zone non richiedono cifratura in SL-2 per requisiti di determinismo e bassa latenza.
* **FR 5:** Segmentazione Zone/Condotti via Stateful Firewall (Deny-by-default).
* **FR 6/7:** Logging centralizzato (Wazuh ready), Port Lock e Disaster Recovery via script IaC.

## 3. RESPONSABILITÀ ASSET OWNER
* La violazione fisica dei quadri elettrici (apertura senza autorizzazione) invalida la conformità.
* La gestione delle password locali è a carico esclusivo del cliente finale.

---
*Dipartimento OT Security - Tecno Pack S.p.A.*