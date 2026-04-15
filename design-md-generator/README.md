# DESIGN.md Generator

> Ein **Claude Code Skill**, der das visuelle Design-System einer Website und/oder aus Design-Guides (PDF, DOCX) extrahiert und als maschinenlesbare DESIGN.md im Google-Stitch-Format generiert — inklusive visueller HTML-Previews in Light und Dark Mode.

**Zweck:** KI-Agenten brauchen ein lesbares Design-System, um konsistente UI zu bauen. Dieser Skill extrahiert die tatsaechlich verwendeten CSS-Werte einer Website, fuehrt sie mit offiziellen Style-Guide-Regeln zusammen und generiert ein 9-Abschnitte-Dokument, das jeder Agent direkt als Referenz nutzen kann.

---

## Warum dieser Skill?

| Problem | Loesung |
|---------|---------|
| KI-Agenten erfinden Farben und Fonts statt echte CSS-Werte zu verwenden | DESIGN.md liefert ausschliesslich extrahierte Werte — nichts wird erfunden |
| Style Guides (PDF) und tatsaechliches CSS weichen oft voneinander ab | Skill fuehrt beide Quellen zusammen und dokumentiert Abweichungen |
| Kein standardisiertes Format fuer KI-lesbares Design | Google-Stitch-Format mit 9 festen Abschnitten — vorhersagbar und maschinenlesbar |
| Dark Mode wird oft vergessen | Automatische Dark-Mode-Ableitung und separater visueller Katalog |

---

## Funktionsumfang

- **Website-Analyse:** Extrahiert CSS Custom Properties, Font-Deklarationen, Farben, Shadows, Spacing, Media Queries per `defuddle` oder `WebFetch`
- **Style-Guide-Analyse:** Liest offizielle Regeln aus PDF/DOCX/PPTX — Farbnamen, Do's/Don'ts, Typografie-Hierarchie, Raster-System
- **CI-Profil-Import:** Uebernimmt bereits extrahierte Farben/Fonts aus dem ci-extraktor Skill
- **Token-Zusammenfuehrung:** Fuehrt Website-CSS und Style-Guide-Regeln systematisch zusammen, mit Vorrang fuer den Style Guide
- **DESIGN.md Generierung:** 9 Abschnitte nach Google-Stitch-Format (Visual Theme, Colors, Typography, Components, Layout, Depth, Do's/Don'ts, Responsive, Agent Prompt Guide)
- **HTML-Previews:** Generiert `preview.html` (Light) und `preview-dark.html` (Dark) als visuellen Design-Katalog
- **Screenshot-Analyse:** Optionale visuelle Analyse per Claude Preview fuer Atmosphaere und Gesamteindruck
- **Brand-Dokumente:** Optionale Einbindung von Brand Story, Archetypen, Tonalitaets-Richtlinien fuer reichere Design-Dokumentation
- **Style Guide PDF:** Optionaler Export als professionelles Style-Guide-Dokument (PPTX) in den Farben und Schriften der Marke — kein Template, individuell generiert

---

## Nutzung

### Trigger-Befehle

```
/design-md-generator
```

Oder natuerliche Sprache:
- "erstelle eine DESIGN.md"
- "extrahiere das Design von [URL]"
- "design system aus website"
- "DESIGN.md fuer [Kundenname]"

### Typischer Ablauf

1. **Skill fragt nach Design-Quellen:** Website-URL und optional Style Guide (PDF/DOCX)
2. **Skill fragt nach Brand-Dokumenten:** Brand Story, Archetypen, Tonalitaet (optional)
3. **Analyse laeuft:** Website-CSS wird extrahiert, Dokumente werden gelesen
4. **Tokens werden zusammengefuehrt:** Farben, Fonts, Spacing, Shadows, Components
5. **Speicherort wird erfragt:** Aktuelles Verzeichnis, eigener Pfad oder Desktop
6. **Drei Kerndateien werden erstellt:**
   - `DESIGN.md` — Das Design-System-Dokument
   - `preview.html` — Visueller Katalog (Light Mode)
   - `preview-dark.html` — Visueller Katalog (Dark Mode)
7. **Optional: Style Guide als PDF** — Professionelles Markendokument in den Farben und Schriften der Marke (via PPTX-Skill)

### Beispiel 1: Nur Website

```
> /design-md-generator
> "Nein, nur die Website"
> URL: https://example.com
```

Ergebnis: DESIGN.md mit allen extrahierten CSS-Werten, automatisch abgeleitetem Dark Mode.

### Beispiel 2: Website + Style Guide

```
> /design-md-generator
> "Ja, hier ist die Datei" → style-guide.pdf
> URL: https://example.com
```

Ergebnis: DESIGN.md mit zusammengefuehrten Werten aus beiden Quellen, offizielle Farbnamen und Do's/Don'ts aus dem Guide.

---

## Die 9 Abschnitte der DESIGN.md

| # | Abschnitt | Inhalt |
|---|-----------|--------|
| 1 | Visual Theme & Atmosphere | Gesamteindruck, Design-Philosophie, Key Characteristics |
| 2 | Color Palette & Roles | Farben mit Hex, CSS-Variable, Name, Verwendungszweck |
| 3 | Typography Rules | Font Family, Hierarchie-Tabelle, Typografie-Prinzipien |
| 4 | Component Stylings | Buttons, Cards, Inputs, Navigation, Distinctive Components |
| 5 | Layout Principles | Spacing, Grid, Whitespace, Border Radius Scale |
| 6 | Depth & Elevation | Shadow-Levels, Shadow-Philosophy, Decorative Depth |
| 7 | Do's and Don'ts | Konkrete Regeln aus Style Guide + CSS-Analyse |
| 8 | Responsive Behavior | Breakpoints, Collapsing Strategy, Touch Targets |
| 9 | Agent Prompt Guide | Quick Reference, Example Prompts, Iteration Guide |

---

## Qualitaetsregeln

- **Keine erfundenen Werte** — Jeder CSS-Wert stammt aus der tatsaechlichen Website
- **Style Guide hat Vorrang** — Bei Widerspruch zwischen CSS und Guide werden beide dokumentiert
- **Hex lowercase** — Immer `#171717`, nie `171717` oder `#FFFFFF`
- **Vollstaendige Shadows** — Als komplette CSS-Werte, nicht nur Farbwerte
- **Duale Font-Sizes** — Immer px UND rem
- **Spezifische Do's/Don'ts** — Keine generischen Plattitueden, sondern umsetzbare Regeln

---

## Dateistruktur

```
design-md-generator/
├── README.md                              ← Diese Datei
├── SKILL.md                               ← Skill-Definition mit Workflow
└── references/
    ├── design-md-format.md                ← Format-Referenz (9 Abschnitte)
    ├── preview-template.html              ← HTML-Template (Light Mode)
    └── preview-dark-template.html         ← HTML-Template (Dark Mode)
```

---

## Hintergrund

Das DESIGN.md-Format basiert auf dem Google-Stitch-Ansatz: ein plain-text Design-System-Dokument, das KI-Agenten als Referenz lesen koennen, um konsistente UI zu bauen. Der Skill wurde entwickelt, weil bestehende Design-Extraktions-Tools entweder nur Farben liefern (ohne Kontext) oder nur fuer menschliche Leser formatiert sind (ohne Maschinenlesbarkeit).

Die Kombination aus Website-Analyse und Style-Guide-Analyse schliesst eine Luecke: Websites zeigen was implementiert ist, Style Guides zeigen was beabsichtigt war. Beides zusammen ergibt das vollstaendige Bild.

---

**Version:** 1.4.0 | **Skill-Name:** `design-md-generator`
