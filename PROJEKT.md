# BrainDump

Eine vollständig eigenständige (standalone) HTML-Anwendung zum Erstellen, Bearbeiten und
Speichern hierarchischer Mindmaps — ohne Server, ohne Build-Prozess, ohne externe
Abhängigkeiten. Die gesamte Anwendung (HTML, CSS, JavaScript) lebt in einer einzigen Datei:
[`mindmap.html`](mindmap.html). Sie lässt sich direkt per Doppelklick im Browser öffnen.

## Ziel

Items (Knoten) frei anlegen, bearbeiten, hierarchisch miteinander verbinden und den
gesamten Baum als Markdown-Datei exportieren bzw. aus einer Markdown-Datei wieder
importieren — als einfaches, versionierbares (git-diff-freundliches) Speicherformat.
Für den verlustfreien, vollständigen Zustand (inkl. manueller Knotengrößen) steht
zusätzlich ein JSON-Speicherformat sowie ein PNG-Export der aktuellen Ansicht zur
Verfügung.

## Eigenschaften

- **Standalone**: eine einzelne `.html`-Datei, keine CDN-Links, keine Installation.
- **Automatisches Baum-Layout**: Knoten werden horizontal nach Tiefe angeordnet und
  vertikal automatisch so gestapelt, dass sich Geschwister- und Eltern-Knoten nicht
  überlappen (klassische Tree-/Org-Chart-Optik).
- **Mehrere Wurzeln**: es können beliebig viele unabhängige Themenbäume gleichzeitig in
  einer Mindmap existieren.
- **Jederzeit bearbeitbar**: Klick in ein Item aktiviert direkte Inline-Bearbeitung
  (`contenteditable`).
- **Größenveränderbar**: jeder Knoten lässt sich über den Griff an der unteren rechten
  Ecke (natives CSS-`resize`) individuell breiter/höher ziehen. Die manuell gesetzte
  Größe wird im Datenmodell gemerkt (Autosave sowie JSON-Export) und das Baum-Layout
  ordnet sich automatisch drumherum neu an.
- **Größe fixieren**: der ⤢/🔒-Schalter in der schwebenden Node-Toolbar friert Breite
  und Höhe eines Items auf ihrem aktuellen Wert ein (`node.fixed`) — der Resize-Griff
  wird deaktiviert (`resize:none`) und die Box wächst nicht mehr automatisch mit dem
  Inhalt mit (Überlauf wird gescrollt statt die Box zu vergrößern). Erneutes Umschalten
  gibt die Größe wieder frei (`resize:both`), ohne dass die zuletzt fixierte Höhe als
  Untergrenze zurückbleibt, damit sich das Item danach wieder beliebig verkleinern lässt.
  Der Zustand wird im JSON-Export mitgesichert und bleibt auch im HTML-Export sowie über
  Ein-/Ausklappen von Vorfahren hinweg erhalten.
- **JSON-Speichern/-Import**: „💾 Speichern (JSON)“ sichert den kompletten Zustand
  verlustfrei (Text, Hierarchie, Knotengröße, Ein-/Ausklapp-Status) als `.json`. Der
  „📂 Import“-Button akzeptiert sowohl `.md`- als auch `.json`-Dateien und erkennt beim
  Öffnen automatisch, um welches Format es sich handelt (Dateiendung + Inhaltscheck) —
  eine manuelle Auswahl entfällt.
- **Graphik-Export**: „🖼 Graphik speichern“ zeichnet die aktuelle Mindmap (Boxen,
  Text, Verbindungslinien, im aktuell aktiven Theme) direkt aus den Layout-Daten auf
  ein `<canvas>` und speichert sie als `.png` — ohne externe Bibliotheken, ohne
  Screenshot des DOM.
