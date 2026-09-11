# Carta scelta dal ventaglio a dimensione raddoppiata

**Data:** 2026-09-11  
**Stato:** bozza per review utente  
**Prodotto:** app Tarocchi (`tarot.html`) — sezione ventaglio iniziale

## Problema

Quando si sceglie una carta dal ventaglio, quella carta va al centro e si scopre, ma resta quasi della stessa dimensione delle carte chiuse (oggi e solo un po' piu grande). Greta vuole vederla **al doppio della dimensione iniziale**, cosi il disegno e piu leggibile durante la lettura.

## Obiettivi

- Alla scoperta, la carta scelta deve apparire **esattamente al doppio** della dimensione che aveva nel ventaglio chiuso.
- Il raddoppio vale sia su desktop sia su telefono (ognuno rispetto alla propria dimensione iniziale).
- La carta resta a dimensione doppia per tutta la lettura, fino al rientro nel mazzo.
- Il resto del ciclo non cambia: richiusura delle altre carte, testo del significato, click per ripartire.

## Non-obiettivi

- Cambiare la dimensione delle carte ancora chiuse nel ventaglio.
- Cambiare il pannello di testo sotto la carta.
- Modificare gli spread a piu carte o il catalogo.
- Redesign generale della pagina.

## Decisioni di prodotto

| Tema | Scelta |
|---|---|
| Riferimento del raddoppio | Dimensione iniziale della carta nel ventaglio, non la misura gia leggermente ingrandita di oggi |
| Durata | Resta doppia per tutta la lettura |
| Altre carte | Stesso comportamento di oggi (si chiudono e spariscono) |
| Mobile | Stesso rapporto: doppio rispetto alle carte piccole del ventaglio sul telefono |

## Approccio scelto

**A — Ingrandire la carta scelta durante l'animazione verso il centro (consigliato)**

La carta vola al centro e, nello stesso movimento, diventa il doppio. Quando si gira, e gia grande. Al rientro nel mazzo, torna a restringersi.

Alternative scartate:

- **B — Cambiare larghezza e altezza della carta dopo la scelta:** stesso risultato visivo, ma piu facile sballare il centraggio.
- **C — Mostrare una seconda carta grande copiata:** doppia rappresentazione, rischio di disallineamento col resto del ciclo.

## Esperienza

```
Stato: APERTO
  ventaglio a due anelli, carte alla dimensione iniziale
       |
       | click su una carta
       v
Stato: SCOPERTA
  altre carte si chiudono e spariscono (come oggi)
  carta scelta al centro, scoperta, dimensione = 2x iniziale
  testo del significato sotto (come oggi)
       |
       | click sulla carta rivelata o su «Scegli un'Altra Carta»
       v
Stato: RIENTRO
  la carta torna nel mazzo (si rimpicciolisce)
  testo si dissolve
  ventaglio si riapre con carte alla dimensione iniziale
```

## Casi limite

- Durante l'animazione di scoperta o rientro i click extra restano ignorati, come oggi.
- Su telefono il raddoppio resta relativo alle carte piccole del ventaglio mobile: non si usa la misura desktop.
- La carta deve restare dentro l'area del ventaglio, centrata, senza tagliare il disegno.

## Verifica di successo

- A occhio: la carta scoperta e chiaramente il doppio di una carta ancora chiusa (se si confronta a memoria con il ventaglio appena visto).
- Il disegno e il nome sulla carta restano nitidi, non tagliati.
- Il ciclo completo resta uguale: scegli → leggi → rimetti → nuovo ventaglio.
- Desktop e telefono: stesso comportamento, ciascuno col proprio doppio.
