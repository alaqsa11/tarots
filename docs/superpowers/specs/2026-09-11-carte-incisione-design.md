# Carte tarocchi in stile incisione

> **SUPERSEDED.** Questa spec ha portato a scene ridisegnate. Usare `docs/superpowers/specs/2026-09-11-incisione-stesso-disegno-design.md`.

**Data:** 2026-09-11  
**Prodotto:** app Tarocchi (`tarot.html`)  
**Riferimento stile:** incisione/woodcut inviata da Greta (inchiostro nero su carta crema, puntini e tratteggi, look da stampa antica). Non copiare il soggetto di quell'immagine (figura, testo meme, titolo "THE DYING LIGHT").

## Problema

Il trattamento CSS attuale (scala di grigi, contrasto, seppia) non basta: le carte restano i disegni classici a colori, solo un po' scuriti. Greta vuole che **i disegni attuali** siano **rifatti** nello stile dell'incisione di riferimento.

## Obiettivi

- Ogni carta tiene la **stessa scena** di oggi: stesse figure, oggetti, pose e composizione (Il Matto resta Il Matto, La Morte resta La Morte, ecc.).
- Ogni carta e ridisegnata come **stampa a incisione**: inchiostro nero, tratteggio e puntinatura, carta crema, contrasto da lastra da zine.
- Il mazzo intero (78 carte: 22 Arcani Maggiori + 56 minori) ha lo stesso linguaggio visivo.
- Ventaglio, stese e mazzo (le carte in basso) usano queste nuove immagini: stesso stile ovunque.
- I nomi italiani restano sotto la carta come prima (stesso aspetto: "Il Matto", non maiuscolo gotico).
- Il titolo gia presente nel disegno (es. THE FOOL, THE HERMIT) va lasciato: fa parte della carta.
- Niente testo extra inventato (niente meme, niente titoli nuovi).
- Sulla carta non compaiono le scritte "Dritta" e "Invertita": restano solo nel testo di interpretazione.
- La carta scelta dal ventaglio continua a ingrandirsi come ora: quel comportamento non si cambia.

## Non-obiettivi

- Inventare scene nuove o cambiare il significato delle carte.
- Copiare il soggetto dell'immagine di riferimento.
- Cambiare l'ingrandimento della carta cliccata nel ventaglio.
- Cambiare flussi (ventaglio, rivelazione, stese, mazzo), a parte togliere "Dritta"/"Invertita" dal fronte carta.
- Applicare questo stile alle illustrazioni del resto del sito (solo le carte).

## Decisioni di prodotto

| Tema | Scelta |
|---|---|
| Contenuto | Disegni attuali, non scene nuove |
| Stile | Incisione nero su crema, come il riferimento |
| Testo nel disegno | Si tiene il titolo originale della carta (inglese, come ora); nome italiano resta l'etichetta in Cinzel |
| Copertura | Tutte le 78 carte, anche quelle in basso nel mazzo |
| Dritta / Invertita | Solo nel testo di interpretazione, mai sopra la carta |
| Ventaglio | La carta cliccata resta grande come ora; non si tocca questo gesto |
| Pagina | Resta l'ambiente esoterico gia fatto; cambiano le immagini delle carte |

## Approccio scelto

**Rifare le illustrazioni delle carte nello stile incisione, tenendo le scene attuali.**

Alternative scartate:

- Solo filtri CSS piu forti: non produce disegni nuovi, e Greta ha chiesto di rifarli.
- Solo gli Arcani Maggiori: il mazzo resterebbe a due stili diversi.

Prima di coprire tutto il mazzo, si fa una prova visiva su poche carte campione (stessa scena, nuovo stile). Se lo stile e quello giusto, si completa il resto.

## Esperienza attesa

1. Si gira una carta e si vede la scena classica, ma come se fosse stampata a incisione su carta crema.
2. Tutte le carte (ventaglio, stesa, elenco mazzo) hanno lo stesso look.
3. Si vede il titolo originale nel disegno e, sotto, il nome italiano in Cinzel.
4. Toccare, trascinare e interpretare funziona come ora.

## Casi limite

- Carte con molte figure o dettagli: la scena deve restare riconoscibile, anche se lo stile e piu grezzo.
- Carta invertita: stessa immagine capovolta; niente disegno diverso.
- Carte senza immagine (solo simbolo): restano come ora, con cornice e tipografia gia presenti.
- Mobile: le carte piccole del ventaglio devono restare leggibili (non un macchia nera).

## Verifica di successo

- A occhio, una carta ricordi l'incisione di riferimento per *tecnica* (nero, crema, tratteggio), non per *soggetto*.
- Una persona che conosce i tarocchi riconosce ancora ogni carta dalla scena.
- Nessuna scritta meme; i titoli originali nel disegno restano.
- Nomi italiani e interazioni invariati.