- **Hierarchische Verbindungen**:
  - über die schwebende Aktions-Toolbar (＋ Kind / ⏎ Geschwister / × löschen), die beim
    Hover bzw. bei Auswahl außerhalb des Knotenrahmens unten rechts eingeblendet wird,
  - über Tastatur (`Tab` = einrücken/Kind, `Umschalt+Tab` = ausrücken, `Enter` = neues
    Geschwister-Item),
  - oder per Drag & Drop: ein Knoten wird auf einen anderen gezogen, um ihn dort als
    Kind einzuhängen (inkl. Schutz gegen das Verschieben eines Knotens in seinen eigenen
    Nachfahren).
- **Löschen ohne Kollateralschaden**: wird ein Knoten gelöscht, werden seine Kinder
  nicht mitgelöscht, sondern rücken eine Ebene nach oben (an die Stelle des gelöschten
  Knotens, direkt unter dessen ehemaligen Eltern bzw. als neue Wurzeln, falls der
  gelöschte Knoten selbst eine Wurzel war).
- **Zoom & Pan**: Mausrad + Strg/Cmd zum Zoomen, Ziehen auf leerer Fläche zum Verschieben
  der Ansicht.
- **Alles einpassen**: der ⛶-Button in den Zoom-Controls berechnet die Bounding-Box
  aller aktuell sichtbaren (gefilterten) Knoten und setzt Zoomstufe sowie Position so,
  dass der komplette Baum ins Fenster passt — ein Klick, um sich nicht mehr auf der
  Fläche zu verlieren.
- **Mini-Karte**: unten links im Viewport zeigt eine kleine Übersicht den gesamten
  Baum verkleinert an (Knoten als farbige Punkte, eingefärbt nach zugewiesener
  Item-Farbe), dazu ein Rahmen für den aktuell sichtbaren Ausschnitt. Ein Klick auf
  die Mini-Karte verschiebt die Ansicht an die geklickte Stelle (Zoomstufe bleibt
  erhalten). Die Mini-Karte berücksichtigt automatisch einen aktiven Farb-/Tag-Filter
  und blendet sich aus, solange keine Items vorhanden sind. In der Haupt-App lässt
  sie sich zusätzlich über den 🗺-Schalter links neben dem Dark-/Hellmodus-Umschalter
  komplett ein-/ausblenden (Zustand wird im `localStorage` gemerkt). Dieselbe
  Mini-Karte (ohne den Ein-/Ausblenden-Schalter) ist auch in der über
  „🌐 HTML-Export“ erzeugten eigenständigen Ansicht enthalten.
- **Ein-/Ausklappen**: Teilbäume lassen sich über das Dreieck-Symbol am Knoten
  kollabieren.
- **Rückgängig**: die letzte Aktion (Anlegen, Löschen, Verschieben, Ein-/Ausrücken,
  Text-Änderung, Import, Alles-löschen) lässt sich über `Strg`/`Cmd`+`Z` oder den
  Toolbar-Button „↶ Rückgängig“ zurücknehmen (Snapshot-Stack, mehrstufig).
- **Autosave**: der aktuelle Stand wird laufend im `localStorage` des Browsers
  gesichert, damit ein versehentliches Neuladen der Seite keine Daten verliert. Die
  eigentliche, dateibasierte Sicherung erfolgt bewusst separat über JSON- (vollständig)
  oder Markdown-Export (portabel, nur Hierarchie).
- **Speicher-Auslastungsanzeige**: unten in den Zoom-Controls zeigt ein kleines
  eingebettetes SVG-Icon (Pfeil in Ablage, per `currentColor` eingefärbt) plus „xx %“
  die geschätzte Auslastung des für Autosave genutzten `localStorage`-Kontingents —
  Icon und Text wechseln gemeinsam ab 70 % auf Orange, ab 90 % auf Rot, da beide
  dieselbe `color`/`currentColor`-Kette nutzen. Da es keine verlässliche Browser-API
  für das exakte `localStorage`-Kontingent gibt, wird es beim Start einmalig empirisch
  ermittelt (Testschreibversuche mit wachsenden Dummy-Strings, binäre Suche, bis
  `QuotaExceededError` auftritt) — verzögert nach dem ersten Rendern, damit die UI
  dadurch nicht blockiert. Es ist bewusst eine **Annäherung**, keine exakte oder
  dauerhaft gültige Zahl: Sie kann je nach Browser, `file://`-Speicherbesonderheiten
  und bei mehreren gleichzeitig geöffneten Tabs (gemeinsames Kontingent pro Origin)
  abweichen. Schlägt ein tatsächlicher Autosave-Versuch fehl (Kontingent
  überschritten), erscheint statt der Prozentzahl „💾 ⚠“ mit Hinweis, manuell über
  „💾 Speichern (JSON)“ zu sichern.
