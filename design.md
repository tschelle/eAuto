# Design-Dokumentation — Vergleichsportal

Dieses Dokument beschreibt das vollstaendige Design-System des Portals (Start.html, index.html, TV-Fernseher.html). Alle drei Seiten teilen dieselben Tokens, Typo-Regeln und Komponentenstile.

---

## 1. Designprinzipien

- **Dark-first**: Standard ist immer dunkel. Hell ist explizite Wahl des Nutzers.
- **Keine Emojis**: Alle Symbole und Labels sind Text oder CSS.
- **Uppercase-Labels**: Kategoriebezeichnungen, Eyebrows und Metadaten in Kapitaelchen mit Laufweite.
- **Mixed Typography auf Start.html**: Fette Sans-Headline mit kursiver Serifschrift als Akzentwort.
- **Pill-Buttons**: Filter- und Rubriken-Buttons haben `border-radius: 999px`.
- **Keine Inter-Page-Navigation**: index.html und TV-Fernseher.html haben keinen Link zur anderen Seite. Start.html ist der einzige Einstiegspunkt.

---

## 2. Farbsystem (CSS Custom Properties)

Alle Farben sind als Tokens auf `:root` definiert. Komponenten referenzieren ausschliesslich Tokens, nie Hex-Werte direkt (Ausnahme: fixe Gruppenheader-Farben).

### 2.1 Dark Mode (Standard / Default)

```css
:root {
  --bg:      #060c1a;   /* Seitenhintergrund — tiefes Navy */
  --surface: #0c1828;   /* Karten, Tabellen, Toolbar — etwas heller */
  --border:  #1b2f4a;   /* Rahmen, Trennlinien */
  --accent:  #4a94ff;   /* Blau — Links, aktive Zust. */
  --text:    #edf2ff;   /* Haupttext — fast weiss */
  --muted:   #6e8fb0;   /* Sekundaertext, Labels, Icons */
  --radius:  10px;      /* Standard Border-Radius fuer Karten */

  /* Aliase fuer Abwaertskompatibilitaet (index.html) */
  --text-muted:  #6e8fb0;
  --hover:       #102038;   /* Zeilen- und Button-Hover */
  --accent-light:#0a1e40;   /* Badge-Hintergrund */
  --header-bg:   #060c1a;   /* = --bg (kein separater Header-Hintergrund) */
  --header-text: #edf2ff;   /* = --text */
  --success:     #34d399;   /* Gruen (Ja-Werte in Tabelle) */
  --warn:        #fbbf24;   /* Gelb (Quell-Badge, Warnungen) */
}
```

### 2.2 Light Mode

Aktivierung per `@media (prefers-color-scheme: light)` ODER `[data-theme="light"]`.

```css
--bg:      #f0f4ff;
--surface: #ffffff;
--border:  #bfcfe8;
--accent:  #2060df;
--text:    #08102a;
--muted:   #4a6080;

--text-muted:  #4a6080;
--hover:       #e8ecf8;
--accent-light:#dce6ff;
--header-bg:   #08102a;
--header-text: #ffffff;
--success:     #059669;
--warn:        #d97706;
```

### 2.3 Theme-Override-Kaskade

```css
/* 1. Basis: dark */
:root { ... dark tokens ... }

/* 2. System-Praeferenz: hell */
@media (prefers-color-scheme: light) {
  :root:not([data-theme="dark"]) { ... light tokens ... }
}

/* 3. Manuelle Wahl erzwingt immer */
:root[data-theme="dark"]  { ... dark tokens ... }
:root[data-theme="light"] { ... light tokens ... }
```

**Regel**: Dark ist immer der Fallback. Nur wenn Nutzer explizit hell waehlt (Toggle oder OS) weicht die Seite ab.

### 2.4 Spezialfarben (nicht per Token)

Gruppenheader in index.html (Spaltengruppen-Zeile ueber der Tabelle):

| Kontext | Dark | Light |
|---|---|---|
| Gruppenheader gerade | `#1a3050` | `#2a4a7a` |
| Gruppenheader ungerade | `#243a5a` | `#3a5a8a` |
| Aktiver Rubriken-Toggle | `#1a3050` | `#2a4a7a` |

Technologie-Badges in TV-Fernseher.html:

| Badge | Hintergrund (dark) | Text (dark) |
|---|---|---|
| OLED / WOLED / QD-OLED | `#1e1240` | `#a78bfa` |
| Mini-LED | `#2a1800` | `#fbbf24` |
| 8K | `#0a2e2a` | `#2dd4bf` |

---

## 3. Typografie

Schriftart: Systemfont-Stack (kein Webfont-Download).

```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
             'Helvetica Neue', Arial, sans-serif;
```

### 3.1 Typo-Skala

| Rolle | Groesse | Weight | Weitere Eigenschaften |
|---|---|---|---|
| H1 (Start.html) | `clamp(2.6rem, 9vw, 3.8rem)` | 800 | `line-height: 1.06`, `letter-spacing: -0.035em`, `text-wrap: balance` |
| H1 Akzentwort `<em>` | (wie H1) | 400 | Georgia / Palatino italic, `color: var(--accent)` |
| Logo (Header) | `0.76rem` | 700 | Uppercase, `letter-spacing: 0.1em` |
| Badge / Eyebrow | `0.64–0.68rem` | 600 | Uppercase, `letter-spacing: 0.13–0.14em` |
| Nav-Link | `0.74rem` | 600 | Uppercase, `letter-spacing: 0.06em`, `color: var(--muted)` |
| Theme-Button | `0.7rem` | 600 | Uppercase, `letter-spacing: 0.09em` |
| Tile-Bigstat | `2.6rem` | 800 | `letter-spacing: -0.03em`, `font-variant-numeric: tabular-nums` |
| Tile-Desc | `0.87rem` | 400 | `line-height: 1.62`, `color: var(--muted)` |
| Lead-Text | `0.96rem` | 400 | `line-height: 1.72`, `color: var(--muted)` |
| Tabellen-Body | `12px` | 400 | `line-height: 1.3` |
| Tabellen-Header | `11px` | 500 | Uppercase ab Gruppenheader |
| Footer | `0.66rem` | 600 | Uppercase, `letter-spacing: 0.09em`, `color: var(--muted)` |

---

## 4. Gemeinsame Komponenten

### 4.1 Header

Alle drei Seiten haben denselben Header-Grundaufbau.

```
[LOGO-BOX]                             [Hell / Dunkel]
```

```css
header {
  height: 54–58px;
  padding: 0 24–28px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid var(--border);
  background: var(--bg);
  position: sticky;
  top: 0;
  z-index: 10–100;
}
```

**Logo-Box**:
```css
.logo {
  font-size: 0.76rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text);
  border: 1px solid var(--border);
  padding: 6px 14px;
  border-radius: 6px;
}
```

**Theme-Button**:
```css
.theme-btn {
  background: none;
  border: 1px solid var(--border);
  color: var(--muted);
  font-size: 0.7rem;
  font-weight: 600;
  letter-spacing: 0.09em;
  text-transform: uppercase;
  padding: 6px 12px;
  border-radius: 6px;
}
.theme-btn:hover { color: var(--text); border-color: var(--muted); }
```

Text: `"Hell"` im Dark Mode, `"Dunkel"` im Light Mode.

### 4.2 Pill-Buttons (Filter / Rubriken)

```css
.btn {
  padding: 6px 14px;
  border-radius: 999px;          /* Pill-Form */
  border: 1px solid var(--border);
  background: var(--surface);
  color: var(--text);
  font-size: 12–13px;
  transition: all 0.15s;
}
.btn:hover   { background: var(--hover); }
.btn.active  { background: var(--accent); color: #fff; border-color: var(--accent); }
```

### 4.3 Suchfeld

```css
.search-box {
  padding: 6px 12px;
  border-radius: 6px;
  border: 1px solid var(--border);
  background: var(--surface);
  color: var(--text);
  font-size: 14px;
}
.search-box:focus { outline: 2px solid var(--accent); border-color: transparent; }
```

---

## 5. Seiten-spezifisches Design

### 5.1 Start.html

**Aufbau** (von oben nach unten):

```
[Header: Logo + Hell/Dunkel-Toggle]
[Badge: "Persoenliches Vergleichsportal"]
[H1: "Vergleichen. Verstehen. Entscheiden."]
[Lead: Kurztext]
[Tile-Grid: 2 Spalten]
[Footer]
```

**Hero**:
- Zentriert, `padding: 64px 24px 96px`
- H1: drei Zeilen, mittlere Zeile `<em>` (kursiv, Serif, Akzentblau)
- Badge: Pill (`border-radius: 999px`), `background: var(--surface)`

