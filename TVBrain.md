# TVBrain - TV-Fernseher Vergleich: Projektdokumentation

## Projektziel
Vergleich der besten Fernseher der Jahrgaenge 2025 und 2026 anhand einheitlicher technischer Kriterien. Alle Daten in `TV-Fernseher.json`, Darstellung ueber `TV-Fernseher.html`.

---

## Status

### Erfasste Geraete (21 Fernseher, 8 Marken — Stand 2026-07-23)

| Rang | Marke | Modell | Jahr | Technologie | Aufloesung |
|------|-------|--------|------|-------------|------------|
| 1 | LG | G5 OLED | 2025 | WOLED | 4K |
| 2 | Samsung | S95F QD-OLED | 2025 | QD-OLED | 4K |
| 3 | Panasonic | Z95B OLED | 2025 | WOLED | 4K |
| 4 | Sony | Bravia 8 II | 2025 | WOLED | 4K |
| 5 | Philips | OLED909 | 2025 | QD-OLED | 4K |
| 6 | LG | C5 OLED | 2025 | WOLED | 4K |
| 7 | Samsung | S90F QD-OLED | 2025 | QD-OLED | 4K |
| 8 | LG | B5 OLED | 2025 | WOLED | 4K |
| 9 | Sony | Bravia 9 | 2025 | Mini-LED | 4K |
| 10 | Samsung | QN90F Neo QLED | 2025 | Mini-LED | 4K |
| 11 | Hisense | U9N Mini-LED | 2025 | Mini-LED | 4K |
| 12 | LG | QNED99 Mini-LED | 2025 | Mini-LED | 4K |
| 13 | Samsung | QN85F Neo QLED | 2025 | Mini-LED | 4K |
| 14 | TCL | C855K QD-Mini-LED | 2025 | Mini-LED | 4K |
| 15 | Hisense | U8N Mini-LED | 2025 | Mini-LED | 4K |
| 16 | Samsung | QN900F Neo QLED | 2025 | Mini-LED | 8K |
| 17 | LG | G6 OLED | 2026 | WOLED | 4K |
| 18 | Samsung | S90H QD-OLED | 2026 | QD-OLED | 4K |
| 19 | Sony | Bravia 9 II | 2026 | Mini-LED | 4K |
| 20 | Samsung | S95H QD-OLED | 2026 | QD-OLED | 4K |
| 21 | Sharp | JP7265E Mini-LED | 2025 | Mini-LED | 4K |

---

## Dateien

| Datei | Beschreibung |
|---|---|
| **TVBrain.md** | Projektdokumentation und Anleitung (diese Datei) |
| **TV-Fernseher.json** | Zentrale TV-Daten (20 Geraete, alle Felder) |
| **TV-Fernseher.html** | TV-Vergleichstabelle (laedt TV-Fernseher.json per fetch) |

---

## Features von TV-Fernseher.html

### Obere Leiste
- **Titel** "TV-Fernseher Vergleich 2025/2026" (links) + Zurueck-Links zur Startseite und Autoübersicht
- **Dark/Light Toggle** — Sonne/Mond-Symbol (rechts)

### Filter-Buttons
| Filter | Funktion |
|---|---|
| Alle | Zeigt alle 20 Geraete |
| OLED | Nur WOLED und QD-OLED |
| Mini-LED | Nur Mini-LED-Geraete |
| 4K | Nur 4K-Aufloesung |
| 8K | Nur 8K-Aufloesung |
| Nur 2025 | Nur Modelle des Jahrgangs 2025 |
| Nur 2026 | Nur Modelle des Jahrgangs 2026 |

### Suchfeld
Echtzeit-Suche ueber: Marke, Modell, Betriebssystem, Technologie

### Tabelle
- **Sortierbare Spalten** — Klick auf Header (aufsteigend/absteigend, mit Pfeil-Indikator)
- **Technologie-Badges** — WOLED (lila), QD-OLED (lila), Mini-LED (amber)
- **8K-Badge** — teal
- **RTINGS-Score farbkodiert** — ≥ 9.0 gruen, sonst amber
- **2026-Zeilen** — Akzent-Hintergrund zur visuellen Hervorhebung
- **Produktlink** — klickbares Symbol oeffnet Hersteller-Seite im neuen Tab

### CSV-Export
- Button exportiert die aktuelle gefilterte Ansicht als UTF-8 CSV (Semikolon-getrennt)
- Dateiname: `TV-Fernseher-Export.csv`

### Dark / Light Mode
- Automatisch per `prefers-color-scheme` (Systemeinstellung)
- Manuell umschaltbar per Toggle-Button; Praeferenz wird in LocalStorage gespeichert

### Statuszeile
- Zeigt Anzahl der angezeigten Geraete (gefiltert / gesamt)

---

## JSON-Struktur pro Fernseher (TV-Fernseher.json)