- **Dark-/Hellmodus-Switch**: Button oben rechts in der Toolbar mit eingebettetem
  SVG-Icon (zweigeteilter Kreis), das sich beim Umschalten um 180° dreht. Ohne manuelle
  Wahl folgt die Optik der Systemeinstellung (`prefers-color-scheme`); ein Klick setzt
  eine explizite Wahl per `data-theme`-Attribut, die im `localStorage` gemerkt wird und
  die Systemeinstellung dauerhaft überschreibt, bis erneut umgeschaltet wird.
- **Farbe pro Item**: Rechtsklick auf einen Knoten öffnet ein Kontextmenü mit acht
  Palette-Farben (+ „Keine Farbe“). Die Farbe wird als kleiner Punkt am Knoten
  angezeigt und ist Teil des Datenmodells (Autosave, JSON-Export/-Import).
- **Farbfilter**: der Toolbar-Button „🎨 Farbfilter“ öffnet ein Dropdown mit allen
  aktuell verwendeten Farben (Mehrfachauswahl per Checkbox), inkl. eines eigenen
  „Keine Farbe“-Eintrags (gestrichelter Kreis) für Knoten ohne zugewiesene Farbe, falls
  solche existieren. Ist ein Filter aktiv, werden nicht passende Knoten komplett
  ausgeblendet und der Baum kompaktiert sich automatisch — sind zwei gleichfarbige
  Knoten nur über ausgeblendete Zwischenknoten verbunden, wird die Verbindung
  gestrichelt direkt zwischen ihnen gezeichnet. „Alle anzeigen“ setzt den Filter
  zurück. Markdown- und PNG-Export berücksichtigen den aktiven Filter automatisch
  (kompaktierte Hierarchie bzw. gestrichelte Kanten im Bild); der JSON-Export bleibt
  davon unberührt und sichert immer den vollständigen, ungefilterten Zustand.
- **Tags**: jedes Item kann beliebig viele Tags tragen — ein Tag ist ein einzelnes Wort
  ohne führendes Leerzeichen (Buchstaben/Ziffern/Unterstrich/Bindestrich), das im
  Datenmodell ohne das `#` gespeichert wird. Erfassung über den `#`-Schalter in der
  schwebenden Node-Toolbar: ein Popup zeigt vorhandene Tags als entfernbare Chips und
  ein Textfeld zum Hinzufügen (Eintippen + `Enter`, führende(s) `#` wird toleriert und
  entfernt, Duplikate werden ignoriert). Am Knoten selbst werden die Tags als kleine
  Chip-Reihe unterhalb des Texts angezeigt. Tags sind Teil jeder Auswertung: Autosave,
  JSON-Export (`tags`-Array), Markdown-Export/-Import (siehe unten), HTML-Export
  (Chips wie im Editor), Graphik-Export (an den Text angehängt) sowie Excel-Export
  (eigene Spalte).
