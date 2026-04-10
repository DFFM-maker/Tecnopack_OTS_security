# **Hardware BOM: Matrice e Distinta Base (Standard Tecnopack)**

**Documento Tecnico per Ufficio Acquisti e Cablaggio**

**Standard di Riferimento:** IEC 62443 SL-2

## **1\. COMPONENTE FISSO (Standard Aziendale)**

Il gateway **Secomea 1549** è lo standard per la teleassistenza. È presente in ogni configurazione. Il suo costo è considerato un "costo fisso" già acquisito e non influenza la scelta del vendor di rete.

## **2\. MATRICE HARDWARE PER TOPOLOGIA**

### **SCENARIO A: MACCHINA SINGOLA (Stand-Alone)**

| Componente           | Opzione 1: FORTINET  | Opzione 2: SIEMENS | Opzione 3: MIKROTIK |
| :------------------- | :------------------- | :----------------- | :------------------ |
| **Edge Firewall**    | FortiGate Rugged 40F | Scalance SC622-2C  | **MikroTik hEX S**  |
| **Switch (8 Porte)** | FSR-108F             | Scalance XC208     | **CSS610-8G-2S+IN** |
| **Wi-Fi Maint.**     | FortiAP 222E         | Scalance W774      | **mAP lite**        |
| **Supporto FW**      | FortiCare (1 anno)   | Incluso            | **Incluso (Free)**  |

### **SCENARIO B: LINEA COMPLESSA (Master \+ 3 Flowpack)**

| Ruolo / Quadro           | Opzione 1: FORTINET  | Opzione 2: SIEMENS | Opzione 3: MIKROTIK     |
| :----------------------- | :------------------- | :----------------- | :---------------------- |
| **Firewall (Master)**    | FortiGate Rugged 70G | Scalance SC632-2C  | **RB5009UG+S+IN**       |
| **Switch (Master)**      | FSR-108F             | Scalance XC208     | **CSS610-8G-2S+IN**     |
| **Switch (Flowpack x3)** | 3x FSR-108F          | 3x Scalance XC208  | **3x CSS610-8G-2S+IN**  |
| **Interconnessione**     | Cavi Ethernet        | Cavi Ethernet      | **Cavi DAC SFP+ (10G)** |

## **3\. NOTE TECNICHE DI CABLAGGIO**

1. **Fissaggio:** Ordinare le **clip DIN universali (35mm)** per tutti i moduli MikroTik.  
2. **Alimentazione:** Tutti i dispositivi MikroTik devono essere alimentati a **24V DC** di quadro tramite adattatori morsettiera-jack.  
3. **Backbone Linea:** Per l'opzione MikroTik, collegare gli switch tramite le porte SFP+ usando i **cavi DAC**. Questo libera le porte RJ45 per i PLC e garantisce velocità a 10Gbps.  
4. **Port Lock:** Verificare che il MAC Locking sia attivo su tutte le porte verso i PLC (SR 7.8).