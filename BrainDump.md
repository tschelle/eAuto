# BrainDump

Eine vollständig eigenständige (standalone) HTML-Anwendung zum Erstellen, Bearbeiten und
Speichern hierarchischer Mindmaps — ohne Server, ohne Build-Prozess, ohne externe
Abhängigkeiten. Die gesamte Anwendung (HTML, CSS, JavaScript) lebt in einer einzigen Datei:
[`BrainDump.html`](BrainDump.html). Sie lässt sich direkt per Doppelklick im Browser öffnen.

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
- **Klickbare Links**: `http(s)`- und `file`-URLs im Itemtext werden erkannt und —
  solange das Item gerade **nicht** bearbeitet wird — als klickbarer Link dargestellt
  (`linkifyText()`; Cmd/Ctrl+Klick öffnet, ein normaler Klick startet wie gewohnt die
  Bearbeitung). Sobald das Item fokussiert wird, erscheint wieder der reine Klartext
  (inkl. der rohen URL) — Links existieren bewusst nur im Anzeigemodus, um
  contenteditable-Eigenheiten bei eingebetteten `<a>`-Tags (Cursor-Verhalten an
  Link-Rändern, veraltendes `href` beim Editieren) zu vermeiden. Da Browser (z. B.
  WebKit/Safari) das native Navigieren von Links *innerhalb* eines
  contenteditable-Bereichs grundsätzlich unterdrücken, wird der Link bei Cmd/Ctrl+Klick
  nicht über die Standardaktion des `<a>` geöffnet, sondern wie bei `downloadFile()`:
  ein echtes, temporäres `<a>` außerhalb des contenteditable-Bereichs wird erzeugt und
  per `.click()` ausgelöst — das trägt die vollen Rechte eines regulären Links-Klicks,
  unabhängig vom URL-Schema (`window.open()` wird bewusst nicht verwendet, da manche
  Browser das für `file://`-Ziele strenger behandeln als für `https://`). Das
  Datenmodell speichert weiterhin nur den rohen Text — die Verlinkung ist reine
  Anzeigelogik ohne Auswirkung auf Autosave/JSON-/Markdown-Export. Dieselbe Erkennung
  ist auch im HTML-Export enthalten (dort immer aktiv, da rein lesend, mit ganz
  normalem Link-Klick ohne Modifier-Taste).
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
- **JSON-Speichern/-Import**: „Speichern (JSON)“ sichert den kompletten Zustand
  verlustfrei (Text, Hierarchie, Knotengröße, Ein-/Ausklapp-Status) als `.json`. Der
  „Import“-Button akzeptiert `.md`-, `.json`- und `.mmap`-Dateien und erkennt beim
  Öffnen automatisch, um welches Format es sich handelt (Dateiendung + Inhaltscheck) —
  eine manuelle Auswahl entfällt.
- **MindManager-Import (`.mmap`)**: importiert Mindmaps aus Mindjet/Corel MindManager
  (getestet mit dem ZIP-basierten Format ab MindManager 2013). Da keine externe
  Bibliothek eingebunden werden darf, liest ein selbstgeschriebener, minimaler
  ZIP-Reader die `Document.xml` aus dem Archiv, ein ebenfalls selbstgeschriebener
  DEFLATE-Dekompressor (RFC 1951) entpackt sie, und `DOMParser` liest daraus den
  Themen-Baum (`ap:Map`/`OneTopic`/`Topic`/`SubTopics`) aus. Übernommen werden Text,
  Hierarchie und Ein-/Ausklapp-Status (`Collapsed`) — das entspricht 1:1 unserem
  Knotenmodell und war im getesteten Beispiel vollständig verlustfrei. Ein
  vorhandener Hyperlink wird best-effort an den Knotentext angehängt (bleibt dank
  automatischer Link-Erkennung klickbar). Farben, Icons, Notizen, Callouts,
  Boundaries und Querverknüpfungen (Relationships) haben keine Entsprechung in
  BrainDump und gehen beim Import verloren. Ältere `.mmap`-Dateien (vor ca.
  MindManager 2013) sind reines, unkomprimiertes XML ohne ZIP-Hülle — dafür würde
  der ZIP-Reader nicht greifen, das wurde nicht getestet.