- **Tag-Filter**: der Toolbar-Button „🏷 Tags“ öffnet ein Dropdown mit allen aktuell
  verwendeten Tags (Mehrfachauswahl per Checkbox, alphabetisch sortiert), inkl. eines
  eigenen „Ohne Tags“-Eintrags für Items ganz ohne Tags, falls solche existieren. Ein
  Knoten ist sichtbar, wenn er mindestens einen der ausgewählten Tags trägt (ODER
  innerhalb der Tag-Auswahl). Sind Farb- **und** Tag-Filter gleichzeitig aktiv, muss ein
  Knoten beide Bedingungen erfüllen (UND zwischen den beiden Filter-Dimensionen) —
  technisch ist das eine einzige gemeinsame `nodeMatchesFilter()`-Prüfung, die dieselbe
  Baum-Kompaktierung/gestrichelte-Kanten-Logik des Farbfilters mitnutzt. „Alle anzeigen“
  setzt nur den Tag-Filter zurück. Wie beim Farbfilter berücksichtigen Markdown-, PNG-,
  HTML- und Excel-Export den aktiven Filter automatisch; der JSON-Export bleibt bewusst
  ungefiltert (vollständiges Backup).
- **Legende einfügen**: der Toolbar-Button „📋 Legende einfügen“ öffnet ein Modal mit
  einem Textfeld je aktuell verwendeter Farbe und erzeugt daraus einen einzelnen neuen
  Wurzelknoten (Kopfzeile `Legende`, eine Leerzeile, dann je Farbe eine Zeile
  „Farbe: Beschreibung“). Der Knoten
  ist über `node.isLegend` markiert (Teil von Autosave/JSON-Export); solange irgendwo
  im Baum ein solcher Legende-Knoten existiert, ist der Button deaktiviert — wird der
  Knoten gelöscht, reaktiviert er sich automatisch beim nächsten Rendern.
- **HTML-Export**: „🌐 HTML-Export“ erzeugt eine separate, eigenständige `.html`-Datei
  mit nur Titel, Dark/Light-Umschalter und dem Baum gemäß aktuell aktivem Farb-/
  Tag-Filter. Die Ansicht ist rein lesend (kein Editieren, kein Umhängen), bietet aber
  Zoom, Pan, „Alles einpassen“, Mini-Karte und klickbare Ein-/Ausklapp-Dreiecke —
  gedacht zum Weitergeben einer fixierten,
  interaktiven Ansicht ohne die volle Editier-Funktionalität. Items mit fixierter
  Breite/Höhe (🔒) behalten ihre exakte Größe auch in dieser Ansicht bei, insbesondere
  über Ein-/Ausklappen von Vorfahren hinweg (Kinder werden dabei aus dem DOM entfernt
  und beim Ausklappen neu erzeugt — die gespeicherte Größe wird dabei erneut angewendet).
- **Excel-Export**: „📊 Excel Export“ erzeugt eine echte `.xlsx`-Datei mit den Spalten
  „Gliederung“ (verschachtelte Nummerierung wie beim `1.`-Markdown-Format, z. B. `1.2.1`),
  „Item“ (Knotentext), „Farbe“ (Farbname als Text sowie als Zellhintergrund in der
  jeweiligen Palette-Farbe) und „Tags“ (kommagetrennt mit `#`-Präfix, z. B.
  `#einkauf, #dringend`). Die Zeilenreihenfolge und ein aktiver Farbfilter folgen
  derselben Display-Tree-Logik wie beim Markdown- und HTML-Export. Da keine externe
  Bibliothek eingebunden werden darf, schreibt `generateXlsx()` das OOXML-Paket
  (`[Content_Types].xml`, `_rels`, `workbook.xml`, `styles.xml`, `sheet1.xml`) sowie
  einen minimalen, unkomprimierten (STORED) ZIP-Container inkl. CRC32 selbst.

## Datenformat: Markdown (portabel) vs. JSON (vollständig)

Der JSON-Export (`💾 Speichern (JSON)`) speichert die Rohdaten 1:1 (`{type, version,
roots}`, mit `text`/`collapsed`/`width`/`height`/`color`/`fixed`/`isLegend`/`tags`/
`children` pro Knoten, ohne IDs — die werden beim Import frisch vergeben). Er dient der
verlustfreien Sicherung inkl. Größen, Fixierung, Farben und Tags, unabhängig vom aktiven
Farbfilter, ist aber kein für Menschen gedachtes Austauschformat.

