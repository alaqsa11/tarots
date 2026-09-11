# Stile Zine Esoterico Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Far sembrare le carte fedeli allo stile zine/incisione del riferimento Behance, e rendere la pagina esoterica sulla stessa base ma piu sobria (con pochi accenti viola/oro) cosi le carte restano il pezzo forte.

**Architecture:** Tutto via CSS (+ link font in HTML). Le carte usano un tema monocromo crema/nero dedicato (filtri immagine, cornici, tipografia etichetta). La pagina usa un tema affine ma distinto (texture leggera, titoli gotici, palette crema/nero + accenti `#c084fc` / `#facc15`). Nessun cambio a `tarot-cards.js` ne alla logica del ventaglio.

**Tech Stack:** HTML/CSS/JS vanilla, Google Fonts CDN (Cinzel Decorative o UnifrakturCook + Cormorant Garamond), verifica manuale in browser. Niente Node/npm.

## Global Constraints

- Carte: fedelta alta al riferimento Behance (stampa zine crema+nero); non copiare asset del progetto.
- Pagina: stessa famiglia, ma distinta dalle carte; pochi accenti viola/oro.
- Stesse immagini carte, trattate in CSS (filtri); niente nuove illustrazioni.
- Non modificare `tarot-cards.js` ne i flussi funzionali.
- Niente Node.js/npm; verifica = browser manuale.
- Messaggi commit in italiano senza accenti; chiedere conferma utente prima dei commit se richiesto dal progetto (in esecuzione piano: commit per task come sotto).
- Testi UI restano in italiano.

## File Structure

| File | Ruolo |
|---|---|
| `tarot.html` | Link font gotici/serif |
| `tarot-style.css` | Tema pagina + tema carte (variabili, filtri, cornici, tipografia) |
| `tarot-app.js` | Non toccare salvo necessita minima di classi |
| `tarot-cards.js` | Non toccare |

---

### Task 1: Font e tema pagina (ambiente esoterico distinto dalle carte)

**Files:**
- Modify: `tarot.html` (head)
- Modify: `tarot-style.css` (`:root`, `body`, `nav`, `header`, titoli, sezioni)
- Test: manuale browser `tarot.html`

**Interfaces:**
- Consumes: markup esistente
- Produces: CSS variables `--ink`, `--paper`, `--paper-deep`, `--accent-violet`, `--accent-gold`, `--font-display`, `--font-body`; pagina crema/nero con accenti; titoli con font display gotico/serif

- [ ] **Step 1: Aggiungere font in `tarot.html`**

Subito dopo il charset/viewport, prima di `tarot-style.css`, inserire:

```html
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@400;700;900&family=Cormorant+Garamond:ital,wght@0,400;0,600;0,700;1,400&display=swap" rel="stylesheet">
```

- [ ] **Step 2: Introdurre variabili e base body**

In cima a `tarot-style.css` (dopo il reset `*`, o sostituendo `body` / aggiungendo `:root`), usare:

```css
:root {
  --ink: #12100e;
  --ink-soft: #2a2622;
  --paper: #f3ece0;
  --paper-deep: #e6dcc8;
  --accent-violet: #7c5cbf;
  --accent-gold: #c4a35a;
  --font-display: 'Cinzel Decorative', 'Times New Roman', serif;
  --font-body: 'Cormorant Garamond', 'Georgia', serif;
  --page-noise: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.55'/%3E%3C/svg%3E");
}

body {
  font-family: var(--font-body);
  color: var(--ink-soft);
  background: var(--paper);
  line-height: 1.7;
  position: relative;
}

body::before {
  content: '';
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9998;
  opacity: 0.07;
  background-image: var(--page-noise);
  mix-blend-mode: multiply;
}
```

- [ ] **Step 3: Aggiornare nav, hero e titoli pagina (senza copiare il look carta)**

Aggiornare blocchi esistenti cosi che la pagina sia esoterica ma piu chiara/sobria delle carte:

```css
nav {
  background: rgba(243, 236, 224, 0.92);
  border-bottom: 1px solid rgba(18, 16, 14, 0.15);
  backdrop-filter: blur(10px);
}

.nav-brand {
  font-family: var(--font-display);
  font-weight: 700;
  color: var(--ink);
}

.nav-brand i {
  color: var(--accent-gold);
}

nav ul li a {
  color: var(--ink-soft);
  font-family: var(--font-body);
  font-size: 1.1rem;
}

nav ul li a:hover {
  color: var(--accent-violet);
}

#hamburger {
  color: var(--ink);
}

header {
  background:
    radial-gradient(ellipse at center, rgba(124, 92, 191, 0.12) 0%, transparent 55%),
    linear-gradient(180deg, var(--paper) 0%, var(--paper-deep) 100%);
}

.hero-overlay {
  opacity: 0.08;
  filter: grayscale(1) contrast(1.2);
}

.hero-content h1,
.fan-title,
#stese > h2,
#mazzo > h2,
#reading-section h2 {
  font-family: var(--font-display);
  color: var(--ink);
  font-weight: 700;
  letter-spacing: 0.02em;
}

.hero-content h1 span {
  background: none;
  -webkit-text-fill-color: var(--accent-violet);
  color: var(--accent-violet);
}

.hero-content p,
.fan-subtitle,
#stese > p,
#mazzo > p {
  color: var(--ink-soft);
}

.btn {
  font-family: var(--font-body);
  background: var(--ink);
  color: var(--paper);
  border: 1px solid var(--ink);
}

.btn:hover {
  background: var(--accent-violet);
  border-color: var(--accent-violet);
  color: #fff;
}

.btn-secondary {
  background: transparent;
  color: var(--ink);
  border: 1px solid var(--ink);
}
```