- **MindManager-Export (`.mmap`, Beta)**: „MindManager (Beta)“ (im Export-Dropdown)
  erzeugt die Gegenrichtung zum Import — ein `Document.xml`
  (`ap:Map`/`OneTopic`/`Topic`/`SubTopics`/`Text`/`Collapsed`, Element-Reihenfolge
  anhand einer echten `.mmap`-Datei verifiziert) verpackt in einem unkomprimierten
  (STORED) ZIP-Container über denselben `buildZip()`/`strToBytes()`-Code wie beim
  Excel-Export — STORED ist ein regulärer, gültiger ZIP-Modus, es war also kein
  neuer Kompressor nötig. Hat BrainDump mehrere Wurzeln, werden sie unter einem
  synthetischen Hauptthema „BrainDump“ gebündelt, da MindManager nur ein einzelnes
  Hauptthema kennt. Ein erster Versuch mit nur `OneTopic` scheiterte beim echten
  Öffnen in MindManager („Speicherfehler: Globales Element hat einen nicht
  unterstützten Typ“) — MindManager validiert gegen sein Schema und erwartet
  unter `ap:Map` zusätzlich `cor:Custom`, `ap:StyleGroup`, `ap:MapViewGroup`,
  `ap:DocumentGroup` und `ap:MarkersSetGroup` sowie `xsi:schemaLocation` am
  Root-Element. Die drei erstgenannten Gruppen (Formatierungs-Defaults,
  Standard-Icon-Sets) sind wörtlich aus einer echten MindManager-Datei
  übernommene Werksvoreinstellungen (keine Nutzerinhalte), `DocumentGroup` wird
  dynamisch mit Themen-/Wortstatistik und aktuellem Zeitstempel gefüllt. Mit
  dieser Erweiterung wurde das Öffnen in echtem MindManager erfolgreich
  verifiziert (durch den Nutzer, mit einer eigenen, umfangreichen Mindmap).