Der Markdown-Export bleibt das portable, lesbare Format für die reine Hierarchie (ohne
Größen). Beim Export („💾 Export (.md)“) fragt ein Auswahlfenster, in welchem von vier Formaten
die Hierarchie dargestellt werden soll:

| Format | Beispiel | Bezeichnung |
|---|---|---|
| `-` | `- A` / `-- B` / `--- C` | ein Bindestrich mehr pro Ebene |
| `#` | `# A` / `## B` / `### C` | eine Raute mehr pro Ebene |
| `1.` | `1. A` / `1.1. B` / `1.1.1. C` | verschachtelte Gliederungsnummerierung, pro Geschwister hochgezählt |
| `(ohne)` | nur Einrückung, kein Symbol | zwei Leerzeichen Einrückung pro Ebene |

Beispiel im `-`-Format:

```markdown
- Projekt Website-Relaunch
-- Design
--- Farbschema
--- Typografie
-- Entwicklung
--- Frontend
--- Backend
- Zweites, unabhängiges Thema
-- Unterpunkt A
```

Trägt ein Item Tags, wird an die jeweilige Zeile eine abschließende Gruppe
`[#tag1 #tag2]` angehängt (in allen vier Formaten gleich):

```markdown
- Milch kaufen [#einkauf #dringend]
```

Diese Gruppe ist immer und ausschließlich als Tag-Liste zu verstehen — eine
abschließende `[...]`-Gruppe, deren Tokens alle mit `#` beginnen, wird beim Import
(`parseMarkdown()`) unabhängig vom erkannten Format vom Itemtext abgetrennt und als
`tags` übernommen. Markdown ist damit für Tags (nicht aber für Größen/Farben)
ebenfalls round-trip-fähig.

Der Import (`parseMarkdown()`) erkennt automatisch anhand der ersten inhaltstragenden
Zeile, welches der vier Formate vorliegt, und parst die Datei entsprechend — es muss
beim Import nichts ausgewählt werden. Zusätzlich bleibt eine klassische, handgeschrieben
verschachtelte Markdown-Liste (Einrückung + einzelnes `-`/`*`/`+` pro Zeile, z. B. aus
einem Texteditor) importierbar: Enthält keine Zeile mehrere gleiche Aufzählungszeichen
hintereinander, wertet der Parser stattdessen die Einrückungstiefe aus. Zeilen ohne
erkennbare Struktur werden ignoriert (außer im `(ohne)`-Format, wo jede nicht-leere
Zeile als Item gilt). Beim Import wird gefragt, ob die importierten Wurzeln zur
bestehenden Mindmap hinzugefügt oder die aktuelle Mindmap komplett ersetzt werden soll.

## Architektur (in `mindmap.html`)

Die Datei ist bewusst in drei Blöcke gegliedert:

1. **`<style>`** — Layout/Optik, inkl. automatischer Hell-/Dunkel-Darstellung über
   `prefers-color-scheme`.
2. **HTML-Grundgerüst** — Toolbar, Viewport/Canvas-Container, SVG-Layer für
   Verbindungslinien, Node-Container, Export-Format-Auswahlfenster (`#export-modal`),
   Farbfilter-Dropdown (`#color-filter-menu`) und Node-Farbmenü (`#node-color-menu`),
   verstecktes `<input type="file">` für den Import.
