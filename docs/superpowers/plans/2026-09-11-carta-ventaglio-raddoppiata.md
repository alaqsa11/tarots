# Carta Ventaglio Raddoppiata Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Quando si scopre una carta dal ventaglio, quella scelta appare al doppio della dimensione iniziale e resta cosi per tutta la lettura.

**Architecture:** Il raddoppio avviene con `scale(2)` sull'animazione esistente in `revealFanCard`. La misura di partenza resta quella CSS di `.tarot-card.fan-card` (76x118 desktop, 54x84 mobile): lo scale e relativo, quindi su telefono il doppio e automatico. Nessun cambio a larghezza/altezza CSS, al pannello testo, ne a `drawFan`.

**Tech Stack:** HTML/CSS/JS vanilla, verifica manuale in browser. Niente Node/npm.

## Global Constraints

- Raddoppio rispetto alla dimensione iniziale della carta nel ventaglio, non rispetto al `scale(1.1)` attuale.
- La carta resta a dimensione doppia per tutta la lettura, fino al rientro nel mazzo.
- Non cambiare la dimensione delle carte ancora chiuse.
- Non cambiare il pannello `fan-result`.
- Non modificare `TAROT_CARDS` ne gli spread.
- Niente Node.js/npm. Verifica = lettura del codice + test manuale in browser su `tarot.html`.
- Git: `& "C:\Program Files\Git\cmd\git.exe"`. Non `git config`. Messaggi commit in italiano senza accenti. Chiedere conferma a Greta prima di ogni commit.
- Testi UI restano in italiano.

## File Structure

| File | Ruolo |
|---|---|
| `tarot-app.js` | Unica modifica: `revealFanCard` usa `scale(2)` al posto di `scale(1.1)` |
| `tarot-style.css` | Non toccare dimensioni `.fan-card` (il raddoppio e relativo a quelle) |
| `tarot.html` | Non toccare |
| `tarot-cards.js` | Non toccare |

---

### Task 1: Raddoppiare la carta scelta in `revealFanCard`

**Files:**
- Modify: `tarot-app.js` (`revealFanCard`, riga con `scale(1.1)`)
- Test: manuale in browser su `tarot.html`

**Interfaces:**
- Consumes: `revealFanCard(card, cardData)` esistente; posizione centro gia calcolata su `offsetWidth` / `offsetHeight` (misura layout, non visiva)
- Produces: carta rivelata con `transform: rotateY(720deg) scale(2)`; resta a 2x finche `returnFanToDeck` non la rimpicciolisce e `drawFan` ricrea il ventaglio

- [ ] **Step 1: Confermare il punto esatto da cambiare**

In `tarot-app.js`, dentro `revealFanCard`, oggi la carta scelta fa:

```javascript
  card.style.left = (W / 2 - card.offsetWidth / 2) + 'px';
  card.style.top = (H / 2 - card.offsetHeight / 2) + 'px';
  card.style.transform = 'rotateY(720deg) scale(1.1)';
  card.style.zIndex = '999';
```

`offsetWidth` / `offsetHeight` restano quelli della carta chiusa (76x118 o 54x84). Con `transform-origin` al centro (default), `scale(2)` raddoppia visivamente la carta restando centrata. Non spostare `left`/`top`.

Non cambiare:

- il `scale(0.3)` delle altre carte in chiusura
- il `scale(0.5)` in `returnFanToDeck` (rientro nel mazzo)
- CSS `.tarot-card.fan-card` (`width`/`height`)

- [ ] **Step 2: Applicare `scale(2)`**

Sostituire solo la riga del transform:

```javascript
  card.style.transform = 'rotateY(720deg) scale(2)';
```

Dopo il timeout di 1200ms il codice aggiunge `.flipped` o `.reversed` sull'inner: non toccarlo. Lo style inline `scale(2)` deve restare sulla carta rivelata, cosi resta doppia per tutta la lettura.

- [ ] **Step 3: Verifica statica del codice**

Cercare in `tarot-app.js` che:

- `revealFanCard` abbia esattamente `scale(2)` e non `scale(1.1)`
- `returnFanToDeck` abbia ancora `scale(0.5)`
- `drawFan` / `placeOnRing` usino ancora `rotate(${rotDeg}deg)` senza scale
- nessun file CSS abbia cambiato `.tarot-card.fan-card`

- [ ] **Step 4: Verifica desktop in browser**

Aprire `tarot.html` (server locale se gia in uso, altrimenti file). Viewport desktop (~1280px o piu).

Flusso:

1. Ventaglio aperto: le carte restano piccole come prima.
2. Click su una carta (anello esterno).
3. Le altre si chiudono e spariscono come oggi.
4. La carta scelta va al centro, si scopre, e a occhio e il doppio di una carta del ventaglio (circa 152x236 px visivi, partendo da 76x118).
5. Il disegno e il nome non sono tagliati; la carta sta dentro l'area del ventaglio, centrata.
6. Il testo del significato sotto compare come oggi.
7. Click sulla carta rivelata: torna nel mazzo, il testo si dissolve, il ventaglio si riapre a dimensione iniziale.
8. Ripetere con una carta dell'anello interno e con il bottone «Scegli un'Altra Carta».

Se la carta risulta tagliata o decentrata, non aumentare `width`/`height` CSS: tenere `scale(2)` e controllare che `left`/`top` restino calcolati sulla misura layout (non visiva).

- [ ] **Step 5: Verifica telefono in browser**

Viewport ~390x844.

1. Ventaglio: carte piccole da CSS mobile (54x84).
2. Scelta: carta rivelata al doppio di quelle (circa 108x168), non al doppio desktop.
3. Non tagliata, centrata, testo risultato visibile.
4. Rientro e riapertura come sul desktop.

- [ ] **Step 6: Commit (solo dopo conferma di Greta)**

Non committare da soli. Se Greta conferma, messaggio:

```
Raddoppia la carta scelta dal ventaglio rispetto alla dimensione iniziale
```

File da includere: solo `tarot-app.js` (e questa spec/piano se non sono ancora nel repo e Greta lo vuole).
