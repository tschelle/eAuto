# eAutoBrain - Projektdokumentation

## Projektziel
Strukturierte Erfassung und Vergleich vollelektrischer Fahrzeuge (BEV) anhand eines einheitlichen Datenblatt-Templates. Alle Daten in `eAuto.json`, Darstellung über `index.html`.

---

## Status

### Erfasste Fahrzeuge (124 Varianten, 52 Marken — Stand 2026-07-18)

Eingebettete Daten im HTML (FIXIERT — 23 Varianten, 15 Marken):
BMW (iX1 eDrive20, iX1 xDrive30, i4 eDrive40), Tesla (Model 3 LR, Model Y LR),
VW (ID.4 Pro, ID.7 Pro S), Hyundai (Ioniq 5, Ioniq 6), Kia (EV6, EV3),
Mercedes (EQA 250+, EQE 350+), BYD (Atto 3, Seal), Polestar 2,
Skoda Enyaq 85, Cupra Born, Renault Megane E-Tech, Volvo EX30,
MG4 Electric, Audi Q4 e-tron 55, Opel Mokka Electric

Nur in eAuto.json (101 weitere Varianten):
- Kleinwagen: Fiat (500e, 600e), Dacia Spring, Mini (Cooper SE, Countryman SE ALL4), Peugeot e-208, Kia EV2, Cupra Raval
- Smart: #1, #3
- Mercedes: EQB 250+, EQS 450+, EQC 400 4MATIC (eingestellt), EQE SUV 350+ (veraltet), EQS SUV 450+, GLC 400 EQ 4MATIC
- XPENG: G6, Mona L03
- Premium: Porsche (Macan Electric, Macan GTS, Taycan, Taycan Sport Turismo, Taycan Cross Turismo, Cayenne Electric, Cayenne Coupé Electric), BMW (iX xDrive50, i5 eDrive40, iX3 Neue Klasse, i7 xDrive60), Lexus RZ 450e, Genesis GV60
- Volumen: VW (ID.3 Pro S, ID.5 Pro, ID. Buzz LWB), Ford (Mustang Mach-E, Explorer), Peugeot (e-3008, e-208), Nissan Ariya, Toyota bZ4X, Renault (5 E-Tech, Scenic E-Tech), Kia (EV5, EV9 GT-Line AWD, PV5 Passenger)
- Nischen: Jeep Avenger, Honda e:Ny1, Citroen (e-C4, e-C3 44 kWh)
- Nachzügler: Skoda Elroq 85, Cupra Tavascan VZ, Mercedes CLA 250+ EQ, Hyundai (Kona 65 kWh, Inster LR, IONIQ 9 LR AWD), Opel Corsa Electric 51 kWh
- China: NIO (ET5, EL6) — mit Batterietausch, Deepal S05, Denza (D9 EV, N7, Z9 GT, Z), Zeekr (X, 001, 007, 009, Mix)
- Oberklasse: Lucid (Air Pure, Air Grand Touring, Gravity), Tesla (Model S, Model X), Mercedes EQV 300, Ferrari Luce
- Ergänzt: Audi (Q4 Sportback, Q6, Q6 Sportback, Q8, e-tron GT, A6 e-tron), BMW iX2, Volvo (EX40 ER, EX90 Twin), Polestar (3, 4), Alfa Romeo Junior
- Campervan: Tonke (ID.Buzz Explorer, EQV Touring), Iridium e-V25, Poessl E-Vanster
- eLKW: Mercedes-Benz Trucks (eActros 600), MAN (eTGX), Volvo Trucks (FH Aero Electric), Tesla (Semi LR), DAF (XF Electric), Scania (45 R BEV), IVECO (S-eWay Artic), Renault Trucks (E-Tech T 780)

### Bemerkungen (persoenliche Notizen)
23 Fahrzeuge haben persoenliche Bemerkungen aus Probefahrten und Recherche.
Quelle: eAuto_intern.json → uebertragen in eAuto.json am 2026-06-20.

