# Ventaglio Curve Concentriche Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Trasformare il ventaglio iniziale in due archi concentrici e completare il ciclo scelta → chiusura → lettura → rientro con rimescolamento e riapertura.

**Architecture:** Tutta la logica resta in `tarot-app.js` (vanilla globale). `drawFan` calcola posizioni su due raggi con lo stesso centro; `revealFanCard` anima la richiusura delle altre carte; una nuova `returnFanCard` (invocata da click sulla carta rivelata e da `resetFan`) dissolve il risultato, rimescola e richiama `drawFan`. CSS minimo in `tarot-style.css` per la dissolvenza del pannello risultato. Nessun cambio a `tarot-cards.js`.

**Tech Stack:** HTML/CSS/JS vanilla, sito statico, verifica manuale nel browser (niente Node/npm/test runner).

## Global Constraints

- Comunicare e commentare in italiano; niente accenti nei dati/codice dove gia evitato (`e` al posto di `e`).
- Non modificare i dati delle 78 carte in `tarot-cards.js`.
- Non introdurre Node.js, npm, framework o build step.
- Verifica = lettura del codice + test manuale in browser.
- Prima di ogni commit chiedere conferma all'utente; messaggi di commit in italiano senza accenti.
- Palette e stile esistenti del sito Tarocchi (non redesign generale).

## File Structure

| File | Ruolo |
|---|---|
| `tarot-app.js` | Layout concentrico, stati animazione, reveal/chiusura, rientro+reshuffle |
| `tarot-style.css` | Transizione dissolvenza `.fan-result`, eventuali stati `.fan-closing` / `.fan-returning` |
| `tarot.html` | Nessun cambio strutturale obbligatorio (bottone gia chiama `resetFan()`) |
| `tarot-cards.js` | Non toccare |

---

### Task 1: Layout a due archi concentrici in `drawFan`

**Files:**
- Modify: `tarot-app.js` (`drawFan`, circa linee 409–504)
- Test: manuale in browser su `tarot.html`

**Interfaces:**
- Consumes: `buildFanCard(cardData)`, `shuffleDeck`, `getAllCards()`, `#fan-container`
- Produces: carte `.fan-card` posizionate su anello esterno/interno con stesso centro; dataset `origLeft`, `origTop`, `theta`, `zIndex`, `ring` (`outer`|`inner`)

- [ ] **Step 1: Sostituire il calcolo a due centri con un centro condiviso**

In `drawFan`, dopo aver letto `W`/`H` del container, usare un solo centro e due raggi. Rimuovere `cyBottom` e il secondo centro della fila inferiore.

Sostituire il blocco geometrico iniziale (da `const cardW` fino a prima del recupero carte) con:

```javascript
  const cardW = 76;
  const cardH = 118;
  const cx = W / 2;
  // Centro del ventaglio: sotto il punto piu alto delle carte, condiviso da entrambi gli anelli
  const cy = H * 0.78;
  const arcDeg = 120 + Math.random() * 50;
  const arcRad = arcDeg * Math.PI / 180;
  // Raggio esterno: l'arco deve stare nel container (margine dalla cima)
  const R_outer = Math.min(cy - cardH * 0.55, W * 0.42);
  const R_inner = R_outer * 0.68;
```

Helper locale (nello stesso `drawFan`, prima dei due forEach):

```javascript
  function placeOnRing(card, i, n, R, zBase) {
    const t = n > 1 ? i / (n - 1) : 0.5;
    const theta = -arcRad / 2 + arcRad * t;
    const x = cx + R * Math.sin(theta);
    const y = cy - R * Math.cos(theta);
    const rotDeg = theta * 180 / Math.PI;

    card.style.left = (x - cardW / 2) + 'px';
    card.style.top = (y - cardH / 2) + 'px';
    card.style.transform = `rotate(${rotDeg}deg)`;
    card.style.zIndex = String(zBase + i);
    card.style.opacity = '1';
    card.style.pointerEvents = '';

    card.dataset.origLeft = card.style.left;
    card.dataset.origTop = card.style.top;
    card.dataset.theta = String(rotDeg);
    card.dataset.zIndex = card.style.zIndex;
  }
```

