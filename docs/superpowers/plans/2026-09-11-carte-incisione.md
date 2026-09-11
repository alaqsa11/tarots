# Carte tarocchi in stile incisione Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rifare tutte le 78 illustrazioni delle carte nello stile incisione (nero su crema), tenendo le scene attuali e i titoli originali nel disegno.

**Architecture:** Ogni carta ha un file locale in `images/tarot/`. Le fonti Rider-Waite restano in `images/tarot-src/` solo per la generazione. `CARD_IMAGES` in `tarot-cards.js` punta ai PNG locali. Lo stile si ottiene ridisegnando l'immagine (GenerateImage + riferimento zine), non con filtri CSS. I filtri grayscale/seppia sulle immagini vanno rimossi quando il mazzo e completo, cosi non sporcano i nuovi disegni.

**Tech Stack:** HTML/CSS/JS vanilla, immagini PNG locali, GenerateImage (Cursor) con due riferimenti (carta originale + stile incisione). Verifica manuale in browser. Niente Node/npm.

## Global Constraints

- Stesse scene dei disegni attuali (Rider-Waite): stesse figure, oggetti, pose, composizione.
- Stile: incisione inchiostro nero su carta crema, tratteggio e puntinatura, come il riferimento zine. Non copiare il soggetto di quel riferimento (figura, meme "my next piercing", titolo THE DYING LIGHT).
- Tenere il titolo originale gia presente nel disegno (es. THE FOOL, ACE OF WANDS). Non inventare titoli nuovi.
- Nome italiano in Cinzel resta l'etichetta UI sotto/sopra la carta.
- Tutte le 78 carte, anche quelle in basso nel mazzo.
- Dritta/Invertita solo nel testo di interpretazione, mai sopra la carta (gia rimosso da `tarot-app.js`; non reintrodurlo).
- Non toccare l'ingrandimento della carta cliccata nel ventaglio (`revealFanCard`, `scale(1.1)`, dimensioni `.fan-card`).
- Non modificare `TAROT_CARDS` (testi, keyword, significati). Solo `CARD_IMAGES`.
- Niente Node.js/npm. Verifica = browser su `http://127.0.0.1:8766/tarot.html`.
- Git: `& "C:\Program Files\Git\cmd\git.exe"`. Non `git config`. Messaggi commit in italiano senza accenti. Chiedere conferma a Greta prima di ogni commit.
- Testi UI restano in italiano.

## File Structure

| File | Ruolo |
|---|---|
| `images/tarot-src/` | JPEG originali Rider-Waite (solo input generazione) |
| `images/tarot-src/style-ref.jpg` | Copia locale del riferimento stile incisione |
| `images/tarot/*.png` | 78 carte finite, usate dal sito |
| `tarot-cards.js` | `CARD_IMAGES` punta ai PNG locali |
| `tarot-style.css` | Togliere filtri grayscale dalle immagini finite; non toccare il ventaglio |
| `tarot-app.js` | Non toccare (tag Dritta/Invertita gia assenti; ventaglio invariato) |

Gia presenti (non rigenerare se lo stile e ancora quello approvato):

- `images/tarot/00-fool.png`
- `images/tarot/09-hermit.png`
- `images/tarot/13-death.png`

---

### Prompt di generazione (identico per ogni carta)

Usare lo strumento Cursor `GenerateImage` (`namespace: cursor`) con:

- `aspect_ratio`: `"3:4"`
- `filename`: il nome file della riga (es. `01-magician-woodcut.png`)
- `reference_image_paths`:
  1. JPEG originale in `images/tarot-src/...`
  2. `images/tarot-src/style-ref.jpg`
- `description` (sostituire solo `[CARD ENGLISH TITLE]`):

```
Redraw this Rider-Waite tarot card as a vintage woodcut engraving print. Keep the EXACT same composition, figures, objects, poses, numerals, and the original English title banner from the source card ([CARD ENGLISH TITLE]). Style must match the second reference: black ink on aged cream paper, heavy stippling, cross-hatching, engraved linework, antique zine print, high contrast, limited or no color fills. Do NOT add meme text, watermarks, or a new title. Do NOT invent a new scene. Vertical tarot card.
```

