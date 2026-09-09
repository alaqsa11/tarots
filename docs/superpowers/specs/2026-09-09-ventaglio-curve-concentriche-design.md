# Ventaglio iniziale a due curve concentriche

**Data:** 2026-09-09  
**Stato:** bozza per review utente  
**Prodotto:** app Tarocchi (`tarot.html`) — sezione ventaglio iniziale

## Problema

Il ventaglio iniziale oggi dispone le carte su due archi, ma non concentrici (centri diversi, effetto “due piani”). Greta vuole un ventaglio a **due curve concentriche** (anello esterno + anello interno) e un ciclo di lettura piu chiaro: scelta → chiusura del ventaglio → lettura → ritorno al mazzo con riapertura random.

## Obiettivi

- Mostrare tutte le carte del mazzo su **due archi concentrici** (stesso centro, due raggi).
- Suddividere le carte circa a meta tra esterno e interno.
- Alla scelta di una carta: le altre si **richiudono verso il centro** e spariscono; la carta scelta resta al centro e si rivela.
- Dopo la lettura: un **click sulla carta rivelata** la riporta nel mazzo, il testo del significato si dissolve in parallelo, il mazzo viene rimescolato a caso e i due ventagli si riaprono come all’inizio.
- Mantenere il look e il linguaggio visivo esistenti del sito Tarocchi.

## Non-obiettivi

- Modificare i dati delle 78 carte o le posizioni degli spread.
- Cambiare il flusso delle letture a piu carte (Celtic Cross, ecc.).
- Introdurre login, salvataggio storico o backend.
- Redesign generale della pagina oltre al ventaglio iniziale.

## Decisioni di prodotto

| Tema | Scelta |
|---|---|
| Distribuzione carte | Circa meta esterna / meta interna |
| Alla rivelazione | Le curve si richiudono verso il centro e spariscono |
| Dopo la lettura | Click sulla carta rivelata **oppure** sul bottone «Scegli un'Altra Carta» → ritorno + rimescolamento + riapertura |
| Testo del risultato | Si dissolve in parallelo al ritorno della carta e alla riapertura |

## Approccio scelto

**A — Due archi sullo stesso centro (consigliato)**

- Un solo centro geometrico.
- Raggio esterno maggiore, raggio interno minore.
- Stessa ampiezza angolare dell’arco (con variazione random leggera come oggi, se utile all’atmosfera).
- Ordine e posizioni delle carte random a ogni apertura / reset.

### Alternative scartate

- **B — Arco esterno piu ampio, interno piu stretto:** piu scenografico, ma meno leggibile come “concentrico”.
- **C — Due file una sopra l’altra (stato attuale rifinito):** non sono curve concentriche.

## Architettura dell’esperienza

```
Stato: APERTO
  due anelli concentrici, carte coperte, tutte cliccabili
       |
       | click su una carta
       v
Stato: CHIUSURA
  carta scelta → centro + rivelazione
  altre carte → richiusura verso il centro → sparizione
  mostra risultato (nome, dritta/invertita, significato, keywords, consiglio)
       |
       | click sulla carta rivelata
       v
Stato: RIENTRO
  carta torna nel mazzo
  testo risultato si dissolve in parallelo
  mazzo rimescolato a caso
       |
       v
Stato: APERTO (di nuovo)
  due ventagli si riaprono con disposizione random
```

## Flussi principali

### 1. Apertura / refresh

1. Mazzo completo mescolato.
2. Circa meta carte sull’anello esterno, meta sull’interno.
3. Posizioni lungo ogni arco calcolate rispetto allo stesso centro.
4. Rotazione di ogni carta allineata alla tangente dell’arco (come un ventaglio reale).

### 2. Scelta di una carta

1. L’utente clicca una carta (esterna o interna, stesso comportamento).
2. La carta scelta si anima verso il centro e si rivela (dritta o invertita).
3. Tutte le altre carte animano una richiusura verso il centro e poi spariscono.
4. Compare il pannello risultato sotto il ventaglio.
5. Durante l’animazione i click ulteriori sono ignorati.

### 3. Fine lettura e ripartenza

1. L’utente ha letto il significato.
2. Avvia la ripartenza in uno di questi modi equivalenti:
   - click sulla carta rivelata al centro, oppure
   - click sul bottone esistente «Scegli un'Altra Carta»
3. La carta torna verso il mazzo; il testo del risultato si dissolve in parallelo.
4. Il mazzo viene rimescolato in modo randomico.
5. I due ventagli si riaprono come all’inizio (nuove posizioni).
6. Durante rientro/riapertura i click sono ignorati.

## Componenti coinvolti (ambito)

- `drawFan` / costruzione carte ventaglio: layout a due raggi concentrici.
- Animazione di rivelazione: aggiungere fase di richiusura delle carte non scelte.
- Nuovo gesto: click sulla carta rivelata per chiudere il ciclo (rientro + reshuffle + reopen).
- Pannello `fan-result`: dissolvenza in parallelo al rientro.
- Resize: se nessuna carta e rivelata, ricalcolo layout; se lettura aperta, resta ferma fino al click di chiusura.

## Casi limite

- Doppio click rapido: ignorato mentre un’animazione e in corso.
- Nessuna seconda carta selezionabile mentre una lettura e aperta (le altre carte non sono piu presenti).
- Resize durante lettura aperta: non interrompe la lettura; al prossimo ciclo aperto il layout si adatta.
- Numero dispari di carte: un anello puo avere una carta in piu (restare “circa meta”).

## Verifica di successo

- A occhio: si percepiscono due curve concentriche, non due file sovrapposte.
- Ciclo completo fluido: apri → scegli → leggi → click carta → riapertura random.
- Desktop e mobile: carte cliccabili, anelli leggibili, testo risultato utilizzabile.
- Dopo ogni ripartenza le posizioni delle carte sono diverse (rimescolamento effettivo).

## Fuori dallo scope di implementazione successiva

- Cambi al catalogo carte o alle immagini.
- Nuove animazioni decorative non legate a chiusura/riapertura.
- Redesign tipografico del pannello risultato oltre alla dissolvenza richiesta.
