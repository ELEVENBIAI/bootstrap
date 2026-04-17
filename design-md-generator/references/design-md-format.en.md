# DESIGN.md format reference

A DESIGN.md has exactly 10 sections. Each section has a fixed purpose.
A standalone DESIGN-DARK.md with the same 10 sections is also generated.

## Section structure

### 1. Visual Theme & Atmosphere
- Describe the overall impression in 2–3 ARGUMENTATIVE paragraphs
- Explain the philosophy behind the design (not just "it's dark" — WHY)
- Frame each design decision as a deliberate choice, don't just list
- **Key characteristics** as bullet list: 6–10 concrete, measurable traits
- CSS values in parentheses: `(#171717)`, `(-2.4px)`, `(box-shadow: ...)`
- **Page Rhythm** as an explicit pattern: which section types alternate?
  e.g. "Dark Hero → Cream Body → Dark Interstitial → Cream Feature → Photo → Dark Footer"

### 2. Color Palette & Roles
- Group colors by category: Primary, Accent, Interactive, Neutral Scale, Surface & Overlay, Shadows
- EVERY color with: name, hex value, CSS variable (if any), usage purpose
- Format: `- **Name** (\`#hex\`): description of usage`
- Shadows as their own category with complete CSS values

### 3. Typography Rules
- Font family with fallbacks
- **Font substitute recommendations** for proprietary fonts: 3–4 open-source alternatives
  with notes on metric differences (e.g. "line-height ~0.95 instead of 1.00 needed")
- Hierarchy table with columns: Role | Font | Size | Weight | Line Height | Letter Spacing | Notes
- At least 10–15 hierarchy tiers (Display, Heading, Body, Caption, Mono, etc.)
- Principles: 3–4 rules that explain the typographic philosophy

### 4. Component Stylings
- Buttons (Primary, Secondary, Ghost/Outline, Pill/Badge) with exact CSS values
- Cards & Containers (background, border, radius, shadow)
- Inputs & Forms
- Navigation
- Image treatment
- Distinctive components (what makes this site unique?)

### 5. Layout Principles
- Spacing system (base unit, scale)
- Grid & Container (max-width, column system)
- Whitespace philosophy
- Border radius scale (from Micro to Full Pill)

### 6. Depth & Elevation
- Table with: Level | Treatment | Use
- From Flat (Level 0) to the highest elevation level
- Shadow philosophy
- Decorative depth (gradients, overlays)

### 7. Do's and Don'ts
- 8–10 Do's with concrete CSS values
- 8–10 Don'ts with reasons
- Every point must be specific and actionable (no platitudes)

### 8. Responsive Behavior
- Breakpoints table: Name | Width | Key changes
- Touch targets
- Collapsing strategy (what happens at smaller viewport)
- Image behavior

### 9. Agent Prompt Guide
- Quick color reference (5–7 most important colors)
- 4–5 example component prompts (copy-paste-ready instructions)
- Iteration guide (5–6 rules for consistent implementation)

### 10. Known Gaps
- Honestly document what could NOT be extracted
- Proprietary fonts unavailable (with substitute recommendations)
- Animations / transitions not visible in static CSS
- Number of pages analyzed and which patterns might be missing
- Missing status colors, icon systems, or other gaps
- Whether dark mode was natively extracted or derived

## Quality rules

- EVERY CSS value must come from the actual website — nothing made up
- Hex colors always lowercase with # (`#171717`, not `171717` or `#171717`)
- Shadows always as complete CSS values
- Font sizes in px AND rem
- No generic descriptions — always concrete values
- The intro text (section 1) must ARGUMENTATIVELY capture the PERSONALITY of the design
- Document page rhythm as a concrete pattern
- ALWAYS recommend font substitutes when proprietary fonts are in use
- Document known gaps honestly — transparency builds trust
- DESIGN-DARK.md must be usable standalone — no "see light version"