- **Graphik-Export**: „Graphik“ (im Export-Dropdown) zeichnet die aktuelle Mindmap (Boxen,
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
- **Duplizieren**: der ⧉-Schalter in der schwebenden Node-Toolbar (oder `Strg`/`Cmd`+`D`
  bei ausgewähltem Knoten) legt eine vollständige Kopie eines Items inkl. seines
  kompletten Teilbaums an — mit frischen, eindeutigen IDs, aber identischem Text,
  Größe, Farbe, Fixierung und Tags. Die Kopie wird direkt als nächstes Geschwister
  neben dem Original eingefügt und automatisch ausgewählt.
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
  „HTML“ (im Export-Dropdown) erzeugten eigenständigen Ansicht enthalten.
- **Suche**: der Toolbar-Button „Suche“ (oder `Strg`/`Cmd`+`F`, ersetzt die native
  Browser-Suche) öffnet eine Suchleiste oben im Viewport. Durchsucht wird der
  komplette Baum — Itemtext per Teilstring, oder bei einer Eingabe mit führendem `#`
  gezielt nur die Tags — unabhängig vom aktuellen Ein-/Ausklapp-Zustand und einem
  aktiven Farb-/Tag-Filter. Treffer werden am Knoten hervorgehoben (aktueller Treffer
  stärker als die übrigen), `Enter`/`Umschalt+Enter` bzw. die Pfeil-Buttons springen
  vor/zurück durch die Treffer. Beim Sprung zu einem Treffer werden eingeklappte
  Vorfahren automatisch aufgeklappt und ein aktiver Farb-/Tag-Filter, der den Treffer
  sonst ausblenden würde, automatisch zurückgesetzt — Suche findet also immer alles,
  unabhängig vom aktuellen Anzeigezustand. `Esc` bzw. der ×-Button schließen die
  Suche wieder und entfernen die Hervorhebung. Nur in der Haupt-App enthalten, nicht
  im HTML-Export.
- **Ein-/Ausklappen**: Teilbäume lassen sich über das Dreieck-Symbol am Knoten
  kollabieren.
- **Rückgängig & Wiederholen**: die letzte Aktion (Anlegen, Löschen, Duplizieren,
  Verschieben, Ein-/Ausrücken, Text-Änderung, Import, Alles-löschen) lässt sich über
  `Strg`/`Cmd`+`Z` oder den Toolbar-Button „Rückgängig“ zurücknehmen (Snapshot-Stack,
  mehrstufig). Zurückgenommene Aktionen lassen sich über `Strg`/`Cmd`+`Umschalt`+`Z`
  (bzw. `Strg`/`Cmd`+`Y`) oder den Toolbar-Button „Wiederholen“ erneut anwenden — ein
  zweiter Stack, der bei jeder neuen Aktion automatisch geleert wird (klassisches
  Undo/Redo-Verhalten: die „Zukunft“ verfällt, sobald man nach einem Rückgängig etwas
  Neues tut).
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
- **HTML-Export**: „HTML“ (im Export-Dropdown) erzeugt eine separate, eigenständige `.html`-Datei
  mit nur Titel, Dark/Light-Umschalter und dem Baum gemäß aktuell aktivem Farb-/
  Tag-Filter. Die Ansicht ist rein lesend (kein Editieren, kein Umhängen), bietet aber
  Zoom, Pan, „Alles einpassen“, Mini-Karte und klickbare Ein-/Ausklapp-Dreiecke —
  gedacht zum Weitergeben einer fixierten,
  interaktiven Ansicht ohne die volle Editier-Funktionalität. Items mit fixierter
  Breite/Höhe (🔒) behalten ihre exakte Größe auch in dieser Ansicht bei, insbesondere
  über Ein-/Ausklappen von Vorfahren hinweg (Kinder werden dabei aus dem DOM entfernt
  und beim Ausklappen neu erzeugt — die gespeicherte Größe wird dabei erneut angewendet).
- **Excel-Export**: „Excel“ (im Export-Dropdown) erzeugt eine echte `.xlsx`-Datei mit den Spalten
  „Gliederungsebene“ (die Verschachtelungstiefe als echte Zahl, beginnend bei 1 —
  z. B. Ebene 3 bei Gliederung `1.1.1`), „Gliederung“ (verschachtelte Nummerierung
  wie beim `1.`-Markdown-Format, z. B. `1.2.1`), „Item“ (Knotentext), „Farbe“
  (Farbname als Text sowie als Zellhintergrund in der jeweiligen Palette-Farbe) und
  „Tags“ (kommagetrennt mit `#`-Präfix, z. B. `#einkauf, #dringend`). Die
  Zeilenreihenfolge und ein aktiver Farbfilter folgen
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
Größen). Beim Export („MD“ im Export-Dropdown) fragt ein Auswahlfenster, in welchem von vier Formaten
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

## Architektur (in `BrainDump.html`)

Die Datei ist bewusst in drei Blöcke gegliedert:

1. **`<style>`** — Layout/Optik, inkl. automatischer Hell-/Dunkel-Darstellung über
   `prefers-color-scheme`.