```
rang                  — Platzierung (1–20)
marke                 — Hersteller (z.B. "LG", "Samsung")
modell                — Kurzbezeichnung (z.B. "G5 OLED")
vollname              — Vollstaendige Modellbezeichnung
jahr                  — Erscheinungsjahr (2025 oder 2026)
technologie           — Paneltechnologie: "WOLED" | "QD-OLED" | "Mini-LED"
panel                 — Panelbezeichnung des Herstellers (z.B. "evo9 MLA")
aufloesung            — "4K" | "8K"
groessen_zoll         — Array verfuegbarer Groessen (z.B. [55, 65, 77, 83])
helligkeit_hdr_nits   — Spitzenhelligkeit HDR in Nit (float)
helligkeit_sdr_nits   — Helligkeit SDR in Nit (float)
hdmi_2_1_ports        — Anzahl HDMI-2.1-Eingaenge (int)
refresh_rate_hz       — Native Bildwiederholrate in Hz (int)
dolby_vision          — bool
hdr10_plus            — bool
hlg                   — bool (Hybrid Log-Gamma)
vrr                   — bool (Variable Refresh Rate)
g_sync                — bool (NVIDIA G-Sync Compatible)
freesync              — bool (AMD FreeSync Premium)
betriebssystem        — z.B. "webOS 25", "Tizen 8", "Google TV"
lautsprecher_watt     — Ausgangsleistung Soundsystem in Watt (int)
earc                  — bool (Enhanced Audio Return Channel)
preis_55_eur          — UVP 55 Zoll in EUR (int, null wenn nicht verfuegbar)
preis_65_eur          — UVP 65 Zoll in EUR (int, null wenn nicht verfuegbar)
preis_77_eur          — UVP 77 Zoll in EUR (int, null wenn nicht verfuegbar)
rtings_score          — RTINGS.com Gesamtwertung (float, z.B. 9.3)
kategorie             — Segmentbezeichnung (z.B. "OLED Premium", "Mini-LED Flaggschiff")
besonderheit          — Kurzbeschreibung der Besonderheiten (string)
produkt_url           — Link zur Hersteller-Produktseite
```

---

## Anleitung

### Seite aufrufen (GitHub Pages)
Live: **https://tschelle.github.io/eAuto/TV-Fernseher.html**
Laedt automatisch TV-Fernseher.json aus dem Repo. Keine Serverinstallation noetig.

### Lokal starten
```bash
python3 -m http.server 9090
```
Dann im Browser: `http://localhost:9090/TV-Fernseher.html`

### Neuen Fernseher hinzufuegen (mit Claude Code)
1. Claude Code oeffnen (App, Desktop oder claude.ai/code)
2. Diese Datei (`TVBrain.md`) als Kontext uebergeben
3. Prompt: **"Ergaenze TV-Fernseher.json um [Modellname]. Dann commit und push."**
4. Claude ergaenzt alle Felder gemaess Schema, committed und pusht
5. GitHub Pages aktualisiert (~1–2 Min)

Beispiel-Prompts:
- "Ergaenze den Samsung S90D in die TV-Fernseher.json und pushe"
- "Fuege den LG C4 als Modell 2024 hinzu"
- "Aktualisiere den Preis vom LG G5 auf 1.899 EUR (55 Zoll)"

Wichtig fuer Claude Code:
- GitHub Repo: `tschelle/eAuto` (public, GitHub Pages aktiv)
- Nur `TV-Fernseher.json` aendern
- Rang anpassen wenn neue Geraete einsortiert werden
- Nach Aenderung: `git add TV-Fernseher.json && git commit -m "..." && git push`

### Neuen Fernseher manuell hinzufuegen
1. In `TV-Fernseher.json` neuen Eintrag im `fernseher`-Array ergaenzen
2. Rang der bestehenden Geraete ggf. anpassen
3. `git add TV-Fernseher.json && git commit -m "Add [Modell]" && git push`
4. GitHub Pages baut automatisch neu (~1–2 Min)

---

## GitHub Repository

- **Repo:** https://github.com/tschelle/eAuto
- **Live:** https://tschelle.github.io/eAuto/TV-Fernseher.html
- **Branch:** main
- **Auto-Deploy:** GitHub Actions (pages.yml) — bei jedem Push auf main, ~1–2 Min

---

## Naechste Schritte
- 2026-Modelle ergaenzen sobald Preise und RTINGS-Scores verfuegbar (LG G6, Sony Bravia 9 II, Samsung S95G vollstaendig)
- Preis fuer 85/98 Zoll ergaenzen (aktuell nur 55/65/77 Zoll)
- Bewertungsquelle neben RTINGS ergaenzen (z.B. RTINGS vs. AVForums)
- Filteroption "Gaming" (VRR + HDMI 2.1 + hohe Hz)
- Filteroption "Kino" (Dolby Vision + WOLED/QD-OLED)

---

## Hinweise
- Preise sind UVP zum Erscheinungsdatum — Strassenpreise weichen regelmaessig ab
- RTINGS-Scores koennen sich durch Software-Updates am TV aendern
- Helligkeit in Nit: Messungen unter optimierten Testbedingungen (kein typischer Wohnzimmerwert)
- Dolby Vision und HDR10+ schliessen sich herstellerseitig oft gegenseitig aus (Samsung: HDR10+, LG/Sony/Panasonic: Dolby Vision)
- 2026-Modelle: Angaben teilweise vorlaeufig (pre-release)
