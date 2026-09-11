# Carte tarocchi: stesso disegno, stile incisione

**Data:** 2026-09-11  
**Prodotto:** app Tarocchi (`tarot.html`)  
**Sostituisce:** `docs/superpowers/specs/2026-09-11-carte-incisione-design.md` (quel tentativo ha ridisegnato le scene; questa spec lo vieta).

**Riferimento stile:** immagine incisione/woodcut inviata da Greta (inchiostro nero su carta crema, puntini e tratteggi, look da stampa antica). Non copiare il soggetto (figura inginocchiata, meme "my next piercing", titolo "THE DYING LIGHT", cherubini, arco gotico).

## Problema

Greta vuole lo stile di quell'incisione su tutte le carte. Il tentativo precedente ha fallito: per aggiungere un titolo o lo stile, le scene sono state ridisegnate (Il Matto non era piu Il Matto di prima). I filtri CSS da soli non bastano: le carte restano i disegni a colori, solo un po' scuriti.

## Obiettivi

- Tutte le **78 carte** (22 Arcani Maggiori + 56 minori) nello stile incisione.
- Ogni carta resta **esattamente** quella di oggi: stesse figure, oggetti, pose, numeri, inquadratura, banner del titolo.
- Cambia **solo la tecnica**: inchiostro nero, tratteggio e puntinatura, carta crema. I colori originali diventano inchiostro (fa parte dello stile).
- I titoli gia nel disegno restano identici: stesso testo, stessa posizione, stesso tipo di lettere (stampatello Rider-Waite, non gotico). Esempio: `THE FOOL.` resta `THE FOOL.`, non "IL MATTO" e non lettere gotiche.
- Se una carta oggi non ha titolo nel disegno, non se ne inventa uno.
- Nomi italiani sotto la carta restano come ora (etichetta UI, es. "Il Matto").
- Ventaglio, stese e mazzo usano le stesse immagini, stesso stile ovunque.
- Flussi, testi, tag Dritta/Invertita, ingrandimento del ventaglio, pagina: **non si toccano**.

## Non-obiettivi

- Ridisegnare o reinterpretare una scena.
- Aggiungere cornici, cherubini, archi, meme o titoli nuovi presi dal riferimento.
- Cambiare i titoli in gotico.
- Togliere o spostare il banner originale (`THE FOOL.`, `DEATH.`, ecc.).
- Fare prima poche carte campione: si fa tutto il mazzo.
- Applicare questo stile alle illustrazioni del resto del sito.

## Decisioni di prodotto

| Tema | Scelta |
|---|---|
| Contenuto | Identico a oggi, carta per carta |
| Stile | Incisione nero su crema, come il riferimento (solo tecnica) |
| Titolo nel disegno | Identico: testo, posizione, tipo di lettere (opzione A) |
| Titolo italiano | Resta l'etichetta sotto la carta |
| Copertura | Tutte le 78, subito, niente prova su 2-3 carte |
| Dritta / Invertita | Restano come ora; non si cambiano |
| Ventaglio | Ingrandimento e gesti invariati |
| Pagina | Ambiente esoterico gia fatto; cambiano solo le illustrazioni |

## Approccio scelto

**Partire da ciascuna illustrazione attuale e applicarle lo stile incisione, senza ridisegnare la scena.**

Alternative scartate:

- Solo filtri CSS piu forti: non produce l'incisione, le carte restano i disegni a colori scuriti.
- Ridisegnare ogni carta da zero "nello stesso soggetto": e il fallimento precedente (scene diverse, titoli persi o cambiati).

Regola di scarto: se una carta esce con pose, oggetti, taglio o titolo diversi dall'originale, non si usa. Si rifa quella carta finche la scena e la stessa.

## Esperienza attesa

1. Si gira una carta e si riconosce subito la scena di oggi, come se fosse stampata a incisione su carta crema.
2. In basso nel disegno si legge lo stesso titolo inglese di prima, con le stesse lettere.
3. Sotto, l'etichetta italiana resta quella di oggi.
4. Tutto il mazzo ha lo stesso linguaggio visivo.
5. Toccare, trascinare e interpretare funziona come ora.

## Casi limite

- Carte molto dettagliate o con tante figure: restano riconoscibili; non si semplifica togliendo personaggi.
- Carta invertita: stessa immagine capovolta; niente disegno diverso.
- Carte minori senza banner di titolo: restano senza banner.
- Carte senza immagine (solo simbolo): restano come ora.
- Mobile / ventaglio piccolo: la carta deve restare leggibile, non una macchia nera.

## Verifica di successo

- A occhio, una carta ricorda il riferimento per *tecnica* (nero, crema, tratteggio), non per *soggetto*.
- Affiancando originale e nuova, si vede la stessa scena e lo stesso titolo; e cambiata solo la tecnica.
- Nessun meme, nessun "THE DYING LIGHT", nessuna lettera gotica al posto dello stampatello.
- Nomi italiani, tag Dritta/Invertita e interazioni invariati.
- Tutte e 78 le carte, in ventaglio, stesa e mazzo.