2. **HTML-Grundgerüst** — Toolbar (u. a. das Export-Sammel-Dropdown
   `#export-menu-wrap`/`#export-dropdown`, das die vier Export-Buttons
   Graphik/MD/HTML/Excel unter einem einzelnen „Export“-Button bündelt — die
   jeweiligen Button-IDs und ihre Click-Handler sind dabei unverändert geblieben,
   nur die Anordnung im Toolbar-Markup hat sich geändert), Viewport/Canvas-Container,
   SVG-Layer für Verbindungslinien, Node-Container, Export-Format-Auswahlfenster
   (`#export-modal`), Farbfilter-Dropdown (`#color-filter-menu`) und Node-Farbmenü
   (`#node-color-menu`), verstecktes `<input type="file">` für den Import.
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
     Größe, Fixierung und Tags). Der Import-Handler unterscheidet `.mmap` (Datei wird
     als `ArrayBuffer` statt als Text gelesen) von JSON/Markdown anhand Dateiendung,
     JSON/Markdown weiterhin per Dateiendung/Inhalt; alle drei Pfade übergeben ihr
     Ergebnis an dieselbe `applyImportedRoots()`-Merge-Logik.
   - **MindManager-Import**: `readZipEntry()` liest die Central-Directory eines
     `.mmap`-ZIP-Archivs (EOCD rückwärts suchen, Einträge auflisten) und liefert die
     Rohbytes von `Document.xml` plus Kompressionsmethode; `inflateRaw()` ist ein
     selbstgeschriebener DEFLATE-Dekompressor (Bit-Reader LSB-first, Stored-/Fixed-/
     Dynamic-Huffman-Blöcke, kanonische Huffman-Tabellen über `buildHuffman()`,
     LZ77-Rückreferenzen byteweise wegen möglicher Overlaps); `parseMindManagerXml()`
     läuft mit `DOMParser` über den `ap:Map`/`OneTopic`/`Topic`/`SubTopics`-Baum und
     baut daraus über `createNode()` dieselben Knotenobjekte wie jeder andere Import.
   - **MindManager-Export**: `nodeToMmapTopic()` baut die Gegenrichtung — pro Knoten
     ein `<ap:Topic>` mit zufälliger `OId` (`randomOId()`: 16 Zufallsbytes über
     `crypto.getRandomValues()`, base64-kodiert) in der beim Import verifizierten
     Kindelement-Reihenfolge (`SubTopics` vor `TopicViewGroup` vor `Text`;
     `ap:Collapsed` nur bei Knoten mit Kindern, analog zum realen Format).
     `generateMmap()` bündelt mehrere BrainDump-Wurzeln unter einem synthetischen
     Hauptthema und übergibt das fertige `Document.xml` an den bestehenden
     `buildZip()` (unkomprimiert/STORED, kein neuer Kompressor nötig).
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
   - **Link-Erkennung**: `linkifyText()` escaped den Text (`xmlEscape()`) und verpackt
     erkannte `http(s)`- und `file`-URLs (`URL_RE`) in
     `<a target="_blank" rel="noopener noreferrer">`, inkl. Abtrennen typischer
     Satzzeichen am Ende (`.`, `)`, `,` …). `render()` setzt das Ergebnis nur als
     `innerHTML`, wenn der Knoten nicht `editingId` entspricht; der `focus`-Handler in
     `bindNodeEvents()` setzt beim Bearbeitungsstart explizit auf `textContent` zurück,
     damit contenteditable nie gleichzeitig mit echten `<a>`-Kindknoten hantiert.
     Da Browser das native Navigieren von Links *innerhalb* eines contenteditable-
     Bereichs unterdrücken (unabhängig von `preventDefault()`), übernimmt ein eigener
     `click`-Handler auf `contentEl` das Öffnen bei gehaltener Cmd/Ctrl-Taste selbst:
     ein temporäres `<a>` wird außerhalb des contenteditable-Bereichs erzeugt, per
     `.click()` ausgelöst und wieder entfernt — derselbe Trick wie in `downloadFile()`,
     bewusst statt `window.open()` (das manche Browser für `file://`-Ziele restriktiver
     behandeln). Der `mousedown`-Handler verhindert bei Cmd/Ctrl+Klick auf einen Link
     zusätzlich den automatischen Browser-Fokus des contenteditable-Feldes
     (`e.preventDefault()`), da dieser sonst über den `focus`-Handler den Link vor dem
     `click`-Event auf Klartext zurücksetzen würde. Ein Klick ohne Modifier verhält
     sich wie bisher (Bearbeitung starten, `preventDefault()` unterbindet nur die
     Link-Navigation).
   - **Undo/Redo**: `snapshot()`/`pushUndo()`/`undo()` verwalten einen Stack aus
     JSON-Snapshots von `roots` (max. 50 Schritte); vor jeder strukturellen Änderung
     sowie vor abgeschlossenen Textbearbeitungen (Diff bei `blur`) wird ein Snapshot
     abgelegt. `redo()` nutzt einen zweiten Stack (`redoStack`): `undo()`/`redo()`
     schieben den jeweils aktuellen Zustand auf den anderen Stack, bevor sie ihren
     eigenen Stack poppen, damit man beliebig zwischen beiden hin- und herspringen
     kann. `pushUndo()` — und damit jede reguläre Aktion über `snapshot()` — leert
     `redoStack` sofort wieder, da die „Zukunft“ nach einer neuen Aktion ungültig wird.
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
   - **Suche**: `collectSearchMatches(query)` durchläuft `roots` vollständig (nicht
     nur den Display-Tree) und vergleicht je nach führendem `#` entweder `node.tags`
     oder `node.text` (Teilstring, case-insensitive); `searchMatchOrder` hält die
     Treffer-IDs in Baum-Reihenfolge, `searchMatchIds` dieselben als Set fürs
     Rendering (`.search-match`/`.search-current`-Klassen in `render()`).
     `revealNode()` klappt beim Sprung zu einem Treffer alle Vorfahren auf
     (`findParentAndIndex()` rückwärts bis zur Wurzel) und setzt
     `activeColorFilter`/`activeTagFilter` zurück, falls `nodeMatchesFilter()` den
     Treffer sonst ausblenden würde; `centerViewOn()` zentriert `view.x`/`view.y` auf
     die zuletzt gerenderte Position (`lastPositions`) bei unveränderter Zoomstufe.
     `Strg`/`Cmd`+`F` (globaler `keydown`-Handler) ruft `openSearchBar()` auf und
     ersetzt damit die native Browser-Suche (`e.preventDefault()`).

