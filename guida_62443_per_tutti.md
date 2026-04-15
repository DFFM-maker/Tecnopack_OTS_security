# IEC 62443 — Guida per Tutti

**Perche' la cybersecurity industriale ci riguarda tutti**

---

## 1. Il Problema: Perche' Serve una Norma?

Le fabbriche moderne usano computer per controllare le macchine (PLC, HMI, robot). Questi computer sono connessi in rete. Se qualcuno entra in quella rete senza autorizzazione, puo':

- Fermare la produzione
- Cambiare i parametri di una macchina (rischio per la sicurezza delle persone)
- Rubare dati di processo o ricette di prodotto
- Usare la macchina come "porta di ingresso" per attaccare il resto dell'azienda

La norma **IEC 62443** e' il riferimento mondiale per proteggere questi sistemi. E' come la norma CE, ma per la cybersecurity.

---

## 2. IEC 62443 in 30 Secondi

| Concetto | Spiegazione Semplice |
|----------|---------------------|
| **IEC 62443** | La norma internazionale che dice COME proteggere i sistemi industriali dal cyberattacco |
| **SL (Security Level)** | Il "livello di scudo". SL-1 = base, SL-2 = intermedio, SL-3 = alto, SL-4 = massimo |
| **Zone** | Aree della rete dove i dispositivi hanno lo stesso livello di fiducia (es. "zona PLC" vs "zona esterna") |
| **Conduit** | Il "corridoio controllato" che collega due zone. Solo il traffico autorizzato puo' passare |
| **FR (Fundamental Requirement)** | I 7 pilastri della sicurezza. Se manca un pilastro, la struttura crolla |
| **SR (System Requirement)** | Il dettaglio tecnico di ogni pilastro. Come si implementa nella pratica |
| **CSMS** | Il sistema di gestione: chi fa cosa, quando, e come si verifica che funzioni |

---

## 3. I 7 Pilastri (FR) — Tradotti in Italiano Semplice

Ogni pilastro corrisponde a una domanda. Se la risposta e' SI per tutti, la macchina e' conforme SL-2.

| # | Pilastro | Domanda | Cosa fa Tecnopack |
|---|----------|---------|-------------------|
| **FR 1** | Identificazione | "So chi sei?" | Login con password (Captive Portal locale, MFA per accesso remoto) |
| **FR 2** | Controllo accesso | "Hai il permesso?" | Tecnico entra solo dalla VLAN manutenzione, con timeout automatico |
| **FR 3** | Integrita' dei dati | "Qualcuno ha modificato il programma del PLC?" | Protezione sorgenti PLC, audit trail attivo |
| **FR 4** | Riservatezza | "Qualcuno puo' leggere i dati?" | Crittografia sulle connessioni esterne (Secomea VPN) |
| **FR 5** | Segmentazione | "Se qualcuno entra da una parte, puo' arrivare ovunque?" | Firewall deny-by-default tra le zone. No, non puede |
| **FR 6** | Monitoraggio | "Me ne accorgo se qualcosa non va?" | Log centralizzati, allarmi su accessi anomali |
| **FR 7** | Disponibilita' | "Se qualcosa si rompe, posso ripristinare?" | Backup automatici, Port Lock fisico, inventario asset |

---

## 4. SL-2: Cosa Significa per Tecnopack

Il livello SL-2 e' il target scelto da Tecnopack. Corrisponde a:

> Protezione contro attacchi con **risorse moderate**, usando **metodi generici** (non sofisticati).

In pratica: non ci difendiamo da attacchi di stato-nazione, ma da malware comune, dipendenti scontenti, attaccanti opportunisti, e errori umani. E' il livello giusto per macchine industriali connessa alla rete del cliente.

---

## 5. Come e' Fatta la Rete Tecnopack (Semplificato)

```
                    Internet
                       |
                  Gateway Secomea
                  (teleassistenza)
                       |
                  +---------+
                  | FIREWALL | <--- Il guardiano
                  +---------+
                   /         \
                  /           \
         VLAN 200          VLAN 300
      (Supervisione)     (Manutenzione)
      PLC, HMI, I/O    Tecnici Wi-Fi
         172.16.224.x     10.0.30.x
```

**Regole del guardiano (Firewall):**

1. Tutto e' BLOCCATO di default
2. Il traffico e' consentito SOLO se esplicitamente dichiarato
3. Il canale MES -> PLC (OPC-UA) e' l'unico autorizzato dalla zona esterna
4. I tecnici possono accedere alla zona PLC solo dopo autenticazione Captive Portal

---

## 6. Cosa Cambia per Ogni Ruolo

### Per il Tecnico di Cablaggio
- Ogni dispositivo ha il suo posto nella VLAN giusta
- Le porte dello switch sono bloccate: se colleghi un dispositivo non registrato, non funziona (Port Lock)
- Il Wi-Fi non e' sempre acceso: si attiva con la chiave sul quadro