**Tile-Grid**:
```css
.tiles {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 14px;
  max-width: 780px;
}
```

**Tile-Karte**:
```css
.tile {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius);   /* 10px */
  padding: 28px;
  text-decoration: none;
  transition: border-color 0.18s, transform 0.18s;
}
.tile:hover { border-color: var(--accent); transform: translateY(-2px); }
```

Innerer Aufbau jeder Tile:
1. **Eyebrow** — Uppercase-Label (`0.64rem`, `var(--muted)`)
2. **Bigstat** — grosse Zahl (dynamisch per JSON-fetch, Fallback `–`)
3. **Bigstat-Label** — z.B. "BEVs verglichen"
4. **Desc** — Kurzbeschreibung, `var(--muted)`
5. **Meta-Row** — 3 Stichpunkte, Uppercase, Border-Top
6. **Link** — "Zur Uebersicht →", `color: var(--accent)`

**Footer**:
```css
footer {
  text-align: center;
  padding: 20px 24px;
  font-size: 0.66rem;
  font-weight: 600;
  letter-spacing: 0.09em;
  text-transform: uppercase;
  color: var(--muted);
  border-top: 1px solid var(--border);
}
```

---

### 5.2 index.html (Elektroauto-Vergleich)

**Aufbau** (Vollbild-App, kein Seiten-Scroll):

```
[Header: Logo + Suche + Hell/Dunkel-Toggle]   ← sticky
[Toolbar: Autofilter-Presets + Export-Buttons]  ← flex-shrink: 0
[Rubriken-Leiste: Gruppen ein-/ausblenden]      ← flex-shrink: 0
[Tabelle mit internem Scroll]                   ← flex: 1
[Horizontale Custom-Scrollbar]                  ← flex-shrink: 0
[Statusleiste]                                  ← flex-shrink: 0
```

```css
body { display: flex; flex-direction: column; overflow: hidden; height: 100%; }
```

**Tabellenheader**:
```css
th {
  background: var(--header-bg);   /* = var(--bg) */
  color: var(--header-text);      /* = var(--text) */
  font-size: 11px;
  font-weight: 500;
  position: sticky;
  top: 0;
}
```

**Gruppenheader** (Spaltengruppen-Zeile):
- Dark: `#1a3050` / `#243a5a` (ungerade/gerade)
- Light: `#2a4a7a` / `#3a5a8a`
- Immer weisser Text, Uppercase, `font-size: 10px`

**Zellenwerte**:
```css
.bool-yes   { color: var(--success); font-weight: 600; }
.bool-no    { color: var(--muted); }
.no-data    { color: var(--muted); font-style: italic; }
.val-min    { color: var(--text); }
.val-max    { color: var(--muted); }
```

**Veraltet-Zeilen**:
```css
tr.veraltet td { background: rgba(239, 68, 68, 0.06); }
tr.veraltet .cell-modell { text-decoration: line-through; opacity: 0.7; }
```

---

### 5.3 TV-Fernseher.html

**Aufbau** (scrollbare Seite):

```
[Header: Logo + Hell/Dunkel-Toggle]   ← sticky
[Filter-Leiste: Pill-Buttons + Suche + CSV-Export]
[Tabelle]
[Statusleiste]
```

**Filter-Leiste**:
```css
.filter-bar {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  align-items: center;
  padding: 12px 24px;
  border-bottom: 1px solid var(--border);
  background: var(--surface);
}
```

**Technologie-Badges** (inline in Tabellenzellen):

| Wert | Dark Background | Dark Text | Bedeutung |
|---|---|---|---|
| WOLED / QD-OLED | `#1e1240` | `#a78bfa` | OLED-Varianten |
| Mini-LED | `#2a1800` | `#fbbf24` | Mini-LED |
| 8K | `#0a2e2a` | `#2dd4bf` | 8K-Aufloesung |

Light-Mode: helle Varianten dieser Farben (gleiche Hue-Familie).

**RTINGS-Score Farbkodierung**:
- Score ≥ 9.0: `color: var(--success)` + `font-weight: 700`
- Score < 9.0: `color: var(--warn)` + `font-weight: 700`

**2026-Zeilen** werden mit Akzent-Hintergrund hervorgehoben:
- Dark: `background: rgba(74, 148, 255, 0.05)`
- Light: `background: rgba(32, 96, 223, 0.04)`

---

## 6. Theme-System (JavaScript)