Es gibt bewusst keine externen Bibliotheken (kein D3, kein React) und keinen
Build-Schritt — die Datei funktioniert offline und lässt sich 1:1 kopieren oder
versionieren.

## Nutzung

1. `BrainDump.html` im Browser öffnen.
2. Über „＋ Neue Wurzel“ ein erstes Thema anlegen, Text eintippen. Enthält der Text
   eine `http(s)`- oder `file`-URL, wird sie außerhalb der Bearbeitung automatisch
   klickbar (Cmd/Ctrl+Klick öffnet).
3. Mit der schwebenden Node-Toolbar oder Tastatur (`Tab`/`Enter`) den Baum ausbauen.
   Über den ⧉-Schalter (oder `Strg`/`Cmd`+`D`) einen Knoten inkl. Teilbaum duplizieren,
   über `Strg`/`Cmd`+`Z`/`Strg`/`Cmd`+`Umschalt`+`Z` Aktionen rückgängig machen bzw.
   wiederholen.
4. Über „Speichern (JSON)“ den vollständigen Zustand sichern (inkl. Größen und Tags)
   oder über „MD“ im Export-Dropdown die Hierarchie inkl. Tags (nicht aber
   Größen/Farben) als portables Markdown exportieren.
5. Über „Import“ eine zuvor gesicherte `.json`- oder `.md`-Datei laden (Format wird
   automatisch erkannt), oder eine bestehende MindManager-Mindmap (`.mmap`)
   übernehmen (Text, Hierarchie und Ein-/Ausklapp-Status; Farben/Icons/Notizen/
   Querverknüpfungen gehen dabei verloren).
6. Über „Graphik“ im Export-Dropdown die aktuelle Ansicht als `.png` sichern, oder
   über „MindManager (Beta)“ die aktuelle (gefilterte) Ansicht als `.mmap`-Datei
   exportieren (unverifiziert, ob MindManager sie klaglos öffnet).
7. Rechtsklick auf ein Item weist eine Farbe zu; über „🎨 Farbfilter“ nach einer oder
   mehreren Farben (oder „Keine Farbe“) filtern. Über „📋 Legende einfügen“ einen
   Legende-Knoten mit einer Beschreibung je verwendeter Farbe erzeugen.
8. Über den ⤢/🔒-Schalter in der schwebenden Node-Toolbar die aktuelle Breite/Höhe
   eines Items fixieren bzw. wieder freigeben.
9. Über den `#`-Schalter in der schwebenden Node-Toolbar beliebig viele Tags an ein
   Item vergeben bzw. wieder entfernen. Über „🏷 Tags“ in der Toolbar nach einem oder
   mehreren Tags (oder „Ohne Tags“) filtern — kombinierbar mit dem Farbfilter.
10. Über „HTML“ im Export-Dropdown die aktuelle (gefilterte) Ansicht als eigenständige,
    rein lesende `.html`-Datei zum Weitergeben exportieren.
11. Über „Excel“ im Export-Dropdown die aktuelle (gefilterte) Ansicht als `.xlsx`-Datei mit
    Gliederungsebene, Gliederung, Item-Text, Farbe (Text + Zellhintergrund) und Tags
    exportieren.
12. Zur Orientierung auf großen Flächen: über den ⛶-Button in den Zoom-Controls den
    kompletten Baum ins Fenster einpassen, oder über die Mini-Karte unten links
    (Klick springt an die gewählte Stelle) navigieren. Über den 🗺-Schalter links
    neben dem Dark-/Hellmodus-Umschalter lässt sich die Mini-Karte bei Bedarf
    komplett ausblenden.