3. **`<script>`** — reines, abhängigkeitsfreies JavaScript (IIFE), gegliedert in:
   - **Datenmodell**: Knoten als `{id, text, collapsed, width, height, color, fixed,
     isLegend, tags[], children[]}` (`color` = `null` oder ein Key aus `COLOR_PALETTE`;
     `fixed` = true, wenn Breite/Höhe über den 🔒-Schalter eingefroren sind; `isLegend` =
     true für den über „📋 Legende einfügen“ erzeugten Legende-Knoten; `tags` = Array von
     Tag-Wörtern ohne führendes `#`, verwaltet über `normalizeTagWord()`/
     `sanitizeTagsArray()`), `roots[]` als Array unabhängiger Bäume.
   - **Farb-/Tag-Filter & Display-Tree**: `getDisplayRoots()` liefert je nach
     `isFilterActive()` (aktiv, sobald Farb- **oder** Tag-Filter gesetzt ist) entweder
     den echten Baum 1:1 (`buildDisplayEntry()`) oder eine kompaktierte Sicht
     (`collectVisibleEntries()`), in der nicht passende Knoten übersprungen werden und
     ihre sichtbaren Nachfahren mit `viaHidden:true` markiert zum nächsten sichtbaren
     Vorfahren aufrücken. Ob ein Knoten passt, entscheidet die gemeinsame
     `nodeMatchesFilter()`: Farbe und Tags werden als unabhängige Dimensionen mit UND
     verknüpft, mehrere ausgewählte Farben bzw. Tags innerhalb einer Dimension mit ODER
     (`activeColorFilter`/`activeTagFilter`, je ein Set-artiges Objekt; `getUsedColors()`/
     `getUsedTags()` liefern die Dropdown-Einträge inkl. Zählung und den Sentineln
     `NO_COLOR_KEY`/`NO_TAG_KEY`). Rendering (`render()`), Layout (`computeLayout()`) und
     Kanten (`drawEdges()`) arbeiten durchgängig auf dieser Display-Tree-Sicht statt
     direkt auf `roots`, `viaHidden` steuert die gestrichelte Kantendarstellung
     (`.edge-skip`).
   - **Persistenz**: `autosave()`/`restore()` (localStorage), `exportToMarkdown(format)`/
     `parseMarkdown()` (vier Markdown-Formate, Auto-Erkennung beim Import) sowie
     `serializeToJSON()`/`parseJSON()` (vollständiger, verlustfreier Zustand inkl.
     Größe, Fixierung und Tags). Der Import-Handler unterscheidet JSON vs. Markdown anhand
     Dateiendung/Inhalt und übergibt beides an dieselbe `applyImportedRoots()`-Merge-Logik.
   - **Graphik-Export**: `exportImage()` zeichnet Knoten (`roundRectPath()`, manueller
     Textumbruch via `wrapCanvasText()`, Farbmarkierung, Tags als `[#tag1 #tag2]` an den
     Text angehängt über `tagSuffixForExport()`) und Bézier-Kanten (gestrichelt
     bei `viaHidden`) anhand der zuletzt berechneten Layout-Positionen (`lastPositions`)
     und der zuletzt gerenderten Display-Tree-Sicht (`lastDisplayRoots`) auf ein
     Offscreen-`<canvas>` (2-fach-Auflösung) und exportiert es als PNG.
   - **HTML-Ansicht-Export**: `generateStandaloneHtml()` baut aus der aktuellen
     Display-Tree-Sicht (`exportEntryToPlainViewNode()`, inkl. `tags`) eine vollständig
     eigenständige zweite HTML-Datei (eigenes, stark reduziertes CSS/JS als
     String-Template) mit nur Theme-Umschalter, Zoom/Pan, „Alles einpassen“, Mini-Karte,
     klickbarem Ein-/Ausklappen und Tag-Chips je Knoten — ohne Editier- oder
     Umhänge-Funktionalität. Layout/Zoom/Minimap-Code ist dort eine eigene, reduzierte
     Kopie (kein gemeinsames Runtime-Modul), da die exportierte Datei bewusst
     abhängigkeitsfrei und ohne Bezug zur Haupt-App bleiben soll.
   - **Excel-Export**: `generateXlsx()` sammelt über `collectExcelRows()` (Display-Tree,
     analog `exportToMarkdown("numbered")`) Gliederung/Text/Farbe/Tags pro Zeile und baut
     daraus die OOXML-Teile eines Spreadsheets (`sheetXml`, `stylesXml` inkl. einem
     `cellXfs`-Eintrag je Palette-Farbe, `workbookXml`, Rels, Content-Types). `buildZip()`
     verpackt diese Teile ohne Kompression (STORED) samt selbst berechneter CRC32-Prüfsummen
     zu einem gültigen `.xlsx`-Container — bewusst ohne externe Bibliothek (kein SheetJS),
     damit die Datei standalone bleibt.
   - **Layout-Engine**: `computeLayout()` misst die tatsächliche Größe der gerenderten
     DOM-Knoten und berechnet daraus rekursiv Spalten- (Tiefe) und Zeilenpositionen
     (Tidy-Tree-Prinzip: Elternknoten wird vertikal auf die Mitte seiner Kinder
     zentriert).
   - **Rendering**: `render()` synchronisiert das Datenmodell mit dem DOM (Diffing über
     vorhandene Elemente, kein vollständiges Neuaufbauen), `drawEdges()` zeichnet die
     Bézier-Verbindungslinien im SVG-Layer.
   - **Interaktionen**: Knoten hinzufügen/löschen/verschieben, Inline-Editing,
     Tastaturkürzel, Drag-&-Drop-Reparenting, Ein-/Ausklappen, schwebende Node-Toolbar
     (per Hover/Selektion positioniert, kein Bestandteil des Knoten-DOM selbst — damit
     sie nicht vom `overflow:auto` des größenveränderbaren Knotens abgeschnitten wird),
     `toggleFixedSize()` für den ⤢/🔒-Schalter. Der `ResizeObserver` pro Knoten
     (`bindNodeEvents()`) ignoriert Messungen an bereits aus dem DOM entfernten Elementen
     (`!el.isConnected`) — sonst würde ein durch den Farbfilter ausgeblendeter Knoten
     beim Entfernen ein letztes Mal mit 0×0 gemessen und `width`/`height` im
     Datenmodell überschreiben.
   - **Undo**: `snapshot()`/`pushUndo()`/`undo()` verwalten einen Stack aus
     JSON-Snapshots von `roots` (max. 50 Schritte); vor jeder strukturellen Änderung
     sowie vor abgeschlossenen Textbearbeitungen (Diff bei `blur`) wird ein Snapshot
     abgelegt.
   - **Zoom & Pan**: einfache CSS-`transform`-basierte Ansicht-Transformation.
     `fitToView()` berechnet die Bounding-Box aller aktuell sichtbaren Knoten aus
     `lastPositions` und setzt Zoomstufe/Position so, dass alles ins Viewport passt
     (respektiert damit automatisch einen aktiven Farb-/Tag-Filter, da `lastPositions`
     nur die gefilterten Knoten enthält).
   - **Mini-Karte**: `renderMinimap()` zeichnet die verkleinerten Knotenpositionen
     (farbig nach `node.color`) auf ein `<canvas>`, `minimapTransform` (Skalierung +
     Versatz) wird dabei zwischengespeichert; `updateMinimapViewportRect()` nutzt diesen
     Wert, um bei reinem Zoomen/Verschieben (`applyView()`) nur das Sichtbereich-
     Rechteck zu repositionieren, ohne die Punkte neu zu zeichnen. Ein Klick auf die
     Mini-Karte setzt `view.x`/`view.y` so, dass die geklickte Weltkoordinate zentriert
     wird (Zoomstufe bleibt unverändert). `minimapVisible` (aus `localStorage`,
     `#btn-minimap-toggle`) blendet die Mini-Karte per Inline-Style ein/aus —
     unabhängig von der `"empty"`-Klasse, die sie bei fehlenden Items ohnehin
     ausblendet. Dieser Ein-/Ausblenden-Schalter existiert nur in der Haupt-App,
     nicht im HTML-Export-Template.

