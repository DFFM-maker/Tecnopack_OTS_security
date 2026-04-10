# Design Decision Log: Architettura OT Agnostica (IEC 62443 SL-2)

**Progetto:** Standardizzazione Sicurezza Tecnopack
**Obiettivo:** Ottenimento conformità SL-2 (3-3, 4-2) e gestione CSMS (2-1).

---

## 1. PROTEZIONE PERIMETRALE (FR 5)
* **Decisione 1.1 (SR 5.2):** Firewall Stateful "Deny-by-default". Blocco management da porta WAN cliente per evitare intrusioni dall'IT.
* **Decisione 1.2 (SR 4.1):** Conduit via OPC-UA (TCP 4840) e Topology Hiding tramite NAT/Alias IP per nascondere l'indirizzamento interno.

## 2. GESTIONE ACCESSI (FR 1 & FR 2)
* **Decisione 2.1 (SR 7.8):** Accesso fisico Wi-Fi attivato esclusivamente tramite **selettore a chiave** a bordo quadro. Controllo compensativo per l'assenza di RJ45 esterne.
* **Decisione 2.2 (SR 2.1):** Captive Portal su VLAN segregata (300) con **idle-timeout (30m)** e session-timeout (4h) per prevenire sessioni orfane.

## 3. INTEGRITÀ E INVENTARIO (FR 7)
* **Decisione 3.1 (SR 7.8):** MAC Locking (Port Lock) obbligatorio su switch di bordo. Azione "Drop" su dispositivi sconosciuti per proteggere l'integrità fisica delle porte PLC/HMI.
* **Decisione 3.2 (SR 7.8):** Inventario asset passivo tramite polling API Dashboard Tecnopack per monitoraggio versioni firmware e CVE.

## 4. LOGGING E MONITORAGGIO (FR 6)
* **Decisione 4.1 (SR 2.8):** Centralizzazione log via Syslog (Wazuh). In modalità **Air-Gap**, salvataggio su buffer locale (SD/Disk) per analisi post-evento.

---

## 5. CSMS LITE - GOVERNANCE (IEC 62443-2-1)
*Queste decisioni coprono il sistema di gestione della sicurezza dell'organizzazione (CSMS).*

**Decisione 5.1: Vulnerability & Patch Management (Step 8)**
* **Monitoring:** La Dashboard Tecnopack monitora quotidianamente i database CVE (NVD/CISA) per gli asset censiti.
* **SLA Valutazione:** Il Security Coordinator valuta l'impatto delle vulnerabilità entro **15 giorni** dalla rilevazione.
* **SLA Patching:** Le patch critiche approvate devono essere applicate entro **30 giorni** (o al primo fermo macchina utile).

**Decisione 5.2: Incident Response Protocol (Step 9)**
* **Rilevazione:** Notifica via Dashboard di anomalie (es. Brute force, MAC Spoofing).
* **Contenimento:** Disattivazione fisica dell'Access Point (via chiave) e isolamento della porta WAN cliente.
* **Analisi & Recovery:** Estrazione log locali e ripristino configurazione "Golden Image" (Backup SR 7.3).

**Decisione 5.3: Formazione & Awareness (Step 10)**
* **Requisito:** Training annuale obbligatorio per tecnici e softwaristi sulle procedure di igiene cyber e uso sicuro del Blueprint.