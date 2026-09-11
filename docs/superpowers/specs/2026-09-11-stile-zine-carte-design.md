# Stile zine esoterica: carte fedeli al riferimento, sito affine ma distinto

**Data:** 2026-09-11  
**Stato:** bozza per review utente  
**Prodotto:** app Tarocchi (`tarot.html`) — look carte + pagina  
**Riferimento carte:** [2027 Calendar + Zine: Mythological Caprids](https://www.behance.net/gallery/249274447/2027-Calendar-Zine-Mythological-Caprids) (Tiffany Wong)

## Problema

Greta vuole che le **carte** riprendano **in modo fedele** lo stile del progetto Behance (zine esoterica: inchiostro nero su carta crema, look da incisione/stampa, tipografia gotica/serif). Il **resto del sito** deve basarsi sullo stesso linguaggio, ma **non essere uguale** alle carte: altrimenti non si nota la differenza e le carte non emergono come oggetto speciale.

## Obiettivi

- Carte (fronte e retro, in ventaglio / stese / mazzo): stile il piu vicino possibile al riferimento (monocromo stampa, cornici, texture carta, tipografia gotica/serif sulle etichette).
- Immagini esistenti trattate per sembrare incisioni/stampe nello stesso linguaggio (non nuove illustrazioni in questa fase).
- Pagina/UI: atmosfera esoterica affine (crema/nero + pochi accenti viola/oro), font piu gotici sui titoli, texture e gerarchia da grimorio/editoriale.
- La differenza carta vs pagina deve restare chiara a colpo d'occhio: le carte = pezzo stampato; la pagina = ambiente intorno.

## Non-obiettivi

- Copiare asset, illustrazioni o layout proprietari del progetto Behance.
- Ridisegnare da zero le 78 immagini (solo trattamento stilistico in questa fase).
- Rendere pagina e carte visivamente indistinguibili.
- Cambiare flussi funzionali (ventaglio, rivelazione, stese, mazzo).

## Decisioni di prodotto

| Tema | Scelta |
|---|---|
| Palette sito | Ibrida: base crema/nero esoterica + pochi accenti viola/oro |
| Stile carte | Fedelta alta al riferimento Behance (stampa zine) |
| Stile pagina | Stessa famiglia, ma piu sobria / diversa abbastanza da far risaltare le carte |
| Immagini carte | Stesse asset, rese in chiave incisione/stampa |
| Tipografia | Titoli piu gotici/serif; UI secondaria leggibile |

## Approccio scelto

**Carte = oggetto stampato fedele; pagina = ambiente affine ma distinto**

- Layer CSS dedicato alle carte (carta crema, nero, cornici doppie, filtri immagine forti, etichette tipografiche).
- Tema pagina separato: stessi ingredienti (texture, serif gotiche, contrasti) ma densita e contrasto inferiori, con tocchi viola/oro per non perdere il brand Tarocchi.
- Nessuna nuova logica di gioco.

### Alternative scartate

- Monocromo totale su tutto il sito (carte e pagina uguali): annulla la gerarchia.
- Solo viola/oro con cornici leggere: troppo lontano dal riferimento per le carte.
- Nuove illustrazioni woodcut: fuori scope ora.

## Esperienza attesa

1. Aprendo la pagina si entra in un ambiente esoterico (crema/nero, titoli gotici, texture).
2. Le carte sembrano pezzi di zine/stampa nera su crema, chiaramente diverse dallo sfondo.
3. Ventaglio, stese e mazzo condividono lo stesso trattamento carte.
4. Interazioni esistenti restano invariate.

## Componenti coinvolti

- `tarot-style.css`: tema pagina; stile `.card-face` / `.card-back` / `.card-front` / `.card-image` / nome carta; font (CDN gotico/serif).
- `tarot.html`: link font se necessario.
- `tarot-app.js` / `tarot-cards.js`: non toccare salvo classi minime.

## Casi limite

- Carte solo-simbolo (senza immagine): cornice e tipografia comunque fedeli.
- Mobile: nome e cornici leggibili sulle carte piccole del ventaglio.
- Badge dritta/invertita: leggibili sul nuovo fronte crema/nero.
- Accenti viola/oro: solo su UI pagina (nav, CTA, titoli secondari), non “rompere” il monocromo delle carte.

## Verifica di successo

- Carte: a occhio ricordano fortemente il riferimento Behance (stampa zine), senza copiare i disegni.
- Pagina: stessa atmosfera, ma si vede subito che non e “la stessa superficie” delle carte.
- Brand Tarocchi ancora presente tramite pochi accenti viola/oro.
- Nessuna regressione funzionale su ventaglio / stese / mazzo.

## Iterazione successiva (fuori scope immediato)

- Se il trattamento immagini non basta: valutare asset dedicati in stile woodcut.
- Ritocchi di intensita filtri / goticita font dopo prova visiva.
