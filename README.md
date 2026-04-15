# **Tecnopack OT Security Repository v1.0**

**Standard di Conformità IEC 62443 \- Security Level 2 (SL-2)**

Questo repository costituisce il corpus documentale ufficiale di Tecnopack per la gestione della Cybersecurity industriale. L'approccio adottato è "Agnostico" (No Vendor), garantendo la conformità normativa indipendentemente dai componenti hardware scelti.

## **📂 Struttura della Documentazione**

### **📖 0\. Onboarding (Per Tutti)**

Documentazione accessibile per chi non conosce la norma IEC 62443.

* [**Guida IEC 62443 per Tutti**](guida_62443_per_tutti.md): Spiegazione della norma in italiano semplice. Pilastri FR, glossario, checklist conformita', impatto per ogni ruolo.

### **🛡️ 1\. Strategia e Governance (Agnostica)**

In questa sezione sono presenti i documenti che definiscono il "Cosa" e il "Perché" dell'architettura di sicurezza.

* [**Design Decision Log Agnostico**](design_log_agnostico.md): Registro delle decisioni architetturali mappate sui requisiti **SR (System Requirements)** della norma. Include il framework **CSMS Lite** per la gestione di vulnerabilità e incidenti.  
* [**Dichiarazione di Conformità IEC 62443**](dichiarazione_conformita_62443.md): Modello ufficiale da allegare al fascicolo tecnico della macchina per attestare il raggiungimento del livello **SL-2**.

### **⚙️ 2\. Implementazione Hardware e Costi**

Documenti operativi per la scelta dei componenti e la valutazione economica.

* [**Hardware BOM e Analisi Vendor**](hardware_bom_completa.md): Matrice hardware comparativa e distinta base per le topologie "Macchina Singola" e "Linea Row Distribution".
* [**Analisi Comparativa Costi**](analisi_costi_hardware.md): Dettaglio dei costi CAPEX e OPEX (5 anni) che evidenzia il risparmio dello standard **MikroTik Lean** rispetto ai vendor Premium.
* [**Profili Vendor-Ready**](vendor_ready_profiles.md): Schede tecniche di implementazione per Fortinet, Siemens e MikroTik.

### **📊 3\. Diagrammi di Rete**

Diagrammi Draw.io per le topologie Macchina Singola e Linea, per entrambi gli standard hardware.

* `Fortinet Network Layout.drawio` — 3 pagine: Macchina Singola, Magnemotion, Linea (FortiLink)
* `MikroTik_Network_Layout.drawio` — 2 pagine: Macchina Singola, Linea (Hub & Spoke / Trunk 802.1Q)

Documenti operativi per la scelta dei componenti e la valutazione economica.

* [**Hardware BOM e Analisi Vendor**](hardware_bom_completa.md): Matrice hardware comparativa e distinta base per le topologie "Macchina Singola" e "Linea Row Distribution".  
* [**Analisi Comparativa Costi**](analisi_costi_hardware.md): Dettaglio dei costi CAPEX e OPEX (5 anni) che evidenzia il risparmio dello standard **MikroTik Lean** rispetto ai vendor Premium.  
* [**Profili Vendor-Ready**](vendor_ready_profiles.md): Schede tecniche di implementazione per Fortinet, Siemens e MikroTik.

### **💻 3\. Strumenti Tecnici e Automazione (IaC)**

Risorse per il deployment rapido e la configurazione degli apparati.

* [**Template Configurazione MikroTik v1.4**](tecnopack_routeros_v1.4.txt): Script "Infrastructure as Code" per apparati RouterOS. Implementa automaticamente Firewall, VLAN, Captive Portal e logica RBAC.

## **🚀 Workflow Operativo**

1. **Definizione:** Consultare il *Design Decision Log* per identificare i requisiti normativi.  
2. **Quotazione:** Utilizzare l'*Analisi Costi* per presentare la soluzione al cliente (Lean vs Premium).  
3. **Approvvigionamento:** Estrarre i codici prodotto dalla *Hardware BOM*.  
4. **Configurazione:** Caricare il *Template v1.4* sull'hardware MikroTik.  
5. **Certificazione:** Firmare la *Dichiarazione di Conformità* dopo il collaudo (FAT).

*Dipartimento OT Security \- Tecno Pack S.p.A.*