Poi copiare il PNG generato (di solito in `C:\Users\Greta\.cursor\projects\c-Users-Greta-Desktop-unicam-prova-tarots\assets\`) in `images/tarot/<nome-finale>.png`.

Download fonti Wikimedia: thumb **250px** (altri size possono dare 400). Header `User-Agent` da browser. Se 429, attendere e riprovare. Non usare Node.

```powershell
$ua = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/128.0.0.0 Safari/537.36"
$headers = @{ "User-Agent" = $ua; "Accept" = "image/jpeg"; "Referer" = "https://commons.wikimedia.org/" }
Invoke-WebRequest -Uri "<URL>" -OutFile "<PATH>" -Headers $headers -UseBasicParsing
```

Server locale (se spento):

```powershell
$root='C:\Users\Greta\Desktop\unicam\prova\tarots'
$l=New-Object System.Net.HttpListener
$l.Prefixes.Add('http://127.0.0.1:8766/')
$l.Start()
# ... stesso listener gia usato nel progetto (mappa path -> file, png/jpg/html/css/js)
```

---

### Task 1: Completare gli Arcani Maggiori

**Files:**
- Create: `images/tarot-src/style-ref.jpg` (copia del riferimento zine)
- Create: `images/tarot-src/01-magician.jpg` ... fonti Wikimedia elencate sotto (skip 00, 09, 13 gia scaricati)
- Create: `images/tarot/01-magician.png` ... (skip 00, 09, 13)
- Modify: `tarot-cards.js` (`CARD_IMAGES.major`)

**Interfaces:**
- Consumes: 3 PNG campione gia approvati; URL Wikimedia in `CARD_IMAGES.major`
- Produces: 22 path locali `images/tarot/00-fool.png` ... `21-world.png`

- [ ] **Step 1: Copiare il riferimento stile in repo**

Copiare:

`C:\Users\Greta\.cursor\projects\c-Users-Greta-Desktop-unicam-prova-tarots\assets\c__Users_Greta_AppData_Roaming_Cursor_User_workspaceStorage_020d71cb1fe861e859db4d8d4c1a9db9_images_image-66c5fe4a-2d28-420d-b2a4-128d8c026243.jpg`

in `images/tarot-src/style-ref.jpg`.

- [ ] **Step 2: Scaricare le fonti mancanti e generare i PNG**

| id | titolo inglese | URL fonte | src | output |
|---|---|---|---|---|
| 1 | THE MAGICIAN | `https://upload.wikimedia.org/wikipedia/commons/thumb/d/de/RWS_Tarot_01_Magician.jpg/250px-RWS_Tarot_01_Magician.jpg` | `images/tarot-src/01-magician.jpg` | `images/tarot/01-magician.png` |
| 2 | THE HIGH PRIESTESS | `https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/RWS_Tarot_02_High_Priestess.jpg/250px-RWS_Tarot_02_High_Priestess.jpg` | `images/tarot-src/02-high-priestess.jpg` | `images/tarot/02-high-priestess.png` |
| 3 | THE EMPRESS | `https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/RWS_Tarot_03_Empress.jpg/250px-RWS_Tarot_03_Empress.jpg` | `images/tarot-src/03-empress.jpg` | `images/tarot/03-empress.png` |
| 4 | THE EMPEROR | `https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/RWS_Tarot_04_Emperor.jpg/250px-RWS_Tarot_04_Emperor.jpg` | `images/tarot-src/04-emperor.jpg` | `images/tarot/04-emperor.png` |
| 5 | THE HIEROPHANT | `https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/RWS_Tarot_05_Hierophant.jpg/250px-RWS_Tarot_05_Hierophant.jpg` | `images/tarot-src/05-hierophant.jpg` | `images/tarot/05-hierophant.png` |
| 6 | THE LOVERS | `https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/RWS_Tarot_06_Lovers.jpg/250px-RWS_Tarot_06_Lovers.jpg` | `images/tarot-src/06-lovers.jpg` | `images/tarot/06-lovers.png` |
| 7 | THE CHARIOT | `https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/RWS_Tarot_07_Chariot.jpg/250px-RWS_Tarot_07_Chariot.jpg` | `images/tarot-src/07-chariot.jpg` | `images/tarot/07-chariot.png` |
| 8 | STRENGTH | `https://upload.wikimedia.org/wikipedia/commons/thumb/f/f5/RWS_Tarot_08_Strength.jpg/250px-RWS_Tarot_08_Strength.jpg` | `images/tarot-src/08-strength.jpg` | `images/tarot/08-strength.png` |
| 10 | WHEEL OF FORTUNE | `https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/RWS_Tarot_10_Wheel_of_Fortune.jpg/250px-RWS_Tarot_10_Wheel_of_Fortune.jpg` | `images/tarot-src/10-wheel.jpg` | `images/tarot/10-wheel.png` |
| 11 | JUSTICE | `https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/RWS_Tarot_11_Justice.jpg/250px-RWS_Tarot_11_Justice.jpg` | `images/tarot-src/11-justice.jpg` | `images/tarot/11-justice.png` |
| 12 | THE HANGED MAN | `https://upload.wikimedia.org/wikipedia/commons/thumb/2/2b/RWS_Tarot_12_Hanged_Man.jpg/250px-RWS_Tarot_12_Hanged_Man.jpg` | `images/tarot-src/12-hanged-man.jpg` | `images/tarot/12-hanged-man.png` |
| 14 | TEMPERANCE | `https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/RWS_Tarot_14_Temperance.jpg/250px-RWS_Tarot_14_Temperance.jpg` | `images/tarot-src/14-temperance.jpg` | `images/tarot/14-temperance.png` |
| 15 | THE DEVIL | `https://upload.wikimedia.org/wikipedia/commons/thumb/5/55/RWS_Tarot_15_Devil.jpg/250px-RWS_Tarot_15_Devil.jpg` | `images/tarot-src/15-devil.jpg` | `images/tarot/15-devil.png` |
| 16 | THE TOWER | `https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/RWS_Tarot_16_Tower.jpg/250px-RWS_Tarot_16_Tower.jpg` | `images/tarot-src/16-tower.jpg` | `images/tarot/16-tower.png` |
| 17 | THE STAR | `https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/RWS_Tarot_17_Star.jpg/250px-RWS_Tarot_17_Star.jpg` | `images/tarot-src/17-star.jpg` | `images/tarot/17-star.png` |
| 18 | THE MOON | `https://upload.wikimedia.org/wikipedia/commons/thumb/7/7f/RWS_Tarot_18_Moon.jpg/250px-RWS_Tarot_18_Moon.jpg` | `images/tarot-src/18-moon.jpg` | `images/tarot/18-moon.png` |
| 19 | THE SUN | `https://upload.wikimedia.org/wikipedia/commons/thumb/1/17/RWS_Tarot_19_Sun.jpg/250px-RWS_Tarot_19_Sun.jpg` | `images/tarot-src/19-sun.jpg` | `images/tarot/19-sun.png` |
| 20 | JUDGEMENT | `https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/RWS_Tarot_20_Judgement.jpg/250px-RWS_Tarot_20_Judgement.jpg` | `images/tarot-src/20-judgement.jpg` | `images/tarot/20-judgement.png` |
| 21 | THE WORLD | `https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/RWS_Tarot_21_World.jpg/250px-RWS_Tarot_21_World.jpg` | `images/tarot-src/21-world.jpg` | `images/tarot/21-world.png` |

Non rigenerare 00, 09, 13.

- [ ] **Step 3: Aggiornare `CARD_IMAGES.major`**

Sostituire l'array `major` con:

```javascript
  major: [
    "images/tarot/00-fool.png",
    "images/tarot/01-magician.png",
    "images/tarot/02-high-priestess.png",
    "images/tarot/03-empress.png",
    "images/tarot/04-emperor.png",
    "images/tarot/05-hierophant.png",
    "images/tarot/06-lovers.png",
    "images/tarot/07-chariot.png",
    "images/tarot/08-strength.png",
    "images/tarot/09-hermit.png",
    "images/tarot/10-wheel.png",
    "images/tarot/11-justice.png",
    "images/tarot/12-hanged-man.png",
    "images/tarot/13-death.png",
    "images/tarot/14-temperance.png",
    "images/tarot/15-devil.png",
    "images/tarot/16-tower.png",
    "images/tarot/17-star.png",
    "images/tarot/18-moon.png",
    "images/tarot/19-sun.png",
    "images/tarot/20-judgement.png",
    "images/tarot/21-world.png"
  ],
```

- [ ] **Step 4: Verifica browser Arcani Maggiori**

Aprire `http://127.0.0.1:8766/tarot.html#mazzo`, tab Arcani Maggiori.

1. Tutte e 22 le miniature sono PNG locali (nessun URL wikimedia).
2. Stile incisione coerente con Matto / Eremita / Morte.
3. Titolo originale visibile nel disegno.
4. Nome italiano sotto la card.
5. Nessun "Dritta"/"Invertita" sul fronte.

- [ ] **Step 5: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add images/tarot images/tarot-src tarot-cards.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: arcani maggiori in stile incisione"
```

---

### Task 2: Bastoni (14)

**Files:**
- Create: `images/tarot-src/wands-01.jpg` ... `wands-14.jpg`
- Create: `images/tarot/wands-01.png` ... `wands-14.png`
- Modify: `tarot-cards.js` (`CARD_IMAGES.wands`)

**Interfaces:**
- Consumes: prompt + style-ref del Task 1
- Produces: 14 PNG locali per Bastoni

- [ ] **Step 1: Scaricare, generare, copiare**

| idx | titolo | file Wikimedia | output |
|---|---|---|---|
| 1 | ACE OF WANDS | `Wands01.jpg` | `images/tarot/wands-01.png` |
| 2 | TWO OF WANDS | `Wands02.jpg` | `images/tarot/wands-02.png` |
| 3 | THREE OF WANDS | `Wands03.jpg` | `images/tarot/wands-03.png` |
| 4 | FOUR OF WANDS | `Wands04.jpg` | `images/tarot/wands-04.png` |
| 5 | FIVE OF WANDS | `Wands05.jpg` | `images/tarot/wands-05.png` |
| 6 | SIX OF WANDS | `Wands06.jpg` | `images/tarot/wands-06.png` |
| 7 | SEVEN OF WANDS | `Wands07.jpg` | `images/tarot/wands-07.png` |
| 8 | EIGHT OF WANDS | `Wands08.jpg` | `images/tarot/wands-08.png` |
| 9 | NINE OF WANDS | `Wands09.jpg` | `images/tarot/wands-09.png` |
| 10 | TEN OF WANDS | `Wands10.jpg` | `images/tarot/wands-10.png` |
| 11 | PAGE OF WANDS | `Wands11.jpg` | `images/tarot/wands-11.png` |
| 12 | KNIGHT OF WANDS | `Wands12.jpg` | `images/tarot/wands-12.png` |
| 13 | QUEEN OF WANDS | `Wands13.jpg` | `images/tarot/wands-13.png` |
| 14 | KING OF WANDS | `Wands14.jpg` | `images/tarot/wands-14.png` |

URL: `https://upload.wikimedia.org/wikipedia/commons/thumb/<hash>/<file>/250px-<file>` — usare gli URL gia in `CARD_IMAGES.wands` (thumb 250px). Fonte locale: `images/tarot-src/wands-NN.jpg`.

- [ ] **Step 2: Aggiornare `CARD_IMAGES.wands`**

```javascript
  wands: [
    "images/tarot/wands-01.png",
    "images/tarot/wands-02.png",
    "images/tarot/wands-03.png",
    "images/tarot/wands-04.png",
    "images/tarot/wands-05.png",
    "images/tarot/wands-06.png",
    "images/tarot/wands-07.png",
    "images/tarot/wands-08.png",
    "images/tarot/wands-09.png",
    "images/tarot/wands-10.png",
    "images/tarot/wands-11.png",
    "images/tarot/wands-12.png",
    "images/tarot/wands-13.png",
    "images/tarot/wands-14.png"
  ],
```

- [ ] **Step 3: Verifica browser tab Bastoni**

`#mazzo` → Bastoni: 14 carte incisione, titolo originale, nome italiano.

- [ ] **Step 4: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add images/tarot images/tarot-src tarot-cards.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: bastoni in stile incisione"
```

---

### Task 3: Coppe (14)

**Files:**
- Create: `images/tarot-src/cups-01.jpg` ... `cups-14.jpg`
- Create: `images/tarot/cups-01.png` ... `cups-14.png`
- Modify: `tarot-cards.js` (`CARD_IMAGES.cups`)

**Interfaces:**
- Consumes: prompt + style-ref
- Produces: 14 PNG Coppe

- [ ] **Step 1: Scaricare, generare, copiare**

Stesso schema del Task 2. File Wikimedia `Cups01.jpg` ... `Cups14.jpg`. URL gia in `CARD_IMAGES.cups`. Titoli: ACE/TWO/.../TEN OF CUPS, PAGE/KNIGHT/QUEEN/KING OF CUPS.

Output: `images/tarot/cups-01.png` ... `cups-14.png`.

- [ ] **Step 2: Aggiornare `CARD_IMAGES.cups`**

```javascript
  cups: [
    "images/tarot/cups-01.png",
    "images/tarot/cups-02.png",
    "images/tarot/cups-03.png",
    "images/tarot/cups-04.png",
    "images/tarot/cups-05.png",
    "images/tarot/cups-06.png",
    "images/tarot/cups-07.png",
    "images/tarot/cups-08.png",
    "images/tarot/cups-09.png",
    "images/tarot/cups-10.png",
    "images/tarot/cups-11.png",
    "images/tarot/cups-12.png",
    "images/tarot/cups-13.png",
    "images/tarot/cups-14.png"
  ],
```

- [ ] **Step 3: Verifica browser tab Coppe**

- [ ] **Step 4: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add images/tarot images/tarot-src tarot-cards.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: coppe in stile incisione"
```

---

### Task 4: Spade (14)

**Files:**
- Create: `images/tarot-src/swords-01.jpg` ... `swords-14.jpg`
- Create: `images/tarot/swords-01.png` ... `swords-14.png`
- Modify: `tarot-cards.js` (`CARD_IMAGES.swords`)

**Interfaces:**
- Consumes: prompt + style-ref
- Produces: 14 PNG Spade

- [ ] **Step 1: Scaricare, generare, copiare**

File Wikimedia `Swords01.jpg` ... `Swords14.jpg`. URL gia in `CARD_IMAGES.swords`. Titoli: ACE/TWO/... OF SWORDS, PAGE/KNIGHT/QUEEN/KING OF SWORDS.

- [ ] **Step 2: Aggiornare `CARD_IMAGES.swords`**

```javascript
  swords: [
    "images/tarot/swords-01.png",
    "images/tarot/swords-02.png",
    "images/tarot/swords-03.png",
    "images/tarot/swords-04.png",
    "images/tarot/swords-05.png",
    "images/tarot/swords-06.png",
    "images/tarot/swords-07.png",
    "images/tarot/swords-08.png",
    "images/tarot/swords-09.png",
    "images/tarot/swords-10.png",
    "images/tarot/swords-11.png",
    "images/tarot/swords-12.png",
    "images/tarot/swords-13.png",
    "images/tarot/swords-14.png"
  ],
```

- [ ] **Step 3: Verifica browser tab Spade**

- [ ] **Step 4: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add images/tarot images/tarot-src tarot-cards.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: spade in stile incisione"
```

---

### Task 5: Denari (14)

**Files:**
- Create: `images/tarot-src/pents-01.jpg` ... `pents-14.jpg`
- Create: `images/tarot/pents-01.png` ... `pents-14.png`
- Modify: `tarot-cards.js` (`CARD_IMAGES.pentacles`)

**Interfaces:**
- Consumes: prompt + style-ref
- Produces: 14 PNG Denari; a questo punto `CARD_IMAGES` non deve contenere nessun URL wikimedia

- [ ] **Step 1: Scaricare, generare, copiare**

File Wikimedia `Pents01.jpg` ... `Pents14.jpg`. URL gia in `CARD_IMAGES.pentacles`. Titoli: ACE/TWO/... OF PENTACLES, PAGE/KNIGHT/QUEEN/KING OF PENTACLES.

- [ ] **Step 2: Aggiornare `CARD_IMAGES.pentacles`**

```javascript
  pentacles: [
    "images/tarot/pents-01.png",
    "images/tarot/pents-02.png",
    "images/tarot/pents-03.png",
    "images/tarot/pents-04.png",
    "images/tarot/pents-05.png",
    "images/tarot/pents-06.png",
    "images/tarot/pents-07.png",
    "images/tarot/pents-08.png",
    "images/tarot/pents-09.png",
    "images/tarot/pents-10.png",
    "images/tarot/pents-11.png",
    "images/tarot/pents-12.png",
    "images/tarot/pents-13.png",
    "images/tarot/pents-14.png"
  ]
```

- [ ] **Step 3: Verifica browser tab Denari**

Controllare anche che in `tarot-cards.js` non resti `wikimedia` / `thumb.wikimedia`.

- [ ] **Step 4: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add images/tarot images/tarot-src tarot-cards.js
& "C:\Program Files\Git\cmd\git.exe" commit -m "feat: denari in stile incisione"
```

---

### Task 6: Filtri CSS e smoke ventaglio/stese

**Files:**
- Modify: `tarot-style.css` (`.card-front .card-image`, `.deck-card .deck-card-image`)
- Test: browser ventaglio, una stesa, mazzo, mobile ~375px

**Interfaces:**
- Consumes: 78 PNG locali
- Produces: immagini a contrasto naturale (niente grayscale extra); ventaglio invariato

- [ ] **Step 1: Togliere i filtri sulle immagini finite**

In `.card-front .card-image` sostituire il blocco `filter` / `mix-blend-mode` con:

```css
.card-front .card-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0;
  filter: none;
  mix-blend-mode: normal;
}
```

In `.deck-card .deck-card-image`:

```css
.deck-card .deck-card-image {
  width: 100%;
  border-radius: 0;
  margin-bottom: 0.5rem;
  aspect-ratio: 2/3.5;
  object-fit: cover;
  border: 2px solid var(--ink);
  filter: none;
  mix-blend-mode: normal;
}
```

Non modificare `.fan-card`, `.fan-card.revealed`, ne `revealFanCard` in `tarot-app.js`.

- [ ] **Step 2: Smoke test completo**

1. Mazzo: tutte le tab, stesso look incisione.
2. Ventaglio: click su una carta — si ingrandisce come prima (`scale(1.1)`); fronte senza Dritta/Invertita; Dritta/Invertita solo nel riquadro di interpretazione.
3. Una stesa (es. Carta Singola): stessa illustrazione, stesso titolo originale, nome italiano.
4. Larghezza ~375px: nomi ancora leggibili.
5. Nessuna regressione su mescola / reset ventaglio.

- [ ] **Step 3: Commit (solo dopo conferma Greta)**

```bash
& "C:\Program Files\Git\cmd\git.exe" add tarot-style.css
& "C:\Program Files\Git\cmd\git.exe" commit -m "fix: toglie filtri CSS dalle carte gia in incisione"
```

---

## Self-Review (piano vs spec)

| Requisito spec | Task |
|---|---|
| Scene attuali rifatte a incisione | 1–5 |
| Titolo originale nel disegno tenuto | prompt + verifica |
| Nome italiano Cinzel | gia in UI; non toccare |
| 78 carte, anche mazzo in basso | 1–5 |
| Dritta/Invertita solo in interpretazione | gia fatto; Task 6 verifica |
| Ventaglio ingrandito invariato | Task 6 non tocca JS/CSS ventaglio |
| Niente meme / niente THE DYING LIGHT | prompt |
| Filtri CSS non sporcano i nuovi disegni | Task 6 |

Nessun TBD. `getCardImage` invariato nella logica, cambiano solo le stringhe in `CARD_IMAGES`.