Adattare anche sezioni scure esistenti (`#stese`, `#mazzo`, footer, `.spread-card`, pannelli) a fondo `var(--paper)` / `var(--paper-deep)` con testo `var(--ink)`, bordi `rgba(18,16,14,0.2)`, accenti oro/viola solo su icone e hover — densita inferiore rispetto alle carte (niente doppia cornice pesante su ogni card UI).

Se un selettore nel file attuale usa colori hard-coded `#0a0a1a`, `#c084fc`, `#fff` su fondi scuri, sostituirli in modo coerente con le variabili sopra **solo per chrome di pagina**, non ancora per `.card-face` (Task 2).

- [ ] **Step 4: Verifica manuale tema pagina**

Aprire `tarot.html` (server locale o file). Controllare:

1. Sfondo crema, non viola scuro pieno.
2. Titoli con Cinzel Decorative.
3. Accenti viola/oro presenti ma limitati.
4. Le carte possono ancora avere lo stile vecchio in questo task: ok.

- [ ] **Step 5: Commit**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot.html tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: tema pagina esoterico crema-nero con accenti"
```

(Usare env author se git config assente, come nei commit precedenti.)

---

### Task 2: Stile carte fedele al riferimento (fronte e retro)

**Files:**
- Modify: `tarot-style.css` (blocchi `.card-face`, `.card-back`, `.card-front`, `.card-image`, `.card-name`, tag, `.fan-card`)
- Test: manuale browser — ventaglio, stesa, mazzo

**Interfaces:**
- Consumes: variabili Task 1; markup carte esistente
- Produces: carte monocromo crema/nero con cornici doppie, filtri incisione sulle immagini, etichette tipografiche; aspetto chiaramente piu "stampato" della pagina

- [ ] **Step 1: Ridefinire superfici carta (crema + nero, cornici)**

Sostituire/aggiornare i blocchi `.card-face`, `.card-back`, `.card-front` (e pattern retro) con:

```css
.card-face {
  position: absolute;
  inset: 0;
  border-radius: 4px;
  backface-visibility: hidden;
  overflow: hidden;
  box-shadow:
    0 1px 0 rgba(243, 236, 224, 0.35) inset,
    0 8px 18px rgba(18, 16, 14, 0.35);
}

.card-back {
  background:
    linear-gradient(135deg, #ebe3d4 0%, #f7f1e6 45%, #e4d9c4 100%);
  border: 2px solid var(--ink);
  display: flex;
  align-items: center;
  justify-content: center;
}

.card-back::before {
  content: '';
  position: absolute;
  inset: 7px;
  border: 1px solid var(--ink);
  border-radius: 2px;
  pointer-events: none;
  opacity: 0.85;
}

.card-back-pattern {
  width: 78%;
  height: 82%;
  border: 1px solid rgba(18, 16, 14, 0.55);
  border-radius: 2px;
  display: flex;
  align-items: center;
  justify-content: center;
  background:
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 6px,
      rgba(18, 16, 14, 0.06) 6px,
      rgba(18, 16, 14, 0.06) 7px
    ),
    repeating-linear-gradient(
      90deg,
      transparent,
      transparent 6px,
      rgba(18, 16, 14, 0.06) 6px,
      rgba(18, 16, 14, 0.06) 7px
    );
  position: relative;
  z-index: 1;
}

.card-back-pattern i {
  font-size: 2rem;
  color: var(--ink);
  opacity: 0.85;
}

.card-front {
  background: #f7f1e6;
  border: 2px solid var(--ink);
  transform: rotateY(180deg);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 0;
  text-align: center;
  overflow: hidden;
}

.card-front::before {
  content: '';
  position: absolute;
  inset: 6px;
  border: 1px solid var(--ink);
  border-radius: 2px;
  pointer-events: none;
  z-index: 3;
  opacity: 0.9;
}
```

Rimuovere il vecchio bordo viola `#c084fc` e il gradient scuro dalle facce carta.

- [ ] **Step 2: Trattamento immagini stile incisione**

```css
.card-front .card-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0;
  filter:
    grayscale(1)
    contrast(1.55)
    brightness(1.05)
    sepia(0.15);
  mix-blend-mode: multiply;
}

.card-front .card-symbol {
  color: var(--ink);
  font-size: 2.2rem;
}
```

Se `.card-symbol` non ha regole, aggiungerle come sopra.

- [ ] **Step 3: Etichetta nome e badge in chiave tipografica zine**