Es gibt bewusst keine externen Bibliotheken (kein D3, kein React) und keinen
Build-Schritt — die Datei funktioniert offline und lässt sich 1:1 kopieren oder
versionieren.

## Nutzung

1. `mindmap.html` im Browser öffnen.
2. Über „＋ Neue Wurzel“ ein erstes Thema anlegen, Text eintippen.
3. Mit der schwebenden Node-Toolbar oder Tastatur (`Tab`/`Enter`) den Baum ausbauen.
4. Über „💾 Speichern (JSON)“ den vollständigen Zustand sichern (inkl. Größen und Tags)
   oder über „💾 Export (.md)“ die Hierarchie inkl. Tags (nicht aber Größen/Farben) als
   portables Markdown exportieren.
5. Über „📂 Import“ eine zuvor gesicherte `.json`- oder `.md`-Datei laden (Format wird
   automatisch erkannt).
6. Über „🖼 Graphik speichern“ die aktuelle Ansicht als `.png` sichern.
7. Rechtsklick auf ein Item weist eine Farbe zu; über „🎨 Farbfilter“ nach einer oder
   mehreren Farben (oder „Keine Farbe“) filtern. Über „📋 Legende einfügen“ einen
   Legende-Knoten mit einer Beschreibung je verwendeter Farbe erzeugen.