13. Über „Suche“ (oder `Strg`/`Cmd`+`F`) ein bestimmtes Item finden — Texteingabe
    durchsucht den Itemtext, eine Eingabe mit führendem `#` nur die Tags. Eingeklappte
    Vorfahren und ein aktiver Farb-/Tag-Filter, die einen Treffer verdecken würden,
    werden beim Sprung zum Treffer automatisch aufgeklappt bzw. zurückgesetzt.

## Bekannte Grenzen

- Das Layout ist ein einfacher, deterministischer Tidy-Tree-Algorithmus — für sehr
  große Mindmaps (mehrere hundert Knoten) kann die Darstellung unübersichtlich werden.
- Knotentext wird beim Export auf eine Zeile normalisiert (Zeilenumbrüche werden zu
  Leerzeichen), damit die Markdown-Listenstruktur gültig bleibt.
- Undo- und Redo-Stack sind je auf 50 Schritte begrenzt und werden beim Neuladen der
  Seite verworfen (nicht Teil des Autosave). Redo verfällt außerdem, sobald nach einem
  Rückgängig eine neue Aktion ausgeführt wird (kein verzweigender Redo-Verlauf).
- Manuell gesetzte Knotengrößen und Farben werden bewusst nicht in den Markdown-Export
  übernommen (der bildet nur Text/Hierarchie/Tags ab, bleibt dadurch portabel/lesbar) —
  für die vollständige, größen- und farberhaltende Sicherung den JSON-Export verwenden.
- Aktuell lässt sich pro Item nur eine Farbe vergeben (keine Mehrfachauswahl); Tags
  hingegen sind explizit als Mehrfachauswahl pro Item gedacht.
- Der Graphik-Export ist ein bewusst einfacher Nachbau (Boxen + Text + Kanten) und
  keine 1:1-Kopie des DOM; Knoten-Icons (Toggle-Dreieck, Kinderzahl-Badge) werden
  darin nicht mitgezeichnet.
- Die Link-Erkennung ist reine Anzeigelogik: Markdown-, PNG- und Excel-Export zeigen
  URLs weiterhin nur als Klartext (kein `[Text](URL)` bzw. keine echte Hyperlink-Zelle)
  — nur der Editor und der HTML-Export rendern sie klickbar. Die Heuristik zum
  Abtrennen von Satzzeichen am URL-Ende behandelt keine geklammerten URLs korrekt
  (z. B. `(https://de.wikipedia.org/wiki/Beispiel_(Begriffsklärung))`).
- Die Suche aktualisiert ihre Trefferliste nur beim Tippen im Suchfeld bzw. beim
  Sprung zum nächsten/vorherigen Treffer, nicht automatisch bei Textänderungen an
  anderer Stelle, während die Suchleiste geöffnet bleibt.
- Der `.mmap`-Import wurde nur mit dem ZIP-basierten MindManager-Format (ab ca.
  2013) getestet. Farben, Icons, Notizen, Callouts, Boundaries und
  Querverknüpfungen (Relationships) einzelner Themen gehen dabei verloren — nur
  Text, Hierarchie und Ein-/Ausklapp-Status werden übernommen. Ältere, unkomprimierte
  Einzeldatei-`.mmap`-Formate (vor MindManager 2013) werden vom ZIP-Reader nicht
  erkannt.
- Der `.mmap`-Export (Beta) wurde vom Nutzer erfolgreich in einer echten
  MindManager-Installation geöffnet (verifiziert mit einer eigenen,
  umfangreichen Mindmap, nach Nachbesserung um die von MindManager
  vorausgesetzten Formatierungs-/Metadaten-Blöcke — siehe Eigenschaften-Abschnitt
  oben). Mehrere Wurzelknoten werden beim Export zu einem synthetischen
  zentralen Thema zusammengefasst; Farben, Icons und Tags werden nicht ins
  MindManager-Format übertragen (nur Text, Hierarchie und Ein-/Ausklapp-Status).
  Da nur mit einer MindManager-Version getestet wurde, bleibt die
  Kennzeichnung „Beta“ vorerst bestehen.
