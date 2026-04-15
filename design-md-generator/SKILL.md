---
name: design-md-generator
description: |
  Extrahiert das visuelle Design-System einer Website und/oder aus Design-Guides (PDF, DOCX)
  und generiert eine DESIGN.md im Google-Stitch-Format. Verwenden wenn der Nutzer ein
  Design-System extrahieren, eine DESIGN.md erstellen oder das visuelle Design dokumentieren
  moechte. Ausloeser: "erstelle eine DESIGN.md", "extrahiere das Design von...",
  "design system aus website", "DESIGN.md fuer...", "design md".
version: 1.3.0
---

# DESIGN.md Generator

Extrahiert das visuelle Design-System aus Websites und/oder Design-Dokumenten und generiert
eine DESIGN.md — ein plain-text Design-System-Dokument, das KI-Agenten lesen um konsistente
UI zu bauen.

Format-Referenz: Siehe [references/design-md-format.md](references/design-md-format.md)

## Workflow

### 0. Quellen erfragen (IMMER zuerst)

Bevor mit der Analyse begonnen wird, den Nutzer fragen:

**Pflichtfrage:** "Hast du einen Design Guide, Style Guide oder Brand Manual (PDF, DOCX, PPTX)
den ich mit einbeziehen soll? Damit wird die DESIGN.md deutlich vollstaendiger."

Moegliche Antworten und Reaktion:
- **"Ja, hier ist die Datei"** → Dokument analysieren (Schritt 1b), dann Website (Schritt 1a)
- **"Nein, nur die Website"** → Direkt zu Schritt 1a
- **"Ich habe ein CI-Profil JSON"** → JSON einlesen, dann Website analysieren

Drei Input-Quellen die der Skill unterstuetzt:

| Quelle | Was sie liefert | Tool |
|--------|----------------|------|
| Website-URL | Tatsaechlich verwendete CSS-Werte | defuddle / WebFetch |
| Style Guide (PDF/DOCX/PPTX) | Offizielle Regeln, Farbnamen, Do's/Don'ts | PDF-Skill / Read |
| CI-Profil JSON | Bereits extrahierte Farben/Fonts | ci-extraktor Output |

### 1a. Website laden und analysieren

Die URL per `WebFetch` oder `defuddle` laden. Dabei zwei Dinge parallel extrahieren:

**HTML/CSS-Rohdaten:**

```bash
npx defuddle parse <url> --json
```

Daraus extrahieren:
- Alle `<style>` und `<link rel="stylesheet">` Inhalte
- CSS Custom Properties (`--variable-name: value`)
- Inline-Styles auf prominenten Elementen
- Font-Family Deklarationen und `@font-face` Regeln
- Media Queries und Breakpoints

**Visuelle Analyse per Screenshot:**

Wenn Claude Preview verfuegbar ist, zusaetzlich:
- Screenshot der Website machen
- Visuellen Gesamteindruck erfassen (Atmosphaere, Farbstimmung, Whitespace)
- Besondere UI-Komponenten identifizieren

### 1b. Design-Dokumente analysieren (wenn vorhanden)

Aus PDF/DOCX/PPTX Style Guides extrahieren:

**Farben:**
- Offizielle Farbnamen und Hex-Werte
- Farbkategorien (Primaer, Sekundaer, Akzent, Neutral)
- Farbkombinationsregeln ("diese Farben duerfen nicht zusammen verwendet werden")
- Print- vs. Digital-Farbwerte (CMYK vs. RGB)

**Typografie:**
- Offizielle Schriftarten mit Lizenzen
- Schriftgroessen-Hierarchie mit Mindest-/Maximalgroessen
- Zeilenabstand-Regeln
- Anweisungen zu Schriftschnitten

**Layout & Spacing:**
- Raster-System, Spalten, Grundlinienraster
- Mindestabstaende, Schutzzonen
- Logo-Platzierung und -Groesse

**Regeln:**
- Offizielle Do's und Don'ts (direkt in Abschnitt 7 uebernehmen)
- Tonalitaet und Bildsprache
- Barrierefreiheits-Anforderungen

**Wichtig:** Informationen aus dem Style Guide haben Vorrang vor CSS-Analyse.
Der Style Guide definiert die Intention, die Website zeigt die Umsetzung.
Wo beide sich widersprechen, beide Werte dokumentieren mit Hinweis.

### 2. Design-Tokens zusammenfuehren

Aus allen Quellen (Website + Dokumente) systematisch zusammenfuehren:

**Farben:**
- Hex-Werte aus CSS + offizielle Namen aus Style Guide zusammenfuehren
- Nach Haeufigkeit und Kontext kategorisieren (Primary, Accent, Neutral, Surface)
- CSS Custom Properties den offiziellen Farbnamen zuordnen

**Typografie:**
- Font-Families mit Fallbacks (aus CSS) + offizielle Regeln (aus Guide)
- Alle font-size / font-weight / line-height / letter-spacing Kombinationen
- Typografie-Hierarchie ableiten (Display → Heading → Body → Caption)

**Spacing & Layout:**
- Padding/Margin-Werte aus CSS + Raster-System aus Guide
- Max-Width / Container-Breiten
- Grid-System erkennen (Flexbox, CSS Grid, Spalten)
- Border-Radius-Scale

**Shadows & Depth:**
- Alle box-shadow Werte sammeln
- Elevation-Levels ableiten

**Components:**
- Button-Styles (Primary, Secondary, Ghost)
- Card-Styles
- Input/Form-Styles
- Navigation-Patterns

### 3. DESIGN.md generieren

Die extrahierten Tokens in das 9-Abschnitte-Format uebersetzen:

