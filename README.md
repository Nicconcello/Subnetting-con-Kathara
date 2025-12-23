# Progetto Kathara: Subnetting VLSM e NAT

## 📌 Introduzione
Questo repository contiene la soluzione all'**Homework** del corso di *Fondamenti di Comunicazioni ed Internet*.
Il progetto implementa una simulazione di rete aziendale distribuita su base regionale, affrontando due tematiche principali:
1.  **Pianificazione degli indirizzi IP (Subnetting)** ottimizzata.
2.  **Configurazione del NAT (Network Address Translation)** per l'accesso a Internet.

📄 **Documentazione:** Per i dettagli teorici e i calcoli completi, consultare il report: [Report Kathara.pdf](./docs/Report_Kathara.pdf).

## 📐 1. Subnetting e VLSM
L'obiettivo era assegnare sottoreti contigue alle sedi regionali minimizzando lo spreco di indirizzi IP. È stato utilizzato il metodo **VLSM (Variable Length Subnet Mask)**.

### Metodologia
Le richieste delle sedi sono state riordinate in modo decrescente (dalla più grande alla più piccola) per allocare prima i blocchi maggiori:
1.  **Puglia** (8390 host richiesti) - *Allocata per prima*
2.  *[Altre regioni]* - *Allocate a seguire negli spazi contigui*

La formula utilizzata per il calcolo della maschera (CIDR) è:
$Host_{richiesti} + 2 \le 2^n$
Dove $n$ determina i bit per gli host e il CIDR è $/(32-n)$.

## 🌐 2. NAT e Masquerading
La rete aziendale utilizza uno spazio di indirizzamento privato (`10.42.0.0/XX`), non instradabile su Internet.
Per permettere agli host interni (es. sede **Liguria**, IP `10.42.144.2`) di comunicare con l'esterno (es. `8.8.8.8`), è stato configurato il **Source NAT** sul router di bordo **R1**.

### Configurazione su R1
È stata utilizzata una regola di `iptables` per il **Masquerading**:
* **In Uscita (POSTROUTING):** R1 sostituisce l'IP sorgente del pacchetto (`10.42.144.2`) con il proprio IP pubblico.
* **In Entrata:** R1 riceve la risposta e, consultando la tabella NAT, reindirizza il pacchetto all'host interno corretto.

## 🚀 Istruzioni per l'Avvio
  Avviare il laboratorio:
    ```
    kathara lstart
    ```

## 🧪 Test e Verifiche
Come documentato nel report, è possibile verificare il funzionamento della rete con i seguenti test:

### Test di Connettività Internet (NAT)
Dalla postazione della **Liguria**, provare a pingare un indirizzo esterno (Google DNS):
```bash
# Terminale Liguria
ping 8.8.8.8
