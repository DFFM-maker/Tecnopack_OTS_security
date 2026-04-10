# Profili Vendor-Ready: Analisi Comparativa

Questo documento dettaglia le caratteristiche tecniche, i vantaggi e le limitazioni dei vendor analizzati per l'architettura Tecnopack OT Security (IEC 62443 SL-2).

---

## 1. PROFILO MIKROTIK (Standard Tecnopack Lean)
*Ideale per la produzione di serie e macchine con budget ottimizzato.*

* **Hardware di riferimento:** RouterOS (RB5009/hEX S) + SwOS (CSS610).
* **Vantaggi:**
    * **Zero Licenze:** Aggiornamenti firmware gratuiti a vita (Compliance CRA a costo zero).
    * **Automazione IaC:** Piena integrazione con la Dashboard Tecnopack via REST API e scripting .rsc.
    * **Performance:** Latenza minima grazie all'architettura snella senza ispezione DPI pesante.
* **Criticità:** Richiede personale tecnico formato sull'uso dei template validati per evitare configurazioni manuali errate.

## 2. PROFILO FORTINET (Premium IT-Driven)
*Ideale per clienti con reparti IT strutturati che richiedono visibilità centralizzata.*

* **Hardware di riferimento:** FortiGate Rugged + FortiSwitch Rugged.
* **Vantaggi:**
    * **Ispezione Profonda:** Deep Packet Inspection (DPI) sui protocolli industriali (con licenza OT Security).
    * **Ecosistema:** Integrazione nativa FortiLink per la gestione degli switch dal firewall.
* **Criticità:** * **Costi Ricorrenti:** TCO elevato dovuto all'obbligatorietà del FortiCare per la conformità normativa.
    * **Complessità:** Gestione amministrativa delle scadenze dei contratti su ogni singolo nodo.

## 3. PROFILO SIEMENS (Premium OT-Driven)
*Ideale per capitolati "Full Siemens" o ambienti ad alta criticità di automazione.*

* **Hardware di riferimento:** Scalance SC-600 + Scalance XC.
* **Vantaggi:**
    * **Integrazione TIA Portal:** Configurazione e diagnostica integrate nell'ambiente di sviluppo dell'automazione.
    * **Certificazioni:** Hardware nato e certificato specificamente per ambienti industriali estremi.
* **Criticità:** Costo hardware elevato e flessibilità limitata per integrazioni con dashboard di terze parti.

---

## 4. PROFILI ESCLUSI DALLA SELEZIONE

### ❌ TELTONIKA (RUT Series)
Nonostante il costo aggressivo, la soluzione è stata scartata per:
* **Dipendenza Cloud:** Il management centralizzato (RMS) è vincolato ai server del vendor, creando una dipendenza esterna non accettabile per la Dashboard Tecnopack.
* **Limiti Firewall:** Motore firewall rule-based che non supporta le logiche dinamiche di Captive Portal e RBAC richieste per il livello SL-2.
* **Inadeguatezza IaC:** Mancanza di un sistema di provisioning "Infrastructure as Code" robusto per il deployment seriale.

### ❌ UBIQUITI (EdgeRouter/UniFi)
* **Motivazione:** Molti prodotti della linea EdgeRouter sono vicini all'End-of-Life (EOL). La linea UniFi è orientata al mondo Office/Retail e manca di caratteristiche industriali (montaggio DIN, alimentazione 24V DC nativa stabile).

---
*Dipartimento OT Security - Tecno Pack S.p.A.*