- [ ] **Step 2: Posizionare meta carte sull'esterno e meta sull'interno**

Sostituire i due forEach attuali con:

```javascript
  const N = cards.length;
  const outerN = Math.ceil(N / 2);
  const innerN = Math.floor(N / 2);

  cards.slice(0, outerN).forEach((cardData, i) => {
    const card = buildFanCard(cardData);
    card.dataset.ring = 'outer';
    container.appendChild(card);
    placeOnRing(card, i, outerN, R_outer, 1);
    card.addEventListener('click', () => revealFanCard(card, cardData));
  });

  cards.slice(outerN).forEach((cardData, i) => {
    const card = buildFanCard(cardData);
    card.dataset.ring = 'inner';
    container.appendChild(card);
    placeOnRing(card, i, innerN, R_inner, 2000);
    card.addEventListener('click', () => revealFanCard(card, cardData));
  });
```

Lasciare invariata la logica di shuffle all'inizio di `drawFan` (rimuovere pero il ramo legacy su `revealedFanCard.parentElement` se diventa morto dopo Task 3; in questo task puo restare).

- [ ] **Step 3: Verifica manuale layout**

Aprire `tarot.html` nel browser (file locale). Controllare:

1. Si vedono due curve con lo stesso centro (anello interno dentro l'esterno), non due file una sopra l'altra.
2. Circa meta carte fuori e meta dentro.
3. Refresh / resize senza carta aperta: layout si ricalcola, ancora concentrico.
4. Hover e click base ancora funzionano (anche se la chiusura completa arrivera al Task 2).

- [ ] **Step 4: Commit (dopo conferma utente)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-app.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: ventaglio iniziale a due archi concentrici"
```

---

### Task 2: Chiusura del ventaglio alla rivelazione

**Files:**
- Modify: `tarot-app.js` (`revealFanCard`, flag di stato)
- Modify: `tarot-style.css` (opzionale: transizione opacity sulle fan-card)
- Test: manuale in browser

**Interfaces:**
- Consumes: layout Task 1, `showFanResult`, `#fan-result`
- Produces: `fanBusy` (boolean globale); alla reveal le non-scelte si richiudono verso il centro e spariscono; carta scelta al centro rivelata

- [ ] **Step 1: Aggiungere flag globale anti-doppio-click**

Vicino a `revealedFanCard` (cercare la dichiarazione esistente in `tarot-app.js`), aggiungere:

```javascript
let fanBusy = false;
```

Se `revealedFanCard` non e ancora dichiarato come `let` in cima alla sezione fan, dichiararlo esplicitamente:

```javascript
let revealedFanCard = null;
let fanBusy = false;
```

- [ ] **Step 2: Riscrivere `revealFanCard` con richiusura delle altre carte**

Sostituire il corpo di `revealFanCard` con questa logica (mantenere firma `revealFanCard(card, cardData)`):

```javascript
function revealFanCard(card, cardData) {
  if (fanBusy) return;
  if (card.classList.contains('revealed')) return;
  if (revealedFanCard) return;

  fanBusy = true;
  const container = card.parentElement;
  if (!container) { fanBusy = false; return; }

  const W = container.clientWidth;
  const H = container.clientHeight;
  const others = [...container.querySelectorAll('.fan-card')].filter(c => c !== card);

  // Richiusura delle altre carte verso il centro del container
  others.forEach((c, idx) => {
    c.style.pointerEvents = 'none';
    c.style.transition = 'transform 0.7s cubic-bezier(0.4, 0, 0.2, 1), left 0.7s cubic-bezier(0.4, 0, 0.2, 1), top 0.7s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.7s ease';
    const delay = Math.min(idx * 8, 120);
    c.style.transitionDelay = delay + 'ms';
    c.style.left = (W / 2 - c.offsetWidth / 2) + 'px';
    c.style.top = (H / 2 - c.offsetHeight / 2) + 'px';
    c.style.transform = 'rotate(0deg) scale(0.3)';
    c.style.opacity = '0';
  });

  container.classList.add('has-revealed');
  card.classList.add('revealed');
  card.style.transition = 'transform 1.2s cubic-bezier(0.4, 0, 0.2, 1), left 1.2s cubic-bezier(0.4, 0, 0.2, 1), top 1.2s cubic-bezier(0.4, 0, 0.2, 1)';
  card.style.transitionDelay = '0ms';
  card.style.left = (W / 2 - card.offsetWidth / 2) + 'px';
  card.style.top = (H / 2 - card.offsetHeight / 2) + 'px';
  card.style.transform = 'rotateY(720deg) scale(1.1)';
  card.style.zIndex = '999';

  revealedFanCard = card;

  const result = document.getElementById('fan-result');
  if (result) result.classList.add('hidden');

  setTimeout(() => {
    others.forEach(c => c.remove());
    if (cardData.isReversed) {
      card.classList.add('reversed');
    } else {
      card.classList.add('flipped');
    }
    showFanResult(cardData);
    fanBusy = false;
  }, 1200);
}
```

Nota: non ripristinare piu la carta precedente nel mazzo durante un secondo click su un'altra carta — dopo la chiusura le altre non ci sono.

- [ ] **Step 3: CSS di supporto (opzionale ma utile)**

In `tarot-style.css`, dopo `.fan-container.has-revealed .fan-card:not(.revealed)`, aggiornare cosi che durante la chiusura non resti il solo attenuamento statico come unico effetto (la JS gestisce opacity). Lasciare la regola esistente o ridurla a:

```css
.fan-container.has-revealed .fan-card:not(.revealed) {
  pointer-events: none;
}
```

- [ ] **Step 4: Verifica manuale chiusura**

1. Click su una carta esterna: le altre si chiudono verso il centro e spariscono; la scelta resta al centro e si rivela; compare il risultato.
2. Click su una carta interna: stesso comportamento.
3. Doppio click rapido: non parte una seconda animazione.
4. Non devono restare carte “fantasma” nel container oltre a quella rivelata.

- [ ] **Step 5: Commit (dopo conferma utente)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-app.js tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: chiusura ventaglio alla rivelazione della carta"
```

---

### Task 3: Rientro, dissolvenza risultato, rimescolamento e riapertura

**Files:**
- Modify: `tarot-app.js` (`returnFanToDeck` / `resetFan`, listener click su carta rivelata)
- Modify: `tarot-style.css` (dissolvenza `.fan-result`)
- Test: manuale in browser

**Interfaces:**
- Consumes: `revealedFanCard`, `fanBusy`, `drawFan`, `#fan-result`
- Produces: `returnFanToDeck()` usata da click sulla carta rivelata e da `resetFan()`; ciclo completo secondo lo spec

- [ ] **Step 1: CSS dissolvenza risultato**

In `tarot-style.css`, aggiornare `.fan-result` aggiungendo transizione opacity, e uno stato che dissolve:

```css
.fan-result {
  width: 100%;
  max-width: 640px;
  margin-top: 2rem;
  background: rgba(255,255,255,0.04);
  border: 1px solid rgba(192,132,252,0.3);
  border-radius: 16px;
  padding: 1.8rem 2rem;
  animation: fadeIn 0.6s ease;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  opacity: 1;
  transition: opacity 0.8s ease;
}

.fan-result.hidden {
  display: none;
}

.fan-result.is-fading {
  opacity: 0;
  pointer-events: none;
}
```

Verificare che `.hidden` esista gia altrove; se `.fan-result.hidden` e gia coperto da una regola globale `.hidden { display: none; }`, non duplicare in conflitto — in quel caso aggiungere solo `opacity`/`transition` su `.fan-result` e la classe `.is-fading`.

- [ ] **Step 2: Implementare `returnFanToDeck` e collegare i trigger**

Aggiungere in `tarot-app.js` (dopo `showFanResult`):

```javascript
function returnFanToDeck() {
  if (fanBusy) return;
  if (!revealedFanCard) {
    drawFan();
    return;
  }

  fanBusy = true;
  const card = revealedFanCard;
  const container = document.getElementById('fan-container');
  const result = document.getElementById('fan-result');

  if (result && !result.classList.contains('hidden')) {
    result.classList.add('is-fading');
  }

  card.style.transition = 'transform 0.8s cubic-bezier(0.4, 0, 0.2, 1), left 0.8s cubic-bezier(0.4, 0, 0.2, 1), top 0.8s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.8s ease';
  card.style.left = card.dataset.origLeft || card.style.left;
  card.style.top = card.dataset.origTop || card.style.top;
  card.style.transform = `rotate(${card.dataset.theta || 0}deg) scale(0.5)`;
  card.style.opacity = '0';

  setTimeout(() => {
    if (result) {
      result.classList.add('hidden');
      result.classList.remove('is-fading');
      result.style.opacity = '';
    }
    revealedFanCard = null;
    fanBusy = false;
    if (container) container.classList.remove('has-revealed');
    drawFan();
  }, 850);
}

function resetFan() {
  returnFanToDeck();
}
```

- [ ] **Step 3: Click sulla carta rivelata per rientrare**

In `revealFanCard`, dopo aver assegnato `revealedFanCard = card`, assicurarsi che un click successivo sulla stessa carta chiami il rientro. Il modo piu semplice: all'inizio di `revealFanCard` gestire il caso revealed, **oppure** registrare un listener una tantum dopo la reveal.

Preferito — all'inizio di `revealFanCard` (prima dei return di guardia gia scritti), questa variante e gia coperta se separiamo cosi:

Nel listener creato in `drawFan`:

```javascript
card.addEventListener('click', () => {
  if (card.classList.contains('revealed')) {
    returnFanToDeck();
    return;
  }
  revealFanCard(card, cardData);
});
```

E all'inizio di `revealFanCard` tenere i guard:

```javascript
if (fanBusy) return;
if (revealedFanCard) return;
```

Cosi il bottone `onclick="resetFan()"` e il click sulla carta rivelata condividono lo stesso percorso.

- [ ] **Step 4: Pulire `drawFan` dal ramo legacy su carta gia rivelata**

All'inizio del recupero carte in `drawFan`, semplificare a:

```javascript
  revealedFanCard = null;
  fanBusy = false;
  const cards = shuffleDeck(getAllCards());
```

Rimuovere il blocco `if (revealedFanCard && revealedFanCard.parentElement) { ... }`. Alla fine di `drawFan` non serve un secondo `revealedFanCard = null` se gia azzerato sopra (lasciarne uno solo).

Aggiornare il resize listener esistente:

```javascript
window.addEventListener('resize', () => {
  const container = document.getElementById('fan-container');
  if (!container) return;
  if (fanBusy) return;
  if (revealedFanCard) return;
  drawFan();
});
```

- [ ] **Step 5: Verifica manuale ciclo completo**

1. Apri pagina → due anelli concentrici.
2. Scegli una carta → chiusura + rivelazione + testo.
3. Click sulla carta al centro → testo dissolve in parallelo, poi ventagli si riaprono con ordine diverso.
4. Ripeti usando il bottone «Scegli un'Altra Carta» invece del click sulla carta → stesso esito.
5. Durante animazioni, click extra ignorati.
6. Resize a ventaglio aperto (nessuna reveal): ok. Resize con carta rivelata: layout non si spezza; al rientro successivo e corretto.
7. Smoke su larghezza mobile (DevTools).

- [ ] **Step 6: Commit (dopo conferma utente)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-app.js tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: rientro carta e riapertura ventaglio rimescolato"
```

---

## Self-Review (piano vs spec)

| Requisito spec | Task |
|---|---|
| Due archi concentrici stesso centro | Task 1 |
| Meta / meta carte | Task 1 |
| Richiusura verso il centro alla scelta | Task 2 |
| Carta scelta resta al centro e si rivela | Task 2 |
| Click carta rivelata → rientro | Task 3 |
| Bottone «Scegli un'Altra Carta» stesso ciclo | Task 3 |
| Dissolvenza testo in parallelo | Task 3 |
| Rimescolamento random + riapertura | Task 3 |
| Ignorare click durante animazioni | Task 2–3 (`fanBusy`) |
| Resize con lettura aperta non interrompe | Task 3 |
| Non toccare `tarot-cards.js` / spread | rispettato |

Nessun TBD nel piano. Nomi allineati: `returnFanToDeck`, `fanBusy`, `placeOnRing`, `R_outer` / `R_inner`.