1. **Visual Theme & Atmosphere** — Gesamteindruck, Philosophie, Key Characteristics
2. **Color Palette & Roles** — Farben mit Hex, CSS-Variable, offiziellem Namen, Verwendungszweck
3. **Typography Rules** — Font Family, Hierarchie-Tabelle, Principles
4. **Component Stylings** — Buttons, Cards, Inputs, Navigation, Distinctive Components
5. **Layout Principles** — Spacing, Grid, Whitespace, Border Radius Scale
6. **Depth & Elevation** — Shadow-Levels, Shadow-Philosophy
7. **Do's and Don'ts** — Aus Style Guide uebernommen + aus CSS abgeleitet
8. **Responsive Behavior** — Breakpoints, Collapsing Strategy, Touch Targets
9. **Agent Prompt Guide** — Quick Reference, Example Prompts, Iteration Guide

### 4. Preview-HTML generieren (IMMER mit erstellen)

Neben der DESIGN.md IMMER zwei HTML-Previews generieren. Die Templates unter
[references/preview-template.html](references/preview-template.html) und
[references/preview-dark-template.html](references/preview-dark-template.html) als Basis nehmen.

**a) `preview.html` (Light Mode):**

Das Template lesen und alle `{{PLATZHALTER}}` durch extrahierte Werte ersetzen:
- `{{SITE_NAME}}` → Name der Website
- `{{PRIMARY_COLOR}}`, `{{ACCENT_COLOR}}` etc. → Hex-Werte aus der Analyse
- `{{FONT_HEADING}}`, `{{FONT_BODY}}` → Font-Stacks
- `{{RADIUS_SM/MD/LG}}` → Border-Radius-Werte
- `{{SHADOW_CARD}}` → Card-Shadow CSS-Wert
- `{{DATE}}` → Aktuelles Datum

Dann die HTML-Kommentar-Bloecke (`<!-- TEMPLATE: ... -->`) durch echte Elemente ersetzen:
- **Farb-Swatches**: Einen `.swatch` pro extrahierter Farbe mit Name und Hex
- **Typo-Skala**: Eine `.type-row` pro Hierarchie-Stufe mit echtem Font-Style
- **Buttons**: Echte Buttons mit den extrahierten Button-Styles
- **Cards**: Standard-Card + Elevated-Card mit extrahierten Shadows
- **Shadows**: Ein `.shadow-box` pro Shadow-Level
- **Spacing**: Ein `.spacing-row` pro Spacing-Wert mit proportionalem Balken
- **Radii**: Ein `.radius-box` pro Border-Radius-Stufe

**b) `preview-dark.html` (Dark Mode):**

Gleiche Struktur wie Light, aber:
- Dark-Mode-Farben verwenden (aus der Website oder intelligent invertiert)
- `{{DARK_BG_COLOR}}` → Dunkelster Hintergrund (z.B. `#1a1a1a`)
- `{{DARK_TEXT_COLOR}}` → Heller Text (z.B. `#f0f0f0`)
- `{{DARK_SURFACE_COLOR}}` → Dunkle Oberflaeche (z.B. `#222`)
- Zusaetzlicher **Kontrast-Check**: Text-auf-Hintergrund-Kombinationen zeigen
- Shadows anpassen (auf Dunkel sind Shadows weniger sichtbar)

Wenn die Website keinen Dark Mode hat, intelligent ableiten:
- Background → dunkelster Neutral-Wert oder `#1a1a1a`
- Text → hellster Neutral-Wert oder `#f0f0f0`
- Surfaces → etwas heller als Background
- Akzentfarben bleiben gleich

### 5. Ausgabe — Speicherort erfragen (IMMER vor dem Schreiben)

Bevor die Dateien geschrieben werden, den Nutzer fragen:

```
Ich habe drei Dateien vorbereitet:
- DESIGN.md — Das Design-System-Dokument
- preview.html — Visueller Katalog (Light Mode)
- preview-dark.html — Visueller Katalog (Dark Mode)

Wo soll ich sie ablegen?
(1) Im aktuellen Verzeichnis
(2) In einem bestimmten Ordner — nenne mir den Pfad
(3) Auf dem Desktop zum schnellen Zugriff
```

Erst nach Antwort die Dateien in das gewaehlte Verzeichnis schreiben.

## Qualitaetsregeln

- JEDER CSS-Wert muss aus der tatsaechlichen Website stammen — NICHTS erfinden
- Informationen aus Style Guides als solche kennzeichnen wenn sie nicht im CSS vorkommen
- Hex-Farben immer lowercase mit # (`#171717`)
- Shadows als vollstaendige CSS-Werte angeben
- Font-Sizes in px UND rem
- Abschnitt 1 muss die PERSOENLICHKEIT des Designs einfangen, nicht nur technische Fakten
- Do's und Don'ts muessen spezifisch sein — keine generischen Plattitueden
- Agent Prompt Guide muss Copy-Paste-faehige Anweisungen enthalten
- Bei Widerspruch zwischen Website und Style Guide: beide Werte nennen, Guide hat Vorrang

## Tipps fuer bessere Ergebnisse

- Mehrere Unterseiten analysieren wenn die Startseite wenig UI-Vielfalt zeigt
- Bei SPAs: defuddle liefert den initialen HTML — fuer dynamische Inhalte Screenshots nutzen
- CSS Custom Properties sind Gold — sie zeigen das intendierte Design-System des Entwicklers
- Wenn eine Website ein bekanntes Framework nutzt (Tailwind, Material), das erwaehnen
- Style Guides liefern Do's/Don'ts die aus CSS allein nicht ableitbar sind — immer nachfragen