8. Über den ⤢/🔒-Schalter in der schwebenden Node-Toolbar die aktuelle Breite/Höhe
   eines Items fixieren bzw. wieder freigeben.
9. Über den `#`-Schalter in der schwebenden Node-Toolbar beliebig viele Tags an ein
   Item vergeben bzw. wieder entfernen. Über „🏷 Tags“ in der Toolbar nach einem oder
   mehreren Tags (oder „Ohne Tags“) filtern — kombinierbar mit dem Farbfilter.
10. Über „🌐 HTML-Export“ die aktuelle (gefilterte) Ansicht als eigenständige,
    rein lesende `.html`-Datei zum Weitergeben exportieren.
11. Über „📊 Excel Export“ die aktuelle (gefilterte) Ansicht als `.xlsx`-Datei mit
    Gliederung, Item-Text, Farbe (Text + Zellhintergrund) und Tags exportieren.
12. Zur Orientierung auf großen Flächen: über den ⛶-Button in den Zoom-Controls den
    kompletten Baum ins Fenster einpassen, oder über die Mini-Karte unten links
    (Klick springt an die gewählte Stelle) navigieren. Über den 🗺-Schalter links
    neben dem Dark-/Hellmodus-Umschalter lässt sich die Mini-Karte bei Bedarf
    komplett ausblenden.

## Bekannte Grenzen

- Das Layout ist ein einfacher, deterministischer Tidy-Tree-Algorithmus — für sehr
  große Mindmaps (mehrere hundert Knoten) kann die Darstellung unübersichtlich werden.
- Knotentext wird beim Export auf eine Zeile normalisiert (Zeilenumbrüche werden zu
  Leerzeichen), damit die Markdown-Listenstruktur gültig bleibt.
- Es gibt nur Undo, kein Redo; der Undo-Stack ist auf 50 Schritte begrenzt und wird
  beim Neuladen der Seite verworfen (nicht Teil des Autosave).
- Manuell gesetzte Knotengrößen und Farben werden bewusst nicht in den Markdown-Export
  übernommen (der bildet nur Text/Hierarchie/Tags ab, bleibt dadurch portabel/lesbar) —
  für die vollständige, größen- und farberhaltende Sicherung den JSON-Export verwenden.
- Aktuell lässt sich pro Item nur eine Farbe vergeben (keine Mehrfachauswahl); Tags
  hingegen sind explizit als Mehrfachauswahl pro Item gedacht.
- Der Graphik-Export ist ein bewusst einfacher Nachbau (Boxen + Text + Kanten) und
  keine 1:1-Kopie des DOM; Knoten-Icons (Toggle-Dreieck, Kinderzahl-Badge) werden
  darin nicht mitgezeichnet.
