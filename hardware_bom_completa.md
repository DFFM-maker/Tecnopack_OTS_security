# Hardware BOM: Matrice e Distinta Base (Standard Tecnopack)

**Documento Tecnico per Ufficio Acquisti e Cablaggio**
**Standard di Riferimento:** IEC 62443 SL-2

## 1. COMPONENTE FISSO (Standard Aziendale)
Il gateway **Secomea 1549** è lo standard per la teleassistenza. È presente in ogni configurazione. Il costo è escluso dai delta economici di confronto.

## 2. MATRICE HARDWARE PER TOPOLOGIA

### SCENARIO A: MACCHINA SINGOLA (Stand-Alone)
| Componente            | Opzione 1: FORTINET           | Opzione 2: SIEMENS | Opzione 3: MIKROTIK |
| :-------------------- | :---------------------------- | :----------------- | :------------------ |
| **Edge Firewall**     | FortiGate Rugged 40F          | Scalance SC622-2C  | **MikroTik hEX S**  |
| **Supp. FW Firewall** | **FortiCare 1Y (Mandatorio)** | Incluso            | **Gratuito**        |
| **Switch (8 Porte)**  | FSR-108F                      | Scalance XC208     | **CSS610-8G-2S+IN** |
| **Supp. FW Switch**   | **FortiCare 1Y (Mandatorio)** | Incluso            | **Gratuito**        |
| **Wi-Fi Maint.**      | FortiAP 222E                  | Scalance W774      | **mAP lite**        |

### SCENARIO B: LINEA COMPLESSA (Master + 3 Flowpack)
| Ruolo / Quadro          | Opzione 1: FORTINET  | Opzione 2: SIEMENS | Opzione 3: MIKROTIK     |
| :---------------------- | :------------------- | :----------------- | :---------------------- |
| **Firewall (Master)**   | FortiGate Rugged 70G | Scalance SC632-2C  | **RB5009UG+S+IN**       |
| **Supporto Master**     | **FortiCare 1Y**     | Incluso            | **Gratuito**            |
| **Switch (Master)**     | FSR-108F             | Scalance XC208     | **CSS610-8G-2S+IN**     |
| **Switch (Periferici)** | 3x FSR-108F          | 3x Scalance XC208  | **3x CSS610-8G-2S+IN**  |
| **Supporto Switch**     | **3x FortiCare 1Y**  | Incluso            | **Gratuito**            |
| **Interconnessione**    | Cavi Ethernet        | Cavi Ethernet      | **Cavi DAC SFP+ (10G)** |

## 3. NOTE TECNICHE DI CABLAGGIO E LICENZE
1. **Rinnovi e CRA:** Per l'opzione Fortinet, è obbligatorio il contratto FortiCare su ogni dispositivo per garantire aggiornamenti firmware (obbligo CRA).
2. **Backbone Linea:** Per MikroTik, l'interconnessione via **Cavi DAC SFP+** è obbligatoria per garantire 10Gbps ed evitare saturazione delle porte RJ45.
3. **Alimentazione:** Tutti i dispositivi MikroTik supportano 24V DC tramite adattatori plug-to-screw.