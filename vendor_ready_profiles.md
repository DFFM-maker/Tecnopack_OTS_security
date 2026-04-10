# **Vendor-Ready Profiles: Strategia di Implementazione**

Mappatura dell'architettura agnostica Tecnopack sui tre stack tecnologici approvati.

## **1\. PROFILO "FORTINET READY" (IT-Compliance Focus)**

*Ideale per clienti con reparti IT strutturati che richiedono visibilità centralizzata.*

* **Hardware:** FortiGate Rugged (Firewall) \+ FortiSwitch Rugged.  
* **Controllo:** Gestione via **FortiLink**. Gli switch sono estensioni del firewall.  
* **Vantaggio:** Supporto nativo 802.1X e ispezione Deep Packet (DPI) sui protocolli industriali (se licenziato).

## **2\. PROFILO "SIEMENS READY" (OT-Integration Focus)**

*Scelta obbligata per capitolati "Full Siemens" o ambienti ad alta criticità Safety.*

* **Hardware:** Scalance SC-600 (Firewall) \+ Scalance XC (Switch).  
* **Controllo:** Integrazione nativa in **TIA Portal** e SINEC NMS.  
* **Vantaggio:** Massima resilienza alle vibrazioni e certificazioni per ambienti classificati. Ridondanza ad anello (MRP) nativa.

## **3\. PROFILO "MIKROTIK READY" (Lean & Serial Production)**

*Lo standard Tecnopack per macchine di serie. Massima sicurezza al minor costo hardware.*

* **Hardware:** RB5009/hEX S (Master) \+ CSS610 (Switch SwOS).  
* **Controllo:** Scripting **RouterOS v1.4** (IaC). Configurazione centralizzata sul Master.  
* **Vantaggio:** Zero costi di licenza. Captive Portal flessibile (RADIUS-ready). Gestione Port Lock granulare via SwOS.