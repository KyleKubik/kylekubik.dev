# Kyle Kubik — Portfolio Design System

**Handoff brief for Claude Design.** This document defines the visual language for kylekubik.dev. Apply it consistently across all page types (home, work index, project detail, writing index, blog post, about, résumé, contact). Build the full system from these tokens — don't reinterpret the direction.

---

## 1. Concept & Feeling

The site belongs to a multidisciplinary engineer: embedded firmware, PCB and mechanical design, systems engineering, and engineering leadership. Career spans race cars (Formula SAE), marine radar, IoT hardware, and nuclear robotics.

**Three adjectives drive every decision: technical, effective, interesting.** The visual language is a **mechanical-drawing / blueprint** aesthetic — the world of CAD, schematics, and drafting sheets — translated into a clean, modern web page.

**Critical principle: the style is a supporting layer, never the star.** Content leads on every screen. The drafting language shows up in small, deliberate details (a title-block footer, dimension-line dividers, registration ticks, a faint masthead grid) — not as loud wallpaper or heavy chrome. When in doubt, make the drafting element quieter and let the work speak.

**Reference resonance (subtle, not literal):** the blue + orange palette echoes Auburn University (the engineer's alma mater). Lean into it as warmth, never as branding.

**What to avoid:** dark mode; terminal/hacker aesthetics; SaaS-marketing gradients, glassmorphism, drop shadows; heavy or gratuitous animation; anything that would look at home on a startup landing page. If a choice would look good in a lab notebook, a schematic, or a well-set engineering document, it's right. If it looks like a marketing site, it's wrong.

---

## 2. Color Palette

Light mode only. Cool off-white "paper," blueprint-blue ink, one warm orange accent used sparingly for signal.

| Token | Hex | Role |
|---|---|---|
| `--paper` | `#f1f1ee` | Page background. Cool off-white — reads as drafting paper, NOT cream/yellow and NOT screen-white. |
| `--paper-hi` | `#f8f8f6` | Raised surfaces (title-block panel, any inset cells). |
| `--ink` | `#1f4e79` | Blueprint blue. Primary accent: kickers, section marks, links, emphasized words, drafting frame borders, registration ticks. |
| `--ink-soft` | `#3a6899` | Secondary blue for small labels inside framed elements. |
| `--text` | `#2c2f31` | Body text. |
| `--text-strong` | `#1a1f24` | Headings / high-emphasis text (near-black, slightly cool). |
| `--dim` | `#8c8b82` | Muted text: tags, captions, metadata, mono labels. |
| `--line` | `#e0dccf` | Hairline rules and dividers (warm-gray, 1px). |
| `--accent` | `#c2622a` | Drafting orange / Auburn orange. Used *sparingly* for genuine signal only — key metrics, status, the section-mark glyph, occasional single annotation. Never for large fills or decoration. |

**Accent discipline:** orange should feel like a drafter's red-pencil markup — rare and meaningful. If more than ~5% of a screen is orange, it's overused.

---

## 3. Typography

Two families only. A clean grotesque sans for reading, a monospace for technical/label register.

**Body & headings — Inter**
- H1: 46px, weight 600, letter-spacing −1px, line-height 1.05, color `--text-strong`
- H3 (project titles): 21px, weight 600, color `--text-strong`
- Lede/intro: 19px, weight 400, line-height 1.5, color `--text`; emphasized words in weight 600 `--ink`
- Body: 16px, line-height 1.6, color `--text`
- Small/caption body: 15px

**Labels, data, metadata — IBM Plex Mono**
- Kicker / eyebrow: 12px, weight 400, letter-spacing 1.5px, uppercase, color `--ink`
- Section label: 12px, letter-spacing 1.5px, uppercase, color `--dim`
- Tags / tech stack: 12px, color `--dim`
- Metrics: 12px, color `--accent`
- Title-block field labels: 9px, letter-spacing 1px, uppercase, color `--ink-soft`

**Rule:** monospace is for the *engineering-document register* — labels, specs, part-number-like tags, metadata, metrics. It never sets long-form reading text. Inter carries all prose.

---

## 4. Layout

- **Single reading column**, max-width ~820px, centered, 44px side padding. This is a document, not a dashboard — resist multi-column or sidebar layouts.
- Generous vertical rhythm; let whitespace do work.
- Precise alignment and hairline rules over cards-with-shadows. No drop shadows anywhere.
- Corners: sharp or minimally rounded (≤2px). No soft/bubbly cards.
- Fully responsive down to mobile; collapse the title-block footer to stacked cells on narrow screens.

**Home page structure (reference):**
```
┌─────────────────────────────────────┐
│  ⌐              [faint grid]      ¬  │  ← masthead: registration ticks at corners,
│  MULTIDISCIPLINARY ENGINEER         │     faint blue grid faded toward edges
│  Kyle Kubik                         │     (grid ONLY here, not whole page)
│  [lede — one emphasized clause]     │
├─────────────────────────────────────┤
│  ⌖ SELECTED WORK ─────────────────  │  ← dimension-line divider
│  ProteusCore          100s vessels  │  ← project row: title+desc+tags left,
│  APEX Pro             2 hw gens     │     orange metric right-aligned
│  Auburn FSAE          1st Nebraska  │
│  Nuclear Robotics     published     │
├─────────────────────────────────────┤
│ ┌──────┬──────┬──────┬──────┐       │  ← title-block footer (see signature)
│ │ NAME │ ROLE │ LOC. │ SITE │       │
│ └──────┴──────┴──────┴──────┘       │
└─────────────────────────────────────┘
```

---

## 5. Signature Elements

These are the recurring drafting details that give the system its identity. Use them consistently; don't invent new ones per page.

**A. Title-block footer** — the primary signature. A bordered frame (1.5px `--ink`) divided into cells, echoing an engineering drawing's corner title block. **Use plain web labels, never drafting metaphors** — "Name / Role / Location / Site," NOT "Drawn by / Scale / Sheet." Visual reference only, honest text. Appears at the bottom of pages as a metadata summary. On project pages, cells can carry project metadata (e.g. Project / Stack / Status / Year).

**B. Dimension-line section dividers** — section labels lead with a target-mark glyph (⌖) in `--accent`, followed by the uppercase mono label, followed by a thin ruled line filling the remaining width. Replaces plain `<hr>` and plain headers.

**C. Registration ticks** — small L-shaped corner marks (⌐ ¬) in faded `--ink` at the corners of the masthead, like drafting alignment marks. Subtle, ~14px.

**D. Faint masthead grid** — a blue grid (~26px cells) behind the hero/masthead ONLY, heavily faded toward the edges and bottom so it reads as the margin of a drafting sheet, not wallpaper. Never apply to the whole page. Keep it barely-there.

**E. Orange metrics** — each project's headline number/outcome (e.g. "100s of vessels in production," "1st overall Nebraska 2016") set in mono `--accent`, right-aligned. This is the main place the accent earns its keep.

---

## 6. Motion

Minimal and functional. Subtle hover states only (e.g. project title shifts to `--ink` on hover). Respect `prefers-reduced-motion`. No scroll-jacking, parallax, or ambient animation — restraint is on-brand here.

---

## 7. Page Types to Build

Apply the system across all of these, reusing the signature elements:
1. **Home** — as specified above
2. **Work index** — full list of projects/roles; same row treatment as home, all entries
3. **Project / experience detail** — one flexible template with a "kind" distinction (build vs. leadership role); Brief → Contributions → Results content shape; title-block carries project metadata; heavy use of real technical imagery (CAD, PCB, schematics, oscilloscope traces)
4. **Writing index** — blog listing
5. **Blog post** — long-form reading; Inter body, mono only for code and captions
6. **About** — career narrative + leadership philosophy
7. **Résumé** — clean, linkable, one-click PDF download
8. **Contact** — form + honeypot (no exposed email); persistent contact also lives in footer
9. **404** — an on-brand "off the drawing" empty state, directional not moody

---

## 8. Voice / Copy

Plain, specific, active voice. Sentence case. No filler, no cleverness for its own sake. Specificity is the personality: "led an 18-engineer aero program to Auburn's first overall win" beats "passionate leader." Let real numbers and real artifacts carry the impression. Errors and empty states give direction, not mood.

---

## 9. Reference Implementation

A working HTML reference of the home masthead, work list, dimension dividers, and title-block footer accompanies this document (`portfolio-blueprint-v3.html`). Treat it as the source of truth for spacing, tone, and the signature elements; extend the same language to the remaining page types.