```css
.card-front .card-name {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: #f7f1e6;
  border-top: 1px solid var(--ink);
  backdrop-filter: none;
  padding: 0.35rem 0.45rem 0.45rem;
  font-family: var(--font-display);
  font-size: 0.58rem;
  color: var(--ink);
  font-weight: 700;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  line-height: 1.15;
  z-index: 4;
}

.card-front .card-reversed-tag,
.card-front .card-upright-tag {
  bottom: 2.1rem;
  border-radius: 0;
  border: 1px solid var(--ink);
  font-family: var(--font-body);
  font-size: 0.55rem;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  padding: 0.12rem 0.35rem;
  z-index: 4;
}

.card-front .card-upright-tag {
  background: #f7f1e6;
  color: var(--ink);
}

.card-front .card-reversed-tag {
  background: var(--ink);
  color: #f7f1e6;
}
```

Aggiornare anche `.tarot-card.fan-card .card-face` se impone `border-radius: 8px` / `border-width: 2px` in conflitto: allineare a `border-radius: 4px`.

Attenuare glow oro su `.fan-card.revealed` (opzionale ma consigliato):

```css
.fan-card.revealed {
  filter: drop-shadow(0 10px 18px rgba(18, 16, 14, 0.45));
}

.fan-card.revealed .card-front {
  box-shadow: none;
}
```

- [ ] **Step 4: Verifica manuale carte vs pagina**

1. Ventaglio coperto: retro crema/nero, non viola neon.
2. Carta rivelata: immagine in bianco-nero/incisione, cornice doppia, nome gotico.
3. Stesa e mazzo: stesso look.
4. A colpo d'occhio le carte sono piu "stampe" della pagina (contrasto e cornici piu forti).

- [ ] **Step 5: Commit**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: carte in stile zine stampa crema-nero"
```

---

### Task 3: Coerenza pannelli, footer e smoke desktop/mobile

**Files:**
- Modify: `tarot-style.css` (`.fan-result`, `.interp-*`, footer, `.spread-card`, media query mobile se i nomi carta sono illeggibili)
- Test: manuale browser

**Interfaces:**
- Consumes: tema Task 1–2
- Produces: UI secondaria allineata al tema pagina (non al monocromo pieno delle carte); leggibilita mobile

- [ ] **Step 1: Allineare pannello risultato ventaglio e badge interpretazione**

```css
.fan-result {
  background: rgba(243, 236, 224, 0.85);
  border: 1px solid rgba(18, 16, 14, 0.25);
  color: var(--ink-soft);
}

.fan-result-name {
  font-family: var(--font-display);
  color: var(--ink);
}

.fan-result-card h4 {
  font-family: var(--font-display);
  color: var(--accent-violet);
}

.interp-badge {
  border: 1px solid var(--ink);
  border-radius: 0;
  font-family: var(--font-body);
  background: #f7f1e6;
  color: var(--ink);
}

.interp-badge.reversed {
  background: var(--ink);
  color: #f7f1e6;
}

.interp-keyword {
  border: 1px solid rgba(18, 16, 14, 0.35);
  border-radius: 0;
  background: transparent;
  color: var(--ink-soft);
}
```

Se le classi `.interp-badge` / `.interp-keyword` hanno stili viola glow, sostituirli in coerenza.

- [ ] **Step 2: Footer e spread-card (ambiente, non carte)**

Assicurarsi che `.spread-card` e `footer` usino fondi `var(--paper-deep)` / bordi inchiostro soft e titoli `var(--font-display)`, **senza** doppia cornice interna come sulle tarot-card (cosi resta chiara la differenza oggetto-carta vs UI).

- [ ] **Step 3: Mobile — nome carta ventaglio**

Nel media query esistente (~768px), se serve:

```css
  .tarot-card.fan-card .card-name {
    font-size: 0.42rem;
    padding: 0.2rem 0.25rem;
  }

  .tarot-card.fan-card .card-front::before,
  .tarot-card.fan-card .card-back::before {
    inset: 4px;
  }
```

- [ ] **Step 4: Smoke test completo**

1. Desktop: pagina esoterica; carte stile zine; differenza evidente.
2. Click ventaglio: reveal + risultato leggibile.
3. Una stesa + mazzo: carte coerenti.
4. Larghezza ~375px: testi e cornici ok.
5. Nessuna regressione funzionale ovvia.

- [ ] **Step 5: Commit**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: allinea pannelli UI al tema esoterico"
```

---

## Self-Review (piano vs spec)

| Requisito spec | Task |
|---|---|
| Carte fedeli al Behance (crema/nero, stampa) | Task 2 |
| Immagini trattate, non sostituite | Task 2 |
| Pagina esoterica affine ma distinta | Task 1 + 3 |
| Accenti viola/oro solo su UI pagina | Task 1 + 3 |
| Font piu gotici | Task 1 (display) + Task 2 (etichette) |
| Nessun cambio flussi / tarot-cards.js | rispettato |
| Mobile leggibile | Task 3 |

Nessun TBD nel piano. Variabili e selettori allineati tra i task.