Alle drei Seiten implementieren dasselbe Theme-System mit identischen Keys.

```js
// LocalStorage-Key: 'theme'  (geteilt ueber alle Seiten)
// Werte: 'dark' | 'light'
// Default: 'dark' (wenn kein Wert gespeichert)

function getTheme() {
  return localStorage.getItem('theme') || 'dark';
}

function applyTheme(t) {
  document.documentElement.setAttribute('data-theme', t);
  document.getElementById('themeBtn').textContent = t === 'dark' ? 'Hell' : 'Dunkel';
  localStorage.setItem('theme', t);
}

// Start.html / TV-Fernseher.html (inline onclick):
function toggleTheme() { applyTheme(getTheme() === 'dark' ? 'light' : 'dark'); }
applyTheme(getTheme());

// index.html (addEventListener):
document.getElementById('themeBtn').addEventListener('click', () => {
  applyTheme(getTheme() === 'dark' ? 'light' : 'dark');
});
applyTheme(getTheme());
```

Der Nutzer wechselt das Theme einmal → alle Seiten des Portals folgen beim naechsten Laden.

---

## 7. Dynamische Inhalte (Start.html)

Tile-Statistiken werden beim Seitenstart per `fetch` geladen:

```js
// Fahrzeuganzahl aus eAuto.json
fetch('eAuto.json?t=' + Date.now())
  .then(r => r.json())
  .then(d => {
    const n = (d.fahrzeuge || []).length;
    if (n > 0) document.getElementById('carCount').textContent = n;
  }).catch(() => {});

// TV-Geraeteanzahl aus TV-Fernseher.json
fetch('TV-Fernseher.json?t=' + Date.now())
  .then(r => r.json())
  .then(d => {
    const n = Array.isArray(d) ? d.length : (d.fernseher || d.geraete || []).length;
    if (n > 0) document.getElementById('tvCount').textContent = n;
  }).catch(() => {});
```

Fallback: `–` (Lang-Bindestrich) wenn JSON nicht erreichbar (z.B. `file://`-Modus).

---

## 8. Abstands- und Layout-Regeln

- **Seiten-Padding**: `24–28px` horizontal auf allen Seiten
- **Header-Hoehe**: `54–58px` (index.html: 54px, Start.html: 58px)
- **Gap zwischen Elementen**: `flex/grid gap`, kein Einzelelement-Margin
- **Karten-Padding**: `28px`
- **Tile-Gap**: `14px`
- **Button-Gap in Leisten**: `6–8px`
- **Border-Radius Karten**: `10px` (`var(--radius)`)
- **Border-Radius Buttons (Pill)**: `999px`
- **Border-Radius Logo, Theme-Btn**: `6px`
- **Transitions**: `0.15–0.18s` fuer Hover-Effekte

---

## 9. Content Security Policy

Alle drei Seiten haben identische CSP-Header:

```html
<meta http-equiv="Content-Security-Policy"
  content="default-src 'self';
           script-src 'self' 'unsafe-inline';
           style-src 'self' 'unsafe-inline';
           img-src 'self' data: blob:;
           connect-src 'self';
           frame-ancestors 'none';
           base-uri 'self';
           form-action 'self';">
```

Kein CDN, keine externen Schriften, kein externes JS — alle Seiten funktionieren offline und als `file://`.

---

## 10. Erweiterungen: Neues Design anlegen

Checkliste fuer jede neue Seite im Portal:

- [ ] CSS-Tokens aus Abschnitt 2 uebernehmen (identisch, kein Abweichen)
- [ ] Theme-Toggle mit `id="themeBtn"` und `getTheme()`/`applyTheme()` implementieren
- [ ] LocalStorage-Key `theme` verwenden (nicht seitenspezifisch)
- [ ] Header: Logo-Box + Theme-Button, **keine Nav-Links zu anderen Seiten**
- [ ] Pill-Buttons fuer alle Filter (`border-radius: 999px`)
- [ ] Uppercase-Labels fuer alle Kategorien und Metadaten
- [ ] Dark als Standard, kein `prefers-color-scheme: dark`-Block (Dark ist `:root`)
- [ ] CSP-Meta-Tag wie oben
- [ ] Kachel auf Start.html ergaenzen (inkl. dynamischem fetch der Elementanzahl)
- [ ] Eintrag in `eAutoBrain.md` Dateien-Tabelle und Changelog