### Per il Software Engineer
- Non puoi collegarti al PLC direttamente dalla rete del cliente
- Devi usare il Captive Portal (VLAN 300) con le tue credenziali
- Dopo 30 minuti di inattivita', la sessione scade automaticamente
- Dopo 4 ore, devi rifare il login

### Per il Project Manager
- La dichiarazione di conformita' IEC 62443 va nel fascicolo tecnico
- Il CRA (Cyber Resilience Act europeo) richiede aggiornamenti firmware: MikroTik li da' gratis, Fortinet serve il contratto
- Il risparmio TCO a 5 anni con MikroTik e' oltre 8.400 EUR rispetto a Fortinet (linea complessa)

### Per il Responsabile Qualita'
- Ogni macchina deve avere la documentazione di conformita' compilata e firmata
- La formazione annuale sulla cybersecurity e' obbligatoria (CSMS)
- Le vulnerabilita' vanno valutate entro 15 giorni e patchate entro 30

---

## 7. Glossario Rapido

| Termine | Significato |
|---------|------------|
| **PLC** | Programmable Logic Controller — il "cervello" della macchina |
| **HMI** | Human Machine Interface — lo schermo dove l'operatore vede e comanda |
| **VLAN** | Reti virtuali separate sulla stessa infrastruttura fisica |
| **NAT** | Nasconde gli indirizzi interni (Topology Hiding) |
| **Captive Portal** | La pagina di login che vedi quando ti connetti al Wi-Fi in hotel |
| **RBAC** | Role-Based Access Control — ogni ruolo vede solo cio' che gli spetta |
| **Port Lock** | Le porte dello switch accettano solo i dispositivi registrati per MAC address |
| **IaC** | Infrastructure as Code — la configurazione e' uno script, non click manuali |
| **Conduit** | Il canale autorizzato tra due zone (es. solo OPC-UA sulla porta 4840) |
| **Zone** | Un gruppo di dispositivi con lo stesso livello di protezione |
| **CSMS** | Cyber Security Management System — le regole organizzative (chi fa cosa) |
| **TCO** | Total Cost of Ownership — quanto costa davvero in 5 anni, non solo l'acquisto |
| **CRA** | Cyber Resilience Act — la nuova legge europea sulla cybersecurity dei prodotti |
| **CVE** | Vulnerabilita' nota e catalogata (Common Vulnerabilities and Exposures) |
| **FAT** | Factory Acceptance Test — il collaudo in fabbrica prima della consegna |
| **SAT** | Site Acceptance Test — il collaudo sul sito del cliente |
| **Deny-by-default** | Tutto e' bloccato salvo eccezione esplicita — il principio fondamentale |

---

## 8. I Diagrammi di Rete

Il progetto include diagrammi draw.io per entrambi gli standard hardware:

| Diagramma | Contenuto |
|-----------|-----------|
| `Fortinet Network Layout.drawio` | 3 pagine: Macchina Singola, Magnemotion, Linea (FortiLink) |
| `MikroTik_Network_Layout.drawio` | 2 pagine: Macchina Singola, Linea (Hub & Spoke / Trunk 802.1Q) |

### Legenda Colori (nei diagrammi)

| Colore | Significato |
|--------|------------|
| Rosso | Firewall / dispositivo di sicurezza |
| Arancione | Dispositivi OT (PLC, HMI, switch supervisione) |
| Blu scuro | Gateway di teleassistenza (Secomea) |
| Viola | Rete I/O (ingressi/uscite campo) |
| Verde chiaro | Rete IT del cliente |
| Viola scuro | Access Point Wi-Fi manutenzione |
| Azzurro | Internet |

---

## 9. Checklist: Cosa Serve per la Conformita'

Prima di dichiarare una macchina conforme SL-2:

- [ ] Firewall configurato con regole deny-by-default
- [ ] VLAN 200 (supervisione) e VLAN 300 (manutenzione) operative
- [ ] Captive Portal attivo sulla VLAN 300 con timeout 30 min / 4 ore
- [ ] Port Lock attivo sugli switch periferici
- [ ] Wi-Fi manutenzione attivabile solo con selettore a chiave
- [ ] Log centralizzati verso Dashboard Syslog
- [ ] Backup configurazione "Golden Image" salvato
- [ ] Credenziali locali cambiate dai default di fabbrica
- [ ] Certificato API-SSL generato e installato
- [ ] Dichiarazione di conformita' compilata e firmata
- [ ] Tecnici formati sull'uso del Captive Portal e del Blueprint

---

*Documento a supporto della presentazione del progetto OT Security — Dipartimento OT Security, Tecno Pack S.p.A.*