### Detaillierte Datenblaetter (Markdown)
| Fahrzeug | Datei | Status | Datum |
|---|---|---|---|
| BMW iX1 (eDrive20 / xDrive30) | [BMW_iX1.md](BMW_iX1.md) | vollstaendig | 2026-06-16 |

---

## Dateien

| Datei | Beschreibung |
|---|---|
| **eAutoBrain.md** | Projektdokumentation und Anleitung (diese Datei) |
| **eAuto.json** | Zentrale Fahrzeugdaten (124 Fahrzeuge, alle Felder) |
| **eAuto_intern.json** | Persoenliche Arbeitsdatei mit Bemerkungen/Probefahrt-Notizen (nicht im Repo) |
| **BMW_iX1.md** | Lesbares Datenblatt BMW iX1 (Detailansicht) |
| **index.html** | Elektroauto-Vergleichstabelle (laedt eAuto.json per fetch) |

---

## Datenhaltung

### Datenquellen-Hierarchie
1. **eAuto.json** im gleichen Verzeichnis wie index.html → wird automatisch geladen (benoetigt Server / GitHub Pages)
2. **JSON-Import** ueber Button in der Toolbar → laedt beliebige JSON-Datei
3. **Eingebettete Daten** (DEFAULT_DATA im HTML) → Fallback wenn kein JSON vorhanden (file:// Modus)

### Regeln
- **eAuto.json** ist die zentrale, editierbare Datenquelle
- **WICHTIG: Die 23 eingebetteten Varianten im HTML (DEFAULT_DATA) sind FIXIERT — NICHT mehr aendern!**
- Neue Fahrzeuge: NUR in eAuto.json eintragen
- Bereiche (z.B. "458–514 km") als `{"min": 458, "max": 514}` gespeichert
- `user_wish` (Praeferenz) wird im Browser via LocalStorage gespeichert (nicht in JSON)
- `bemerkung` kann sowohl in der JSON als auch via LocalStorage gepflegt werden
- **eAuto_intern.json** ist in `.gitignore` und wird NICHT gepusht
- Bei eingebetteten Daten: gelbes **"int"**-Badge in der Pref-Spalte
- Bei JSON-Laden/Import: kein Badge

---

## Features von index.html

### Obere Leiste
- **Titel** "eAuto Vergleich" (links) + Zurueck-Link zur Startseite
- **Suche** — globale Volltextsuche ueber alle Felder (rechts)
- **Dark/Light Toggle** — Sonne/Mond-Symbol (rechts)

### Autofilter-Presets
Alle | Praeferenz | Reichweite > 400 km | Preis < 50.000 EUR | Allrad | DC > 150 kW | AC 22 kW | Waermepumpe | 0-100 < 6s

### Toolbar-Buttons
| Button | Funktion |
|---|---|
| CSV Export | Exportiert aktuelle gefilterte Ansicht als CSV (nur sichtbare Spalten) |
| JSON Export | Exportiert aktuelle gefilterte Ansicht als JSON-Datei |
| JSON Import | Laedt externe JSON-Datei und ersetzt Tabellendaten |
| Filter zuruecksetzen | Setzt alle Filter, Suche und Sortierung zurueck |

### Rubriken-Leiste
Toggle-Buttons zum Ein-/Ausblenden von Spaltengruppen. Zustand wird in LocalStorage gespeichert.
- **Alle** — blendet alle Gruppen ein (deaktiviert "Keine")
- **Keine** — blendet alle Gruppen aus, nur Pref/Modell/Marke/Segment/Link/Bemerkung bleiben
- **Einzelne Rubriken** — individuell ein-/ausklappbar

### Tabelle
- **Sticky Header** — Ueberschriften bleiben beim vertikalen Scrollen sichtbar
- **Horizontales Scrollen** — Scrollbar immer sichtbar
- **Gruppen-Header** — farbige Zeile ueber den Spalten mit Gruppenname
- **ⓘ Info-Icons** — jeder Spaltenheader hat ein klickbares ⓘ-Symbol; Klick zeigt deutschsprachige Beschreibung der Spalte als Floating-Popup
- **Spaltenfilter** — Eingabefeld unter jeder Spalte:
  - Freitext: filtert auf Teilstring
  - `>Zahl`: groesser als (z.B. `>400`)
  - `<Zahl`: kleiner als (z.B. `<50000`)
  - `=Wert`: exakter Match
  - **Multiselect** (Segment, Chemie, Zelltyp): Dropdown mit Mehrfachauswahl per Checkbox
- **Sortierung** — Klick auf Spaltenheader (aufsteigend/absteigend)
- **Praeferenz** — editierbares Zahlenfeld pro Zeile (LocalStorage)
- **Bemerkung** — editierbares Textfeld pro Zeile (LocalStorage)
- **Link** — klickbares 🔗-Symbol, oeffnet Hersteller-Modellseite im neuen Tab
- **Bild** — klickbares 📷-Symbol, oeffnet Wikipedia-Artikel mit Fahrzeugfoto im neuen Tab

### Statusleiste (unten)
- Links: Anzahl Varianten/Modelle (gefiltert / gesamt)
- Rechts: Datenquelle (z.B. "eAuto.json geladen (124 Fahrzeuge)" oder "Eingebettete Daten (23 Fahrzeuge)")

---

## Spaltengruppen

| Gruppe | Spalten | Ein-/Ausklappbar |
|---|---|---|
| (ohne) | ID, Pref, Modell, Marke, Segment, Link, Bild | immer sichtbar |
| Sicherheit | ADAC, NCAP Sterne, Erw.%, Kind%, Fuss%, Assi% | ja |
| Motor | Antrieb, kW, PS, Nm, 0-100, Vmax | ja |
| Batterie | kWh netto, Volt, Chemie (Multiselect), Zelltyp (Multiselect) | ja |
| Laden | DC kW, 10-80%, AC kW, AC opt., AC opt. EUR, V2L, V2L kW, P&C, Routenpl. | ja |
| Reichweite | WLTP kWh (min/max), WLTP km (min/max), Real kWh (min/max), Winter km | ja |
| Abmessungen | Laenge, Breite, Br. Spiegel, Hoehe, Radstand, Bodenfreiheit, Sitze, Felgen, Display, Wendekreis, cw, Dachlast, HUD | ja |
| Specs | Gewicht, Kofferraum, Koff. max, Frunk, Frunk L, AHK, Koff. el., OTA, WP, Fahrger. dB, Fahrger. @km/h | ja |
| Kosten | Preis EUR, Versich. (min/max), Gar.rel., Wartungsintervall, Wartung EUR (min/max), Anfaellige Punkte, AHK EUR | ja |
| LKW | Nutzlast kg, Achsen, Achskonf., Laderaum m3, Fuehrerhaus, kWh/tkm | ja (bei "Alle" NICHT eingeblendet) |
| (ohne) | Prod.land, Lieferz., Bemerkung | immer sichtbar |

---

## JSON-Struktur pro Fahrzeug (eAuto.json)

```
id, modell, marke, modelljahr, plattform, autotyp, produktionsland, bild_url, lieferzeit_wochen,
user_wish, bemerkung, produkt_url,

antrieb: { konzept, anzahl_motoren, motortyp, achskonfiguration }
leistung: { systemleistung_kw, systemleistung_ps, drehmoment_nm, beschleunigung_0_100_s, hoechstgeschwindigkeit_kmh }
batterie: { brutto_kwh, netto_kwh, chemie, systemspannung_v, zelltyp, zelllieferant[],
            gewicht_kg{min,max}, garantie_jahre, garantie_km, garantie_restkapazitaet_pct }
laden:    { ac_serie_kw, ac_optional_kw, ac_optional_preis_eur, dc_max_kw, dc_10_80_min,
            ladestecker[], vorkonditionierung, v2l, v2l_leistung_kw, v2g, v2h,
            plug_and_charge, laderoutenplaner }
verbrauch: { wltp_kwh_100km{min,max}, wltp_reichweite_km{min,max},
             realverbrauch_kwh_100km{min,max}, realreichweite_sommer_stadt_km,
             realreichweite_autobahn_130_km, realreichweite_winter_km,
             effizienz_wh_km{min,max}, verbrauch_kwh_tkm }
abmessungen: { laenge_mm, breite_mm, breite_mit_spiegel_mm, hoehe_mm, radstand_mm,
               leergewicht_kg, zulaessiges_gesamtgewicht_kg, kofferraum_liter,
               kofferraum_umgeklappt_liter, kofferraum_elektrisch,
               anhaengelast_gebremst_kg, anhaengelast_ungebremst_kg, ahk_preis_eur,
               wendekreis_m, bodenfreiheit_mm, cw_wert, dachlast_kg,
               frunk, frunk_liter, ahk_verfuegbar, sitze, felgen_zoll, display, hud,
               nutzlast_kg, anzahl_achsen, laderaum_m3, fuehrerhaus }
fahrwerk:  { federung_vorne, federung_hinten, rekuperation, waermepumpe,
             fahrgeraeusch_db{wert_db, kmh} }
preis:     { basispreis_eur, stromkosten_eur_100km{min,max},
             versicherung_eur_jahr{min,max}, wertverlust_3_jahre_pct{min,max} }
garantien: { fahrzeug_jahre, fahrzeug_km, lack_jahre, durchrostung_jahre, mobilitaet_jahre }
service:   { wartungsintervall, wartungskosten_eur{min,max}, anfaellige_punkte[], garantie_relevant }
ota_updates, adac_note,
euro_ncap: { sterne, erwachsene, kinder, fussgaenger, assistenten }
reifen_serie, quellen[], datenerfassung
```

### Besondere Felder
- `laden.v2l_leistung_kw` — V2L-Ausgangsleistung in kW (float, null wenn kein V2L)
- `fahrwerk.fahrgeraeusch_db.wert_db` — Fahrgeraeusch in dB (gemessen bei 130 km/h, ADAC-Methodik); 76/120 Fahrzeuge befuellt, ~44 noch null (Trucks, chinesische Marken, neue 2025/26-Modelle)

---

## Anleitung

### Seite aufrufen (GitHub Pages)
Live: **https://tschelle.github.io/eAuto/index.html**
Laedt automatisch eAuto.json aus dem Repo. Keine Serverinstallation noetig.

### Lokal starten (mit JSON)
```bash
python3 -m http.server 9090
```
Dann im Browser: `http://localhost:9090/index.html`

### Lokal starten (ohne Server)
`index.html` doppelklicken → zeigt 23 eingebettete Fahrzeuge (mit "int"-Badge).
Alle 124: JSON-Import-Button nutzen und `eAuto.json` auswaehlen.

### Neues Fahrzeug hinzufuegen (mit Claude Code)
1. Claude Code oeffnen (App, Desktop oder claude.ai/code)
2. Diese Datei (`eAutoBrain.md`) als Kontext uebergeben
3. Prompt: **"Ergaenze die eAuto.json um [Modellname]. Dann commit und push."**
4. Claude ergaenzt alle Felder (inkl. `v2l_leistung_kw`, `fahrgeraeusch_db`), committed und pusht
5. GitHub Pages aktualisiert (~1–2 Min)

Beispiel-Prompts:
- "Ergaenze den Ferrari SF90 in die eAuto.json und pushe"
- "Fuege Lucid Air und Genesis G80 hinzu, commit und push"
- "Aktualisiere den Preis vom BMW iX1 auf 47.900 EUR und pushe"

Wichtig fuer Claude Code:
- GitHub Repo: `tschelle/eAuto` (public, GitHub Pages aktiv)
- Nur `eAuto.json` aendern — NICHT die eingebetteten Daten im HTML
- Nach Aenderung: `git add eAuto.json && git commit -m "..." && git push`

### Neues Fahrzeug manuell hinzufuegen
1. In `eAuto.json` neuen Eintrag im `fahrzeuge`-Array ergaenzen
2. `git add eAuto.json && git commit -m "Add [Modell]" && git push`
3. GitHub Pages baut automatisch neu (~1–2 Min)

### Daten weitergeben
- **GitHub Pages-Link**: https://tschelle.github.io/eAuto/index.html
- **CSV-Export**: Button exportiert aktuelle gefilterte Ansicht
- **JSON-Export**: Button exportiert aktuelle gefilterte Ansicht als JSON

---

## GitHub Repository

- **Repo:** https://github.com/tschelle/eAuto
- **Live:** https://tschelle.github.io/eAuto/index.html
- **Branch:** main
- **Auto-Deploy:** GitHub Actions (pages.yml) — bei jedem Push auf main, ~1–2 Min

---

## Changelog

| Datum | Aenderung |
|---|---|
| 2026-07-18 | Mercedes EQC 400 4MATIC (000124) hinzugefuegt — eingestellt 2023, Nachfolger GLC EQ |
| 2026-07-18 | Start.html aktualisiert: 124 Fahrzeuge, 70+ Merkmale, Stand Juli 2026 |
| 2026-07-18 | Denza Z (000123) hinzugefuegt — Supersportwagen, 1.604 PS, 1.500 kW Laden |
| 2026-07-18 | Aktualitaetspruefung Juli 2026: 20 Eintraege aktualisiert (Status, Preise, Hinweise) |
| 2026-07-18 | Mercedes GLC 400 EQ 4MATIC (000122) hinzugefuegt |
| 2026-07-17 | Xpeng Mona L03 (000121) hinzugefuegt |
| 2026-07-17 | Multiselect-Filter fuer Chemie und Zelltyp (wie Segment) |
| 2026-07-17 | ⓘ Info-Icons fuer alle 70 Spaltenheader mit deutschen Beschreibungen |
| 2026-07-05 | Erststand: 120 Fahrzeuge, alle Features |

---

## Naechste Schritte
- Fahrgeraeusch-Werte fuer ~44 Fahrzeuge nachrecherchieren (Trucks, chinesische Marken, neue 2025/26-Modelle)
- V2L-Leistungen fuer neuere Fahrzeuge pruefen und ergaenzen
- Eingabeformular in HTML einbauen (Fahrzeug direkt im Browser hinzufuegen)
- Vergleichsmodus (2–3 Autos nebeneinander, Unterschiede farblich)
- Farbkodierung bei Zahlenwerten (gruen=gut, rot=schlecht)

---

## Hinweise
- Datenquelle: oeffentlich verfuegbare Spezifikationen, Hersteller-Konfiguratoren, Testberichte
- ADAC-Noten und Euro NCAP-Werte basieren auf Wissensstand 2026 — bei Kaufinteresse auf adac.de / euroncap.com gegenpruefen
- Varianten (z.B. eDrive20 vs. xDrive30) als separate Zeilen im JSON
- Alle Preise brutto, Stand Deutschland
- Produkt-URLs verlinken auf Hersteller-Modellseiten (koennen sich aendern)
- Bild-URLs verlinken auf Wikipedia-Artikel mit Fahrzeugfotos (stabile Links)
- **LKW-Felder** bei PKW/Campervans mit "n/a" befuellt; LKW-Rubrik wird bei "Alle" nicht eingeblendet
- **eLKW-Preise** sind Listenpreise in EUR; Tesla Semi Preis in USD umgerechnet
- **eAuto_intern.json** ist in `.gitignore` und wird NICHT gepusht
